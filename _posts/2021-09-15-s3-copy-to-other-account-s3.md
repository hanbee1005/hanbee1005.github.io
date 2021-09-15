---
layout: post
title:  "[AWS Lambda] S3 데이터 다른 계정 S3로 복사"
date:   2021-09-15 15:03:23 +0900
categories: jekyll update
---

아래와 같이 Lambda를 통해 Account A의 S3에 저장된 데이터를 그대로 Account B의 S3로 복사하는 과정을 알아보겠습니다.
```
┌─ AWS Account A───┐     ┌──AWS Account B ──┐
│                  │     │                  │
│                  │     │                  │
│ ┌─────────────┐  │     │  ┌────────────┐  │
│ │     S3      ├──┼─────┼─►│     S3     │  │
│ └─────────────┘  │ ▲   │  └────────────┘  │
│                  │ │   │                  │
│                  │ │   │                  │
│ ┌─────────────┐  │ │   │                  │
│ │ Lambda Func ├──┼─┘   │                  │
│ └─────────────┘  │     │                  │
│                  │     │                  │
└──────────────────┘     └──────────────────┘
```

Lambda는 python을 사용하여 작성하였습니다.
```python
import json
import boto3
from urllib.parse import unquote_plus

def lambda_handler(event, context):
    source_client = boto3.client('s3')
    
    destination_client = boto3.client('s3', aws_access_key_id='XXXXX', aws_secret_access_key='XXXXX')
    
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = unquote_plus(record['s3']['object']['key'])
        
        # 복사할 소스 파일 가져오기
        source_response = source_client.get_object(Bucket=bucket, Key=key)
        
        # 대상 s3에 그대로 내용 복사
        destination_client.upload_fileobj(source_response['Body'], 'DESTINATION_BUCKET_NAME',key)
        
        # 복사한 뒤 소스 S3 내 파일 삭제
        source_client.delete_object(Bucket=bucket, Key=key)
```

작업을 하던 중 아래와 같은 요청 사항이 추가되었습니다.
- 소스 S3에 폴더(yyyy-MM-dd-HH-mm-ss) 단위로 데이터가 저장
- 기존 대상 S3에 복사된 데이터를 모두 삭제하고 소스 S3에 마지막에 추가된 폴더만 복사될 수 있도록 처리

따라서 아래와 같은 코드를 추가하였습니다.
```python
import json
import boto3
from urllib.parse import unquote_plus

def lambda_handler(event, context):
    source_client = boto3.client('s3')
    
    destination_client = boto3.client('s3', aws_access_key_id='XXXXX', aws_secret_access_key='XXXXX')
    
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = unquote_plus(record['s3']['object']['key'])
        source_response = source_client.get_object(Bucket=bucket, Key=key)
        
        # 파일 복사 전 이미 복사된 파일 삭제
        delete_bucket(destination_client, 'DESTINATION_BUCKET_NAME', key.split('/')[0])
        
        destination_client.upload_fileobj(source_response['Body'], 'DESTINATION_BUCKET_NAME',key)
    
# 대상 S3 내 기존 데이터 삭제 (대상 s3, 대상 bucket 이름, 디렉토리명) - 같은 디렉토리에 저장된 파일은 삭제하지 않기 위함
def delete_bucket(s3_client, bucket_name, dir_name):
    objects = s3_client.list_objects(Bucket=bucket_name)
    content = objects.get('Contents', [])
    # bucket 내 모든 파일을 하나씩 확인하면서 
    for obj in content:
        key = obj['Key']
        # 상위 디렉토리가 다른 경우에만 파일 삭제
        if dir_name != key.split('/')[0]:
            s3_client.delete_object(Bucket=bucket_name, Key=key)
    # s3_client.delete_bucket(Bucket=bucket_name) # 버킷 자체를 삭제
```

이 방식은 운영 계정의 로그 데이터를 개발 계정으로 복사해서 분석하고 싶은 경우와 같이 여러가지 목적으로 계정간 데이터 공유를 필요로 할 때 사용할 수 있습니다.

*******

- Lambda 함수 생성 후 트리거로 S3를 추가합니다. 원하는 버킷을 선택하고 원하는 이벤트를 지정하여 연결하면 S3에 파일이 추가될 때마다 해당 람다 함수가 호출되어 복사를 진행합니다.

- Account A에서 Account B 계정의 S3에 접근하기 위해 람다 내에서 대상 S3 호출 시 아래와 같이 access_key와 secret_access_key를 추가해주었습니다.
```python
destination_client = boto3.client('s3', aws_access_key_id='XXXXX', aws_secret_access_key='XXXXX')
```
이 키는 Account B에서 IAM으로 사용자를 생성하고 S3에 접근할 수 있는 권한을 부여한 뒤 발급 받은 액세스 키를 적어주면 됩니다.