+++
title = "Create the control"
weight = 100
home_region = "eu-west-1"
codecommit_repo_name = "aws-config-rds-storage-encrypted" 
codecommit_repo_branch = "master" 
product_name = "aws-config-rds-storage-encrypted"
product_version = "v1"
portfolio_name = "cloud-engineering-governance"
+++
---

## What are we going to do?

We are going to perform the following steps:

- define another product with a version and add it to the existing _{{% param portfolio_name %}}_ portfolio 
- add the source code for our product
- provision that product into a spoke account

## Step by step guide

Here are the steps you need to follow to "{{% param title %}}"

### Define a product with a version and a portfolio

- Navigate to the {{% service_catalog_factory_code_commit_repo_link %}} again

- Click on *portfolios*

{{< figure src="/tasks/ClickOnPortfolios.png" >}}

- Click on *reinvent.yaml*

{{< figure src="/tasks/ClickOnreinvent.png" >}}

- Click *Edit*

{{< figure src="/tasks/ClickEdit.png" >}}

- We will need to insert the following to the products section:

{{% code file="40-reinvent2019/150-task-2/artefacts/factory/new_product_details.yaml" language="js" %}}

- Once completed it should like look this: 

 <figure>
  {{< highlight js >}}
 
 Schema: factory-2019-04-01
 Products:
   - Name: "aws-config-desired-instance-types"
     Owner: "budget-and-cost-governance@example.com"
     Description: "Enables AWS Config rule - desired-instance-type with our RIs"
     Distributor: "cloud-engineering"
     SupportDescription: "Speak to budget-and-cost-governance@example.com about exceptions and speak to cloud-engineering@example.com about implementation issues"
     SupportEmail: "cloud-engineering@example.com"
     SupportUrl: "https://wiki.example.com/cloud-engineering/budget-and-cost-governance/aws-config-desired-instance-types"
     Tags:
       - Key: "type"
         Value: "governance"
       - Key: "creator"
         Value: "cloud-engineering"
       - Key: "cost-center"
         Value: "governance"
     Versions:
       - Name: "v1"
         Description: "v1 of aws-config-desired-instance-types"
         Active: True
         Source:
           Provider: "CodeCommit"
           Configuration:
             RepositoryName: "aws-config-desired-instance-types"
             BranchName: "master"
     Portfolios:
       - "cloud-engineering-governance"
 
   - Name: "aws-config-rds-storage-encrypted"
     Owner: "data-governance@example.com"
     Description: "Enables AWS Config rule - aws-config-rds-storage-encrypted"
     Distributor: "cloud-engineering"
     SupportDescription: "Speak to data-governance@example.com about exceptions and speak to cloud-engineering@example.com about implementation issues"
     SupportEmail: "cloud-engineering@example.com"
     SupportUrl: "https://wiki.example.com/cloud-engineering/data-governance/aws-config-rds-storage-encrypted"
     Tags:
       - Key: "type"
         Value: "governance"
       - Key: "creator"
         Value: "cloud-engineering"
       - Key: "cost-center"
         Value: "governance"
     Versions:
       - Name: "v1"
         Description: "v1 of aws-config-rds-storage-encrypted"
         Active: True
         Source:
           Provider: "CodeCommit"
           Configuration:
             RepositoryName: "aws-config-rds-storage-encrypted"
             BranchName: "master"
     Portfolios:
       - "cloud-engineering-governance"
 
 Portfolios:
   - DisplayName: "cloud-engineering-governance"
     Description: "Portfolio containing the products needed to govern AWS accounts"
     ProviderName: "cloud-engineering"
     Associations:
       - "arn:aws:iam::${AWS::AccountId}:role/TeamRole"
     Tags:
       - Key: "type"
         Value: "governance"
       - Key: "creator"
         Value: "cloud-engineering"
       - Key: "cost-center"
         Value: "governance"

 
 {{< / highlight >}}
 </figure>
 
 
 
- Set your *Author name*
- Set your *Email address*
- Set your *Commit message*

{{% notice tip %}}
Using a good / unique commit message will help you understand what is going on later.
{{% /notice %}}

- Click the *Commit changes* button:

{{< figure src="/tasks/CommitChanges.png" >}}

#### What did we just do?

