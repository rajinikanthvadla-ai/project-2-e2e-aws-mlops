# 🏗️ MLOps E2E Architecture Flow Diagram

**Designed & Validated by: Rajinikanth Vadla** ⭐⭐⭐⭐⭐

## 📋 Complete Data & ML Pipeline Flow

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           🌟 MLOPS END-TO-END PIPELINE                          │
│                     Tested by Rajinikanth Vadla - 100% Success                 │
└─────────────────────────────────────────────────────────────────────────────────┘

📊 DATA SOURCE (Abalone Dataset from UCI)
    │
    ▼
┌─────────────────────┐    ⚡ LAMBDA TRIGGER    ┌──────────────────────┐
│  🪣 S3 DATA BUCKET  │ ────────────────────► │  📋 DATA MANIFEST   │
│  Raw CSV Files     │                       │  JSON Configuration  │
└─────────────────────┘                       └──────────────────────┘
                                                        │
                                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                            🔄 AWS CODEPIPELINE                                  │
│                         (Triggered by data changes)                            │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────┐│
│  │   📥 SOURCE     │  │   🧪 CI STAGE   │  │ 🤖 ML PIPELINE  │  │ 🚀 DEPLOY   ││
│  │                 │  │                 │  │     STAGE       │  │   STAGE     ││
│  │ • GitHub/       │  │ • Unit Tests    │  │                 │  │             ││
│  │   CodeCommit    │  │ • Code Lint     │  │ • Preprocessing │  │ • Manual    ││
│  │ • Auto trigger  │  │ • Build CDK     │  │ • Training      │  │   Approval  ││
│  │                 │  │                 │  │ • Evaluation    │  │ • Deploy    ││
│  └─────────────────┘  └─────────────────┘  └─────────────────┘  └─────────────┘│
│           │                     │                     │                     │  │
└───────────┼─────────────────────┼─────────────────────┼─────────────────────┼──┘
            │                     │                     │                     │
            ▼                     ▼                     ▼                     ▼

┌─────────────────────────────────────────────────────────────────────────────────┐
│                         🤖 SAGEMAKER ML PIPELINE                                │
│                              (mlops-e2e)                                       │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────────┤
│  │  STEP 1: ⚙️ PREPROCESSING                                                   │
│  │  • SKLearn Processor (ml.t3.medium)                                        │
│  │  • Feature engineering & data cleaning                                     │
│  │  • Split: Train/Validation/Test sets                                       │
│  │  • Output: Processed datasets + preprocessor model                         │
│  └─────────────────────────────────────────────────────────────────────────────│
│           │
│           ▼
│  ┌─────────────────────────────────────────────────────────────────────────────┤
│  │  STEP 2: 🏋️ MODEL TRAINING                                                 │
│  │  • XGBoost Estimator (ml.m4.xlarge)                                        │
│  │  • Hyperparameters: max_depth=5, eta=0.2, gamma=4                          │
│  │  • Training: 50 rounds with early stopping                                 │
│  │  • Output: Trained model artifacts (model.tar.gz)                          │
│  └─────────────────────────────────────────────────────────────────────────────│
│           │
│           ▼
│  ┌─────────────────────────────────────────────────────────────────────────────┤
│  │  STEP 3: 📊 MODEL EVALUATION                                               │
│  │  • Test dataset evaluation                                                 │
│  │  • Metrics: MSE, MAE, R²                                                   │
│  │  • Quality gate: MSE ≤ 6.0 (configurable)                                 │
│  │  • Output: evaluation.json with performance metrics                        │
│  └─────────────────────────────────────────────────────────────────────────────│
│           │
│           ▼
│  ┌─────────────────────────────────────────────────────────────────────────────┤
│  │  STEP 4: 🔍 CONDITIONAL REGISTRATION                                       │
│  │  • Quality check: IF MSE ≤ 6.0 THEN register model                        │
│  │  • Pipeline model: SKLearn preprocessor + XGBoost model                    │
│  │  • Model package group: AbaloneModelPackageGroup                           │
│  │  • Output: Registered model ready for deployment                           │
│  └─────────────────────────────────────────────────────────────────────────────│
└─────────────────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                       🎯 MODEL DEPLOYMENT & SERVING                             │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────┐     ┌─────────────────────┐     ┌─────────────────────┐│
│  │  ☁️ CDK DEPLOYMENT  │ ──► │ 🎯 SAGEMAKER        │ ──► │ 🌐 API GATEWAY     ││
│  │                     │     │    ENDPOINT         │     │                     ││
│  │  • Infrastructure   │     │ • Real-time         │     │ • REST API          ││
│  │    as Code          │     │   inference         │     │ • CORS enabled      ││
│  │  • Automated        │     │ • Auto-scaling      │     │ • Request/Response  ││
│  │    provisioning     │     │ • Multi-AZ          │     │   transformation    ││
│  │                     │     │ • Multi-AZ          │     │                     ││
│  └─────────────────────┘     └─────────────────────┘     └─────────────────────┘│
└─────────────────────────────────────────────────────────────────────────────────┘
                                          │
                                          ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         💻 ONLINE CONSUMER APPLICATION                          │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌─────────────────────┐     ┌─────────────────────┐     ┌─────────────────────┐│
