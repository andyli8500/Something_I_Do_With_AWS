> This article illustrates that how to change the retry parameters for DynamoDB SDK in boto3.

## To change the retry parameters, such as "max attempts", use either of the followings:

-  If using boto3 Client:

  ```
  client = boto3.client('dynamodb', region_name='<desired_region>')
  client.meta.events._unique_id_handlers['retry-config-dynamodb']['handler']._checker.__dict__['_max_attempts'] = 20
  ```

- If using boto3 Service Resource:

  ```
  dynamodb = boto3.resource('dynamodb', region_name='<desired_region>')
  dynamodb.meta.client.meta.events._unique_id_handlers['retry-config-dynamodb']['handler']._checker.__dict__['_max_attempts'] = 20
  ```
  

### More on boto3 DynamoDB retry config:

- Get all the available retry parameters for DynamoDB by:

  ```
  print(client.meta.events._unique_id_handlers['retry-config-dynamodb']['handler']._checker.__dict__)
  ```

- The above code gives the output looks like this (by default)

  ```
  {
      '_max_attempts': 10,
      '_checker': < botocore.retryhandler.MultiChecker object at 0x104d40e50 > ,
      '_retryable_exceptions': ( < class 'botocore.vendored.requests.exceptions.ConnectionError' > , 
                            < class 'botocore.vendored.requests.packages.urllib3.exceptions.ClosedPoolError' > , 
                            < class 'botocore.vendored.requests.exceptions.Timeout' > , 
                            < class 'botocore.exceptions.EndpointConnectionError' > , 
                            < class 'botocore.exceptions.ChecksumError' > )
  }
  ```

### If you would like to explore more on what functions are callable but not documented in the boto3 documentation, use the following code to find out what are the available methods a function/object has:

```
print([method for method in dir(obj)])
```

- For example, the available methods can be called from a boto3 client
  
  ```
  client = boto3.client('dynamodb', region_name='<desired_region>')
  print([method for method in dir(client)])
  ```

Which gives the output of all the available methods and classes:

  ```
  ['_PY_TO_OP_NAME', 
       '__class__', 
       '__delattr__', 
       '__dict__', 
       '__doc__', 
       '__format__', 
       '__getattr__', 
       '__getattribute__', 
       '__hash__', 
       '__init__', 
       '__module__', 
       '__new__', 
       '__reduce__', 
       '__reduce_ex__', 
       '__repr__', 
       '__setattr__', 
       '__sizeof__', 
       '__str__', 
       '__subclasshook__', 
       '__weakref__', 
       '_cache', 
       '_client_config', 
       '_convert_to_request_dict', 
       '_endpoint', 
       '_exceptions', 
       '_exceptions_factory', 
       '_get_waiter_config', 
       '_load_exceptions', 
       '_loader', 
       '_make_api_call', 
       '_register_handlers', 
       '_request_signer', 
       '_response_parser', 
       '_serializer', 
       '_service_model', 
       u'batch_get_item', 
       u'batch_write_item', 
       'can_paginate', 
       u'create_table', 
       u'delete_item', 
       u'delete_table', 
       u'describe_limits', 
       u'describe_table', 
       u'describe_time_to_live', 
       'exceptions', 
       'generate_presigned_url', 
       u'get_item', 
       'get_paginator', 
       'get_waiter', 
       u'list_tables', 
       u'list_tags_of_resource', 
       'meta', 
       u'put_item', 
       u'query', 
       u'scan', 
       u'tag_resource', 
       u'untag_resource', 
       u'update_item', 
       u'update_table', 
       u'update_time_to_live', 
       'waiter_names']
  ```

------------------------
### Resources:

[1] Error Handling - Error Retries and Exponential Backoff - http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.Errors.html#Programming.Errors.RetryAndBackoff
