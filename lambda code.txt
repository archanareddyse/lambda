import boto3
from uuid import uuid4

def lambda_handler(event, context):

    dynamodb = boto3.resource('dynamodb')
    dynamoTable = dynamodb.Table('newtable2')

    if 'Records' in event:
        for record in event['Records']:

            bucket_name = record['s3']['bucket']['name']
            object_key = record['s3']['object']['key']
            size = record['s3']['object'].get('size', -1)
            event_name = record['eventName']
            event_time = record['eventTime']

            dynamoTable.put_item(
                Item={
                    'unique': str(uuid4()),
                    'Bucket': bucket_name,
                    'Object': object_key,
                    'Size': size,
                    'Event': event_name,
                    'EventTime': event_time
                }
            )

    else:
        print("No S3 event received")

    return {
        "statusCode": 200,
        "body": "Data stored in DynamoDB"
    }