│  │ 🌐 CLOUDFRONT CDN   │ ──► │ 💻 REACT WEB APP    │ ──► │ 👤 END USER        ││
│  │                     │     │                     │     │                     ││
│  │ • Global delivery   │     │ • Modern UI/UX      │     │ • Input: Length,    ││
│  │ • HTTPS enabled     │     │ • TypeScript        │     │   Diameter, Height, ││
│  │ • Static hosting    │     │ • Form validation   │     │   Weights, etc.     ││
│  │                     │     │ • Real-time         │     │ • Output: Predicted ││
│  │                     │     │   predictions       │     │   Abalone Age       ││
│  └─────────────────────┘     └─────────────────────┘     └─────────────────────┘│
└─────────────────────────────────────────────────────────────────────────────────┘

```

## 🔧 Technical Components

### Core AWS Services
- **🪣 Amazon S3**: Data lake for raw data, processed datasets, model artifacts
- **⚡ AWS Lambda**: Event-driven data processing and monitoring
- **🔄 AWS CodePipeline**: CI/CD orchestration and workflow management
- **🧪 AWS CodeBuild**: Automated testing and building
- **🤖 Amazon SageMaker**: End-to-end ML lifecycle management
- **🎯 SageMaker Endpoints**: Real-time model inference
- **🌐 Amazon API Gateway**: RESTful API management
- **💻 Amazon CloudFront**: Global content delivery network
- **☁️ AWS CDK**: Infrastructure as Code framework

### ML Pipeline Details
- **Framework**: XGBoost for regression (Abalone age prediction)
- **Data Processing**: Scikit-learn for feature engineering
- **Instance Types**: ml.t3.medium (processing), ml.m4.xlarge (training)
- **Model Evaluation**: MSE, MAE, R² metrics with quality gates
- **Deployment**: Pipeline model (preprocessing + inference)
- **Scalability**: Auto-scaling endpoints with multi-AZ deployment

### Development Stack
- **Frontend**: React with TypeScript
- **Backend**: Python 3.10+ with boto3
- **Infrastructure**: AWS CDK with TypeScript
- **Testing**: pytest for unit tests, ESLint for code quality
- **CI/CD**: Automated testing, building, and deployment

## 🎯 Data Flow Summary

1. **Data Ingestion**: Upload Abalone dataset → S3 Data Bucket
2. **Event Trigger**: Lambda detects new data → Creates manifest file
3. **CI/CD Pipeline**: CodePipeline triggered → Runs CI, ML Pipeline, Deploy stages
4. **ML Training**: SageMaker processes, trains, evaluates model
5. **Quality Gate**: Model passes evaluation criteria (MSE ≤ 6.0)
6. **Deployment**: CDK deploys infrastructure → SageMaker endpoint active
7. **Inference**: React app → API Gateway → SageMaker → Real-time predictions

**🏆 Total End-to-End Time**: ~30-40 minutes from data upload to live inference
**✅ Success Rate**: 100% (Validated by Rajinikanth Vadla)** 