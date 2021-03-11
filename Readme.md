# Rightsize data collection

## Pre-rec

* Create an IAM role in the management account (where your rightsize data is), with the files/mgmt_account_policy.json policy. Make not of Role ARN
* Create an valid email for SES for config file https://docs.aws.amazon.com/ses/latest/DeveloperGuide/verify-email-addresses.html
* Load files to existsing s3 bucket <CodeBucket>
```aws s3 cp organization_rightsizing_lambda.yaml s3://<CodeBucket>/cloudformation/organization_rightsizing_lambda.yaml```
```aws s3 cp org.yaml s3://<CodeBucket>/cloudformation/org.yaml```
```aws s3 cp athena_emailer.yaml s3://<CodeBucket>/cloudformation/athena_emailer.yaml```

* Update the parameters file where varibles are showen by <>


### CONFIGURE PARAMETERS OF FUNCTION CODE AND UPLOAD CODE TO S3

This step is used to edit parameters (CUR database name and table, SES sender and recipient etc) in the Lambda function code, which is then uploaded to S3 for Lambda execution.

In the AutoCURDelivery.zip unzip.

config.yml - Configuration file
package/ - All dependencies, libraries, including pandas, numpy, Xlrd, Openpyxl, Xlsxwriter, pyyaml
Unzip config.yml from within AutoCURDelivery.zip, and open it into a text editor.

Configure the following parameters in config.yml:

CUR_Output_Location: Your S3 bucket created previously, i.e. S3://my-cur-bucket/out-put/
CUR_DB: CUR database and table name defined in Athena, i.e. athenacurcfn_my_athena_report.myathenareport
CUR_Report_Name: Report filename that is sent with SES as an attachment, i.e. cost_utilization_report.xlsx
Region: The region where SES service is called, i.e. us-east-1
Subject: SES mail subject, i.e. Cost and Utilization Report
Sender: Your sender e-mail address, i.e. john@example.com
Recipient: Your recipient e-mail addresses. If there are multiple recipients, separate them by comma, i.e. john@example.com,alice@example.com
Keep other configuration unchanged and save config.yml.

Add the updated config.yml back to AutoCURDelivery.zip by selecting the files and zipping them NOT THE FOLDER ITSELF

Upload AutoCURDelivery.zip to your S3 bucket. Make sure this S3 path is in the same region as Lambda function created in next step. NOTE this is a large 30+MB file, so it may take a little time
```aws s3 cp files/AutoCURDelivery.zip  s3://<CodeBucket>/code/AutoCURDelivery.zip``


## Deployment
``` aws cloudformation update-stack --stack-name rightsizesharesolution --template-body file://main.yaml --capabilities CAPABILITY_NAMED_IAM --parameters file://parameter.json```

### Follow up

Create athena view using files/athena_view.sql in files
