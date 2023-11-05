Certainly, here's a complete set of steps to create an AWS Lambda function and API Gateway endpoint for performing a port scan, along with the Python Lambda function code using Boto3:

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

Python Lambda function using Boto3:

```python
import json
import socket

def lambda_handler(event, context):
    # Get the requestor's IP address from the API Gateway event
    requestor_ip = event['requestContext']['identity']['sourceIp']

    # Define the range of ports to scan
    ports_to scan = [80, 443, 22, 3389]

    open_ports = []
    for port in ports_to_scan:
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(1)
        result = sock.connect_ex((requestor_ip, port))
        sock.close()
        if result == 0:
            open_ports.append(port)

    response = {
        'statusCode': 200,
        'body': json.dumps({
            'requestor_ip': requestor_ip,
            'open_ports': open_ports
        })
    }

    return response
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
