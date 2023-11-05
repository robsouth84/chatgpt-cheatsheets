Sure, here are the same steps with placeholders replaced by Bash variables for improved readability:

**Step 1: Create an AWS Lambda Function**

```bash
function_name="PortScanFunction"
role_arn="<your-role-arn>"
handler="lambda_function.lambda_handler"
lambda_code_path="path/to/your/lambda-code.zip"

aws lambda create-function \
  --function-name $function_name \
  --runtime python3.8 \
  --role $role_arn \
  --handler $handler \
  --zip-file fileb://$lambda_code_path
```

**Step 2: Create an API Gateway**

```bash
api_name="PortScanAPI"
aws apigateway create-rest-api --name $api_name

api_id="<your-api-id>"
stage_name="prod"

aws apigateway create-deployment --rest-api-id $api_id --stage-name $stage_name
```

**Step 3: Deploy the API and Get the Endpoint URL**

```bash
api_id="<your-api-id>"
stage_name="prod"

aws apigateway create-deployment --rest-api-id $api_id --stage-name $stage_name

endpoint_url=$(aws apigateway get-stage --rest-api-id $api_id --stage-name $stage_name --query 'deploymentId' --output text)
```

**Step 4: Set Up Permissions**

```bash
role_name="LambdaApiGatewayRole"
trust_policy_path="path/to/your/trust-policy.json"
lambda_execute_policy_arn="arn:aws:iam::aws:policy/service-role/AWSLambda_Execut"

aws iam create-role \
  --role-name $role_name \
  --assume-role-policy-document file://$trust_policy_path

aws iam attach-role-policy --role-name $role_name --policy-arn $lambda_execute_policy_arn

aws lambda update-function-configuration \
  --function-name $function_name \
  --role "<your-newly-created-role-arn>"
```

**Step 5: Invoke the API**

```bash
# Replace with your actual endpoint URL
endpoint_url="https://your-api-id.execute-api.your-region.amazonaws.com/prod"

# Make a GET request to the comescanmebro endpoint
curl -X GET $endpoint_url/comescanmebro
```

By using Bash variables, you can easily customize and reuse these commands with different values for your specific scenarios.
