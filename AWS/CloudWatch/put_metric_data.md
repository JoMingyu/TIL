# put_metric_data
CloudWatch에는 custom metric이란 게 있고, `put_metric_data` API로 이런 custom metric을 추가할 수 있다. [CLI](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-data.html)로도 할 수 있지만 그냥 boto3 예제를 보자.

```python
import boto3

# Create CloudWatch client
cloudwatch = boto3.client('cloudwatch')

# Put custom metrics
cloudwatch.put_metric_data(
    MetricData=[
        {
            'MetricName': 'PAGES_VISITED',
            'Dimensions': [
                {
                    'Name': 'UNIQUE_PAGES',
                    'Value': 'URLS'
                },
            ],
            'Unit': 'None',
            'Value': 1.0
        },
    ],
    Namespace='SITE/TRAFFIC'
)
```

간단하게 사용한다면 별도의 Dimension 없이 걍 metric만 두고 put해도 된다.
