## Formatting REST responses

By default, non-error responses (operation completed successfully, "data" object has at least one non-null property), are formatted to look like standard REST responses. 

Upon error (operation did not complete successfully, "data" object is undefined or has only null properties) the REST API will return an object with the original `errors` array from GraphQL.

The format of both successful responses and error responses can be modified by providing *init()* with `formatErrorFn` or `formatDataFn` functions. 
 
Each of these function receives the response from GraphQL, and returns a modified JSON response that will be sent to the client via REST API. 

`formatErrorFn` also receives the HTTP status code that the REST API sends to the client. 

Example:

```js
/* this function will be called to format all errors emitted by GraphQL before sent out via REST API */
const formatError = (graphQlErrorObj, httpStatusCode = 500) => {
    const err = graphQlErrorObj;
    if (!err || typeof err !== 'object' || !err.errors || !Array.isArray(err.errors)) return err;
    const firstError = err.errors[0];
    if (!firstError || typeof firstError !== 'object') return err;
    
    return {
        message: firstError.message || 'General error',
        status: httpStatusCode
    }
}
```

And call init() with: 

```js
const restRouter = GraphQL2REST.init(schema, execute, options, formatError);
```

