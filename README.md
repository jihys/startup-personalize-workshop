# Startup Amazon Personalize workshop Guide

Amazon Personalize는 추천 / 개인화 모델을 빠르고 효과적으로 구축하고 확장 할 수있는 기계 학습 서비스입니다. 아래 내용은 주어진 사용 사례에 대한 초기 모델을 구축하는 데 도움이되며 데이터가 아직 Amazon Personalize에서 사용하기에 이상적인 형식이 아닐 수도 있다고 가정합니다.

이 리포지토리는 서비스에 대한 기본 지식이 있다고 가정하고 아직 수행하지 않은 경우 아래의 시작하기 자료를 사용하는 것을 권장합니다.



## Introduction to Amazon Personalize

아래 링크에는 추가적으로 Amazon Personalize에 유용한 가이드가 정리 되어 있습니다.  

- [Product Page](https://aws.amazon.com/personalize/)
- [GitHub Sample Notebooks](https://github.com/aws-samples/amazon-personalize-samples)
- [Product Docs](https://docs.aws.amazon.com/personalize/latest/dg/what-is-personalize.html)

## 

## Goals

이번 워크샾을 통해 프로그래밍 방식으로 Amazon Personalize를 활용하여 개인화 추천 결과를 구축하는 방법에 대해 알아봅니다.  이번 워크샾에서는 다읍과 같은 내용을 배우게 됩니다.

##### 데이터 준비하기

- Dataset을 Amazon Personalize에 매핑해 봅니다.
- 향후 Validation 및 데이터 분석을 위해서 데이터셋을 Training용과 Test용으로 분리 합니다. 

##### 솔루션 생성하기

- Amazon Personalize에 제공하는 다양한 알고리즘을 통해 솔루션(사용자 추천 모델)을 생성해 봅니다.
- 사용자 기반 추천/아이템 기반 추천/Cold-start 아이템 추천의 결과를 확인해 봅니다.

##### 캠페인 생성 

- 생성된 모델을 활용하여 개인화 결과를 얻어 봅니다.
- Real-time서빙과 Batch recommendation 차이에 대해 알아보고 적합한 적용 방식에 대해 알아봅니다. 

##### 성능 분석 

- 추천 모델 성능 지표에 대한 메트릭에 대해 알아 봅니다.



## 순서

1. 작업 환경 구성하기  [아래 참고]
2. Validating and importing user-item-interaction data - `01_Validating_and_Importing_User_Item_Interaction_Data.ipynb`
3. Validating and importing item-metadata [in development] - `Validating_and_Importing_Item_Metadata.ipynb`
4. Creating and evaluating your first solutions - `02_Creating_and_Evaluating_Solutions.ipynb`
5. Deploying and interacting with campaigns - `03_Deploying_Campaigns_and_Interacting.ipynb`
6. Cleaning up the resources on your AWS account - `04_Clean_Up_Resources.ipynb`



## 작업 환경 구성하기 

CloudFormation Template 을 활용하여 작업 환경을 구성합니다.

1. 인터넷 브라우저(Chrome, Firefox 권장)를 하나 더 연뒤에 AWS Account로 로그인 합니다.
2. 브라우저에 새로운 Tab 을 생성한 뒤 아래 링크를 클릭하여 CloudFormation을 통해 환경을 구축합니다. 



[![Launch Stack](https://camo.githubusercontent.com/210bb3bfeebe0dd2b4db57ef83837273e1a51891/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f636c6f7564666f726d6174696f6e2d6578616d706c65732f636c6f7564666f726d6174696f6e2d6c61756e63682d737461636b2e706e67)](https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=PersonalizePOC&templateURL=https://jihys-personalize-ap-northeast-2.s3.ap-northeast-2.amazonaws.com/PersonalizePOC_v1.yaml)

추가 궁금한 사항은 아래 스크린 샷의 가이드를 따라 합니다. 

### Cloud Formation Wizard

아래의 "Next" 버튼을 눌러서 CloudFormation 설정 작업을 시작 합니다.

[![StackWizard](https://github.com/aws-samples/amazon-personalize-samples/raw/master/workshops/POC_in_a_box/static/imgs/img1.png)](https://github.com/aws-samples/amazon-personalize-samples/blob/master/workshops/POC_in_a_box/static/imgs/img1.png)



다음 설정을 

고유한 S3 버킷 이름을 지정해야 합니다. 간단하게 영어로 본인의 이름을 **영어소문자** 로 더해주세요. (예: personalizeddemo-jihys) 이후에 'Next'버튼을 클릭 해주세요 



다음과 같은 설정을 해 줍니다.  

1. Stack name을 원하는 대로 변경해 변경해 주세요.  `i.e. PersonalizePOCDemo`
2. 노트북 이름을 변경해 주세요. (Optional)
3. SageMaker Notebook instance에 EBS 볼륨을 더 할당 하고 싶으신 경우(데이터 셋이 클경우) 더 큰 사이즈로 변경하세요.기본은 10GB 입니다. 

모든 작업이 끝나면 `Next` 버튼을 클릭합니다.

[![StackWizard2](https://github.com/aws-samples/amazon-personalize-samples/raw/master/workshops/POC_in_a_box/static/imgs/img2.png)](https://github.com/aws-samples/amazon-personalize-samples/blob/master/workshops/POC_in_a_box/static/imgs/img2.png)

해당 페이지에서 밑에까지 스크롤 다운 한뒤에  `Next` 버튼을 클릭합니다. 해당 페이지에서는 모든 default설정은 POC를 하는데 충분합니다. 필요시에만 변경 하도록 합니다. 

[![StackWizard3](https://github.com/aws-samples/amazon-personalize-samples/raw/master/workshops/POC_in_a_box/static/imgs/img3.png)](https://github.com/aws-samples/amazon-personalize-samples/blob/master/workshops/POC_in_a_box/static/imgs/img3.png)

다시 아래까지 스크롤 한 뒤, CloudFormation Template이 IAM 자원을 생성할 수 있는 권한을 줄수 있도록 box를 체크합니다. 그리고  `Create Stack` 을 클릭합니다.

[![StackWizard4](https://github.com/aws-samples/amazon-personalize-samples/raw/master/workshops/POC_in_a_box/static/imgs/img4.png)](https://github.com/aws-samples/amazon-personalize-samples/blob/master/workshops/POC_in_a_box/static/imgs/img4.png)



몇 분뒤 CloudFormation은 새로운 자원을 생성하게 됩니다. 프로지버닝 단계에서는 다음과 같은 화면이 보일 것 입니다.

[![StackWizard5](https://github.com/aws-samples/amazon-personalize-samples/raw/master/workshops/POC_in_a_box/static/imgs/img5.png)](https://github.com/aws-samples/amazon-personalize-samples/blob/master/workshops/POC_in_a_box/static/imgs/img5.png)

모든 작업이 완성이 된 후에는 Status가 글씨로 아래와 같이 "CREATE_COMPLETE" 로 보이게 됩니다. 



[![StackWizard5](https://github.com/aws-samples/amazon-personalize-samples/raw/master/workshops/POC_in_a_box/static/imgs/img6.png)](https://github.com/aws-samples/amazon-personalize-samples/blob/master/workshops/POC_in_a_box/static/imgs/img6.png)

이제 AWS Management 콘솔 페이지에서  `Services` 클릭 후  `SageMaker`서비스를 조회한 하여 클릭한 후 SageMaker메뉴로 이동 합니다.

[![StackWizard5](https://github.com/aws-samples/amazon-personalize-samples/raw/master/workshops/POC_in_a_box/static/imgs/img7.png)](https://github.com/aws-samples/amazon-personalize-samples/blob/master/workshops/POC_in_a_box/static/imgs/img7.png)

SageMaker콘솔에서 Notebook에서 본인이 방금 생성한 노트북을 찾아 클릭합니다.



[![StackWizard5](https://github.com/aws-samples/amazon-personalize-samples/raw/master/workshops/POC_in_a_box/static/imgs/img8.png)](https://github.com/aws-samples/amazon-personalize-samples/blob/master/workshops/POC_in_a_box/static/imgs/img8.png)

선택한 SageMaker notebook 에서  `Open JupyterLab` 을 클릭합니다. 

[![StackWizard5](https://github.com/aws-samples/amazon-personalize-samples/raw/master/workshops/POC_in_a_box/static/imgs/img9.png)](https://github.com/aws-samples/amazon-personalize-samples/blob/master/workshops/POC_in_a_box/static/imgs/img9.png)



이는 workshop을 위해 필요한 Jupyter 환경을 오픈합니다. Jupyter는 데이터 사이언스를 위한 IDE환경입니다. `PersonalizeWorkshop` 이는 자동으로 폴더를 오픈 This will open the Jupyter environment for your POC; think of it as a web based data science IDE if you are not familiar with it. It should  automatically open the `PersonalizePOC` folder for you, if  not, just click on the folder icon in the browser on the left side of  the screen and follow the documentation below to get started with your  POC!

## 

## Validating and Importing User-Item-Interaction Data

The core data for every algorithm supported in Amazon Personalize is  user-item-interaction data; this notebook will guide you through the  process of identifying this data, then formatting it for the service,  defining your schema, and lastly importing it.

Open `01_Validating_and_Importing_User_Item_Interaction_Data.ipynb` and follow along there.

Once you have completed this, you can move on to creating and evaluating your first solutions.

## 

## Validating and Importing Item Metadata [in development]

Amazon Personalize has several algorithms that can give you a results with no metadata. However, the HRNN-Metadata algorithm might be an  interesting resource to deploy, depending your dataset. In case it is,  there is an optional item metadata notebook below. It is similar to the  process for users, and the only algorithm that supports either data type is HRNN-Metadata.

Open `Validating_and_Importing_Item_Metadata.ipynb` and follow along there to prepare the dataset for the POC/Amazon Personalize.

## 

## Creating and Evaluating Your First Solutions

In Amazon Personalize there is a concept of a solution, which is a  trained model based on the data that you've provided to the service. All models are private and no data sharing occurs between accounts or even  between dataset groups. This notebook will guide you through the process of training models; aka building a solution for:

- HRNN
- SIMS
- Personalized-Ranking

Something you may notice is that each of these algorithms or recipes  solves a critically different problem. The goal is to show you how to  build things that address a host of problems from a relatively simple  dataset.

Open `02_Creating_and_Evaluating_Solutions.ipynb` and follow along to build these solutions and see their results.

### 

### Deploying Your Campaigns and Interacting with Personalize

Once you have a series of trained solutions the next step is to deploy them. This is done inside `03_Deploying_Campaigns_and_Interacting.ipynb`. Here you will learn:

1. Deployment and capacity planning
2. How to interact with a deployed solution (various approaches)
3. Real-time interactions
4. Batch exporting



### Next Steps

Following these notebooks should have left you with a series of  working models for your customer. From here, you will look to leverage  how the customer accomplishes AB testing today against their goals  (coversions, clicks, etc) and then start sending traffic to these models and monitoring those metrics. Over time this should build confidence  and will be your path to production at scale.

More content on AB testing coming soon as well.

### 

### Cleaning Up

워크샾이 끝난 후에 모든 자원을 삭제 하기 위해서는 `04_Clean_Up_Resources.ipynb` 노트북 가이드를 따라 합니다. 여기서는 배포된 모든 Personalize자원을 삭제 하는 방법에 대해 알려줍니다.