## Configuration update recommendations

The current resource configurations do not require an update, the suggestions below are simply recommendations for some optimization or
troubleshooting if required

- If the file size of the each S3 file containing the archive ids is large e.g 1GB
    - Increase [this function's memory size](https://github.com/yemisprojects/lambda-delete-glacier-archives/blob/main/Cloudformation/main_template.yaml#L184) from the current value of 256MB 
    - The Function could fail execution without enough RAM to read the S3 files
- If the lambda timout needs to be reduced from the current value of 15mins
    - Ensure to update this [countdown time value](https://github.com/yemisprojects/lambda-delete-glacier-archives/blob/main/Cloudformation/main_template.yaml#L195) to value lower than this [Lambda's time out value](https://github.com/yemisprojects/lambda-delete-glacier-archives/blob/main/Cloudformation/main_template.yaml#L182) i.e
~~~
         COUNT_DOWN_TIME < Lambda_Timeout
~~~
- If there are failures deleting the archives due to service throttling errors such as the [error logs further below](https://github.com/yemisprojects/lambda-delete-glacier-archives/blob/main/images/Throttling%20Errors.png)
    - Consider reducing [this Lambda concurrency](https://github.com/yemisprojects/lambda-delete-glacier-archives/blob/main/Cloudformation/main_template.yaml#L48) from the current value of 3 
    - You can update the stack parameter via a change set
- If there are only a few small sized s3 files e.g five 10KB files
    - Reducing this [sqs max receive count](https://github.com/yemisprojects/lambda-delete-glacier-archives/blob/main/Cloudformation/main_template.yaml#L96) to a lower value is an option but not required
    - It is currently set to 300 as the expectation is there could be as much as a 1000 or more S3 files being processe. Given lambda
    auto-scaling has been disabled, a high throttling by Lambda could increase likely hood of a premature redrive of sqs messages to the dead letter queue; hence a high default value is set.


![Throttling Errors](https://github.com/yemisprojects/lambda-delete-glacier-archives/blob/main/images/Throttling%20Errors.png)
<h4 align="center">Sample Service throttling Logs</h4>
