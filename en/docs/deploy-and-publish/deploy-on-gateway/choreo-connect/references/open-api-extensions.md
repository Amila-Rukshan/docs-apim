  
Choreo Connect supports the following OpenAPI Extensions. You can use these extensions to override information with regard to API specifications.
  
   | Extension                         | Description                                                                                                            | Required/ Optional                          |  APIM Mode  | Standalone Mode |
   |-----------------------------------|------------------------------------------------------------------------------------------------------------------------|---------------------------------------------|------|-----|
   | `x-wso2-basePath`                 | Base path that the Gateway uses to expose the API.                                                                     | Optional → API level only                   | ✓ | ✓ |
   | `x-wso2-production-endpoints`     | Specifies the actual backend of the service.                                                                           | Optional → API/Resource level               | ✓ | ✓ |
   | `x-wso2-sandbox-endpoints`        | Specifies the sandbox endpoint of the service if available.                                                            | Optional → API/Resource level               | ✓ | ✓ |
   | `x-wso2-throttling-tier`          | Specifies the rate limiting for the API or resource.                                                                   | Optional → API/Resource level               | ✓ | ✓ |
   | `x-wso2-cors`                     | Specifies the Cross-Origin Resource Sharing (CORS) configuration for the API.                                          | Optional → API level only                   | ✓ | ✓ |
   | `x-wso2-disable-security`         | Enables the resource to be invoked without authentication.                                                             | Optional → API/Resource /Operation level  | ✓ | ✓ |
   | `x-wso2-auth-header`              | Specify the authorization header for the API to which either bearer or basic token is sent                             | Optional → API level only                | ✓ | ✓ |
   | `x-wso2-backend-http2-enabled`  | Enables gateway to enpoint communication using HTTP/2 protocol                                                           | Optional → API level only                | ✓ | ✓ |

!!! note
    -   If you want to expose an API or a resource without security, you can use the `x-wso2-disable-security` extension. You can find more information about this extension from [here]({{base_path}}/deploy-and-publish/deploy-on-gateway/choreo-connect/security/api-authentication/disabling-security/#disabling-security).
    -  Choreo Connect also supports the `"x-auth-type": "None"` option to disable the security. This extension has a different functionality when run with WSO2 API Manager and can have the following values which are not supported in standalone mode.
        -   Application & Application User
        -   Application
        -   Application User
    

   See the [petstore_basic.yaml file](https://github.com/wso2/product-microgateway/blob/main/samples/openAPI-definitions/petstore_basic.yaml) to view an example on how these OpenAPI extensions are used in an OpenAPI definition.