The YAML we pasted in the previous step told the framework to perform several actions:

- create a product named _{{% param product_name %}}_
- add a _{{% param product_version %}}_ of our product
- add the product: _{{% param product_name %}}_ to the portfolio: _{{% param portfolio_name %}}_

#### Verify that the change worked

Once you have made your changes the {{% service_catalog_factory_pipeline_link %}} should have run. If you were very quick, the pipeline 
may still be running.  If it has not yet started feel free to the hit the *Release change* button.

Once it has completed it should show the *Source* and *Build* stages in green to indicate they have completed 
successfully:

{{< figure src="/tasks/SuccessfulFactoryRun.png" >}}

{{% notice note %}}
If this is failing please raise your hand for some assistance
{{% /notice %}}


### Add the source code for our product

When you configured your product version, you specified the following version: 

{{% code file="40-reinvent2019/150-task-2/artefacts/factory/create-the-version--version-only.yaml" language="js" %}}

This tells the framework the source code for the product comes from the _{{% param codecommit_repo_branch %}}_ branch of a
_CodeCommit_ repository of the name _{{% param codecommit_repo_name %}}_. 

We now need to create the CodeCommit repository and add the CloudFormation template we are going to use for our
product.

- Navigate to {{% codecommit_link %}}

- Click *Create repository*

{{< figure src="/tasks/CreateRepository.png" >}}

- Input the name `{{% param codecommit_repo_name %}}`

{{< figure src="/tasks/InputTheName.png" >}}

- Click *Create*

{{< figure src="/tasks/ClickCreate.png" >}}

- Scroll down to the bottom of the page and hit the *Create file* button

{{< figure src="/tasks/create_file.png" >}}

- Copy the following snippet into the main input field:

 <figure>
  {{< highlight js >}}

AWSTemplateFormatVersion: '2010-09-09'
Description: "Create an AWS Config rule ensuring RDS instances use encrypted storage"

Resources:
  AWSConfigRule:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: "rds-storage-encrypted"
      Description: "Checks whether storage encryption is enabled for your RDS DB instances."
      Scope:
        ComplianceResourceTypes:
          - "AWS::RDS::DBInstance"
      Source:
        Owner: AWS
        SourceIdentifier: RDS_STORAGE_ENCRYPTED

 {{< / highlight >}}
 </figure>
 

- Set the *File name* to `product.template.yaml`

- Set your *Author name*
- Set your *Email address*
- Set your *Commit message*

{{% notice tip %}}
Using a good / unique commit message will help you understand what is going on later.
{{% /notice %}}

Creating that file should trigger your 
{{% codepipeline_pipeline_link "aws-config-rds-storage-encrypted-v1-pipeline" %}}.  

Once the pipeline has completed it should show the *Source*, *Package*, *Package* and *Deploy* stages in green to indicate they have 
completed successfully:

{{< figure src="/tasks/SuccessfulFactoryProductRun.png" >}}

{{% notice tip %}}
You should see your commit message on this screen, it will help you know which version of ServiceCatalogFactory repository the 
pipeline is processing.
{{% /notice %}}

{{% notice note %}}
If this is failing please raise your hand for some assistance
{{% /notice %}}

Once you have verified the pipeline has run you can go to {{% service_catalog_products_list_link %}} to view your newly
created version.

You should see the product you created listed:

{{< figure src="/tasks/SeeYourTask1Product.png" >}}

Click on the product and verify *{{% param product_version %}}* is there

{{< figure src="/tasks/SeeYourTask1ProductVersion1.png" >}}

{{% notice note %}}
If you cannot see your version please raise your hand for some assistance
{{% /notice %}}

You have now successfully created a version for your product! 

#### Verify the product was added to the portfolio

Now that you have verified the pipeline has run you can go to {{% service_catalog_portfolios_list_link %}} to view your
portfolio.

- Click on *reinvent-cloud-engineering-governance*

{{< figure src="/tasks/PortfolioReinventCloudEngineeringGovernance.png" >}}

- Click on the product *_{{% param product_name %}}_*

- Click on the version *_{{% param product_version %}}_*

{{< figure src="/tasks/ClickAwsConfigS3BucketServerSideEncryptionEnabledV1.png" >}}