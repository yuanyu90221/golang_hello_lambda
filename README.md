# test for golang_lambda with localstack

## prerequest

1. run up a localstack
2. pip install awslocal

## usage

```makefile
PWD=$(shell pwd)
build:
	GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build -o main main.go

zip_file: build
	zip main.zip main

create_lambda: zip_file
	awslocal lambda create-function \
	--function-name hello_v1 \
	--runtime go1.x \
	--zip-file fileb://${PWD}/main.zip \
	--handler main \
	--role arn:aws:iam::000000000000:role/lambda-role \
	--region=us-east-1

invoke:
	awslocal lambda invoke --function-name hello_v1 --endpoint http://localhost:4566 --region=us-east-1 result.txt
getfunction:
	awslocal lambda get-function --function-name hello_v1 --endpoint-url=http://localhost:4566 --region=us-east-1
delfunction:
	awslocal lambda delete-function --function-name hello_v1 --endpoint-url=http://localhost:4566 --region=us-east-1	
```