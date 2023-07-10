## End-to-end Multimodal Data Analysis with Purpose-built Health AI and Machine Learning Services on AWS

This repository contains code samples related to the AWS [Guidance for Multimodal Data Analysis with Purpose-built Health AI and Machine Learning Services on AWS](add link). You can follow the given instructions to build an end-to-end framework for storing, integrating, and analyzing genomic, clinical, and medical imaging data. 

As an example, we will use the [Synthea Coherent Data Set](https://registry.opendata.aws/synthea-coherent-data/), an open-source, synthetic data set that includes FHIR resources, MRI DICOM images, genomic data, physiological data (i.e., ECGs), and simple clinical notes. All the data types are linked together by FHIR. For the purpose of demonstration, we already converted the genomic data (originally released as CSV files with variant and annotation information) to VCF format, that is compatible with Amazon Omics. Similarly, we reformatted the clinical data into NDJSON files that comply with Amazon HealthLake. For ease of use, we have made this derived data set available on Amazon S3. Since the Coherent Data Set focuses on cardiovascular disease, we consider the use case of predicting patients' outcomes, such as hypertension, stroke, coronary heart disease, and Alzheimer's disease. 


#### Architecture Overview 

The following diagram provides an overview of the architecture and the steps followed to ingest, store, integrate, and analyze multimodal data leveraging AWS services. 

![Figure 1: Architecture for multimodal data analysis with purpose-built Health AI and machine learning services on AWS](./architecture/architecture-diagram.png)

#### Instructions 

1. Store multimodal data with purpose-built Health AI services ([Amazon Omics](https://aws.amazon.com/omics/), [Amazon HealthLake](https://aws.amazon.com/healthlake/), and [Amazon HealthLake Imaging](https://aws.amazon.com/healthlake/imaging/))
    * To store each data type in the purpose-built Health AI service, follow the artifacts in the corresponding folders. 
        * genomic - Run the notebook store-multimodal-data/genomic/store-analyze-genomicdata-with-amazonomics.ipynb. This creates Omics data stores (Reference Store, Variant Store, and Annotation Store) to import reference genome, VCF files, and ClinVar annotation file. 
        * clinical - Follow the instructions in store-multimodal-data/clinical/README.md to create a HealthLake data store and import NDJSON files. 
        * medical imaging - First, run the notebook store-multimodal-data/medical_imaging/store-imagingdata-with-amazon-healthlake-imaging.ipynb to create HealthLake Imaging data stores and import DICOM files. Then, run generate-radiomic-features.ipynb to generate radiomic features from multimple images in parallel using Amazon SageMaker Preprocessing. 

2. Preprocess and analyze multimodal data with [AWS Lake Formation](https://aws.amazon.com/lake-formation/), [Amazon Athena](https://aws.amazon.com/athena/), and [Amazon SageMaker Feature Store](https://aws.amazon.com/sagemaker/feature-store/?sagemaker-data-wrangler-whats-new.sort-by=item.additionalFields.postDateTime&sagemaker-data-wrangler-whats-new.sort-order=desc)
    * To prepare and analyze the multimodal data for downstream analysis (eg. querying with Amazon Athena, training a machine learning (ML) model with Amazon SageMaker), follow the artifacts in the corresponding folders.
        * genomic - Run the notebook preprocess-multimodal-data/genomic/preprocess-genomic.ipynb to execute feature engineering and store the final set of genomic features in SageMaker Feature Store, a fully managed service for machine learning features. 
        * clinical - Run the notebook preprocess-multimodal-data/clinical/preprocess-clinical.ipynb to execute feature engineering and store the final set of clinical features in SageMaker Feature Store. 
        * medical imaging - Run the notebook preprocess-multimodal-data/medical-imaging/preprocess-imaging.ipynb to execute feature engineering and store the final set of radiomic features in SageMaker Feature Store.

    At the end of this step, you have created three Feature Groups in SageMaker Feature Store, one for each data modality. We will use these features in the following steps to train a machine learning model and build visualization dashboards. 

3. Build, train, test, and deploy machine learning models with Amazon SageMaker 
    * For the given use case of patients' outcome prediction, we will use [SageMaker AutoGluon](https://docs.aws.amazon.com/sagemaker/latest/dg/autogluon-tabular.html), an AutoML framework that ensembles multiple ML models to improve predictive performance. To train and test the ML model on the multimodal feature set, run the notebook train-test-ml-model/train-test-model.ipynb. This generates evaluation metrics (accuracy, precision, recall, and f1 score) for the four outcomes (hypertension, stroke, coronary heart disease, and Alzheimer's disease).

4. Create data visualization dashboards with Amazon QuickSight 
    * Follow the instructions in visualization-dashboard/README.md to use the interactive dashboards and get a comprehensive view of patients across all three data modalities. These dashboards mitigate the challenge of data siloes and help end users (eg. clinicians, bioinformaticians, radiologists) easily access, view, and compare clinical, genomic, and medical imaging data across individual patients and cohorts. 



#### Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

#### License

This library is licensed under the MIT-0 License. See the LICENSE file.

