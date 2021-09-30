# Configuring a Distributed API-M Deployment

The WSO2 API-M server can be deployed as an [all-in-one deployment]({{base_path}}/install-and-setup/setup/single-node/all-in-one-deployment-overview) or as a distributed deployment. In the distributed setup, the [API-M server profiles]({{base_path}}/install-and-setup/setup/distributed-deployment/product-profiles) are deployed as separate API-M nodes. 

Given below are the API-M nodes you can have in a distributed deployment.

!!! Tip
    To enable high availability, you need a minimum of two nodes running each profile.

<table>
    <tr>
        <th>
            API-M Node (Profile)
        </th>
        <th>
            Description
        </th>
    </tr>
    <tr>
        <td>
            Gateway Worker Node
        </td>
        <td>
            API-M nodes running the Gateway profile.
        </td>
    </tr>
    <tr>
        <td>
            Control Plane Node
        </td>
        <td>
            API-M nodes running the Control Plane profile. The Control Plane includes the Traffic Manager, Key Manager, Publisher, and Developer Portal components.
        </td>
    </tr>
    <tr>
        <td>
            Traffic Manager Node (Optional)
        </td>
        <td>
            If required you can configure a separate API-M node to run the Traffic Manager component. That is, the Control Plane nodes run the Key Manager, Publisher, and Developer Portal, while the Traffic Manager runs on a separate node.
        </td>
    </tr>
</table>

## Step 1 - Install WSO2 API-M

To install and set up the API-M servers:

1.  Download the [WSO2 API Manager](http://wso2.com/products/api-manager/).
2.  Create copies of the API-M distribution for the individual profiles.

## Step 2 - Install and configure the databases

You can create the required databases for the API-M deployment in a separate server and point to the databases from the respective nodes. 

For information, see [Installing and Configuring the Databases]({{base_path}}/install-and-setup/setup/setting-up-databases/overview/).

## Step 3 - Configure your deployment with production hardening

Ensure that you have taken into account the respective security hardening factors (e.g., changing and encrypting the default passwords, configuring JVM security, etc.) before deploying WSO2 API-M. 

For more information, see [Production Deployment Guidelines]({{base_path}}/install-and-setup/deploying-wso2-api-manager/production-deployment-guidelines/#common-guidelines-and-checklist).

## Step 4 - Create and import SSL certificates

Create an SSL certificate for each of the WSO2 API-M nodes and import them to the keystore and the truststore. This ensures that hostname mismatch issues in the certificates will not occur. 

!!! Note
    The same primary keystore should be used for all API Manager instances to decrypt the registry resources. For more information, see [Configuring the Primary Keystore]({{base_path}}/install-and-setup/setup/security/configuring-keystores/configuring-keystores-in-wso2-api-manager/#configuring-the-primary-keystore).

For more information, see [Creating SSL Certificates]({{base_path}}/install-and-setup/setup/security/configuring-keystores/keystore-basics/creating-new-keystores/).

## Step 5 - Configure API-M Analytics

API Manager Analytics is delivered via the API Manager Analytics cloud solution. You need to configure the API Manager Gateway to publish analytics data to the cloud.

See the instructions on [configuring the API Gateway]({{base_path}}/api-analytics/gateways/configure-synapse-gateway) with the cloud-based analytics solution.

## Step 6 - Configure and start the profiles

Let's configure the API-M nodes in the deployment.

### Configure the Gateway nodes

Configure the Gateway nodes in the deployment to communicate with the Control Plane nodes.

<a href="{{base_path}}/assets/img/setup-and-install/gateway-connections.png"><img src="{{base_path}}/assets/img/setup-and-install/gateway-connections.png" width="60%"></a>

Follow the instructions given below to configure the Gateway node so that it can communicate with the Control Plane node:

{!includes/install_setup/configuring_gateway_node.md!}

### Configure the Control Plane nodes

Follow the steps given below to configure the Control Plane nodes to communicate with the Gateway.

!!! Note
    If you are separating the Traffic Manager from the Control Plane, be sure to apply the additional configurations explained in the [Configure separate Traffic Manager nodes](#configure-a-separate-traffic-manager-nodes-optional) section.

1.  Open the `<API-M_HOME>/repository/conf/deployment.toml` file of the Control Plane node.
2.  Add the following configurations to the `deployment.toml` file.
    
    !!! Info
        This configuration is used for publishing APIs to the Gateway and for connecting the Developer Portal component to the Gateway.
        
    ``` toml tab="Gateway with HA"
    [[apim.gateway.environment]]
    name = "Default"
    type = "hybrid"
    display_in_api_console = true
    description = "This is a hybrid gateway that handles both production and sandbox token traffic."
    show_as_token_endpoint_url = true
    ws_endpoint = "ws://[API-Gateway-LB-Host-or-IP]:9099"
    wss_endpoint = "wss://[API-Gateway-LB-Host-or-IP]:8099"
    http_endpoint = "http://[API-Gateway-LB-Host]"
    https_endpoint = "https://[API-Gateway-LB]"
    ```

    ``` toml tab="Single Gateway"
    [[apim.gateway.environment]]
    name = "Default"
    type = "hybrid"
    display_in_api_console = true
    description = "This is a hybrid gateway that handles both production and sandbox token traffic."
    show_as_token_endpoint_url = true
    ws_endpoint = "ws://[API-Gateway-host-or-IP]:9099"
    wss_endpoint = "wss://[API-Gateway-host-or-IP]:8099"
    http_endpoint = "http://[API-Gateway-host-or-IP]:${http.nio.port}"
    https_endpoint = "https://[API-Gateway-host-or-IP]:${https.nio.port}"
    ```

3.  Optionally, add the following configuration to enable distributed cache invalidation within the nodes.

    ``` toml
    [apim.cache_invalidation]
    enabled = true
    domain = "control-plane-domain"
    ```

4.  Follow the steps given below to configure High Availability (HA) for the Control Plane:
    
    1.  Create a copy of the API-M Control Plane node that you just configured. This is the second node of the API-M Control Plane cluster.
    2.  Configure a load balancer fronting the two Control Plane nodes in your deployment.
                
        For instructions, see [Configuring the Proxy Server and the Load Balancer]({{base_path}}/install-and-setup/setup/setting-up-proxy-server-and-the-load-balancer/configuring-the-proxy-server-and-the-load-balancer/).

### Configure separate Traffic Manager nodes (Optional)

In a typical distributed deployment, all API-M components (excluding the API-M Gateway) run in the Control Plane. However, you have the option of separating the Traffic Manager from the Control Plane. In this scenario, you need to apply the following additional configurations:

1.  Set up a new API-M server node to run the Traffic Manager profile.
2.  Configure the Traffic Manager nodes should communicate with the Control Plane.
3.  Configure the Key Manager, Publisher, and Developer Portal components in the Control Plane to communicate with the Traffic Manager.

<a href="{{base_path}}/assets/img/setup-and-install/traffic-manager-connections.png"><img src="{{base_path}}/assets/img/setup-and-install/traffic-manager-connections.png" width="60%"></a>

Follow the steps given below.

1.  Configuring the Traffic Manager nodes:

    1.  Open the `<API-M_HOME>/repository/conf/deployment.toml` file in the Traffic Manager node. 
    2.  Apply the following configurations to point to Key Manager node in the Control Plane.

        -   Connecting the Traffic Manager to the Key Manager component of the Control Plane:

            ``` toml tab="Control Plane with HA"
            [apim.key_manager]
            service_url = "https://[control-plane-LB-host]/services/"
            username = "$ref{super_admin.username}"
            password = "$ref{super_admin.password}"
            ```
                
            ``` toml tab="Single Key Manager"
            [apim.key_manager]
            service_url = "https://[control-plane-host]:${mgt.transport.https.port}/services/"
            username = "$ref{super_admin.username}"
            password = "$ref{super_admin.password}"
            ```

        -   Connecting the Traffic Manager to the Gateway:

            ``` toml
            [[apim.gateway.environment]]
            name = "Default"
            type = "hybrid"
            ```

    3. Follow the steps given below to configure High Availability (HA) for the Traffic Manager:
    
        1.  Create a copy of the Traffic Manager node that you just configured. This is the second node of the Traffic Manager cluster.
        2.  Configure a load balancer fronting the two Traffic Manager nodes in your deployment.
        2.  Open the `deployment.toml` file in the Traffic Manager node and add the following configuration to publish events to the other node.

    
            ``` toml tab="Node1"
            [apim.throttling]
            event_duplicate_url = ["tcp://Traffic-Manager-2-host:5672"]
            ```
            
            ``` toml tab="Node2"
            [apim.throttling]
            event_duplicate_url = ["tcp://Traffic-Manager-1-host:5672"]
            ```
            
        !!! Note
            In each startup of a Traffic Manager node, the rate-limiting policies are redeployed by retrieving the latest policy details from the database. This maintains the consistency between the Traffic Manager nodes. If you need to avoid redeploying certain rate-limiting policies, add the following configuration to the `<API-M_HOME>/repository/conf/deployment.toml` file in the Traffic Manager node.  
                
            ```toml
            [apim.throttling]
            skip_redeploying_policies = ["throttle_policy_1","throttle_policy_2"]
            ```                   

    ??? info "Sample configuration for the Traffic Manager"
        ``` toml
        [server]
        hostname = "tm.wso2.com"
        node_ip = "127.0.0.1"
        server_role = "traffic-manager"
        offset=3
        
        [user_store]    
        type = "database"
        
        [super_admin]
        username = "admin"
        password = "admin"
        create_admin_account = true
        
        [database.apim_db]
        type = "mysql"
        hostname = "db.wso2.com"
        name = "apim_db"
        port = "3306"
        username = "root"
        password = "root"

        [database.shared_db]
        type = "mysql"
        hostname = "db.wso2.com"
        name = "shared_db"
        port = "3306"
        username = "root"
        password = "root"

        [keystore.tls]
        file_name =  "wso2carbon.jks"
        type =  "JKS"
        password =  "wso2carbon"
        alias =  "wso2carbon"
        key_password =  "wso2carbon"
        
        [truststore]
        file_name = "client-truststore.jks"
        type = "JKS"
        password = "wso2carbon"

        [apim.key_manager]
        service_url = "https://km.wso2.com:9443/services/"
        username= "$ref{super_admin.username}"
        password= "$ref{super_admin.password}"

        [[apim.gateway.environment]]
        name = "Default"
        type = "hybrid"
        ```

2.  Configuring the Control Plane nodes:

    !!! Note
        The following configurations connect the Control Plane to the Traffic Manager node. Note that you must also [configure the Control Plane with the Gateway](#configure-the-control-plane-nodes).

    1.  Open the `<API-M_HOME>/repository/conf/deployment.toml` file in the Control Plane node. 
    2.  Apply the following configurations to the `deployment.toml` file. 
    
        -   Connecting the Key Manager component in the Control Plane to the Traffic Manager.
 
            ``` toml tab="Traffic Manager with HA"
            [apim.throttling]
            throttle_decision_endpoints = ["tcp://Traffic-Manager-1-host:5672","tcp://Traffic-Manager-2-host:5672"]
            
            [[apim.throttling.url_group]]
            traffic_manager_urls = ["tcp://Traffic-Manager-1-host:9611"]
            traffic_manager_auth_urls = ["ssl://Traffic-Manager-1-host:9711"]
            
            [[apim.throttling.url_group]]
            traffic_manager_urls = ["tcp://Traffic-Manager-2-host:9611"]
            traffic_manager_auth_urls = ["ssl://Traffic-Manager-2-host:9711"]

            [[event_listener]]
            id = "token_revocation"
            type = "org.wso2.carbon.identity.core.handler.AbstractIdentityHandler"
            name = "org.wso2.is.notification.ApimOauthEventInterceptor"
            order = 1

            [event_listener.properties]
            notification_endpoint = "https://[Traffic-Manager-LB-Host]/internal/data/v1/notify"
            username = "${admin.username}"
            password = "${admin.password}"
            'header.X-WSO2-KEY-MANAGER' = "default"
            ```

            ``` toml tab="Single Traffic Manager"
            [apim.throttling]
            throttle_decision_endpoints = ["tcp://Traffic-Manager-host:5672"]
                
            [[apim.throttling.url_group]]
            traffic_manager_urls = ["tcp://Traffic-Manager-host:9611"]
            traffic_manager_auth_urls = ["ssl://Traffic-Manager-host:9711"]

            [[event_listener]]
            id = "token_revocation"
            type = "org.wso2.carbon.identity.core.handler.AbstractIdentityHandler"
            name = "org.wso2.is.notification.ApimOauthEventInterceptor"
            order = 1

            [event_listener.properties]
            notification_endpoint = "https://Traffic-Manager-host:${mgt.transport.https.port}/internal/data/v1/notify"
            username = "${admin.username}"
            password = "${admin.password}"
            'header.X-WSO2-KEY-MANAGER' = "default"
            ```

        -   Connecting the Publisher component in the Control Plane with the Traffic Manager.

            !!! Info
                This configuration enables the publishing of throttling policies, custom templates, block conditions, and API events to the Traffic Manager node.

            ``` toml tab="Traffic Manager with HA"
            [apim.throttling]
            service_url = "https://[Traffic-Manager-LB-Host]/services/"
            throttle_decision_endpoints = ["tcp://Traffic-Manager-1-host:5672","tcp://Traffic-Manager-2-host:5672"]

            [[apim.throttling.url_group]]
            traffic_manager_urls = ["tcp://Traffic-Manager-1-host:9611"]
            traffic_manager_auth_urls = ["ssl://Traffic-Manager-1-host:9711"]
            
            [[apim.throttling.url_group]]
            traffic_manager_urls = ["tcp://Traffic-Manager-2-host:9611"]
            traffic_manager_auth_urls = ["ssl://Traffic-Manager-2-host:9711"]
            ```
            
            ``` toml tab="Single Traffic Manager"
            [apim.throttling]
            service_url = "https://Traffic-Manager-host:${mgt.transport.https.port}/services/"
            throttle_decision_endpoints = ["tcp://Traffic-Manager-host:5672"]

            [[apim.throttling.url_group]]
            traffic_manager_urls = ["tcp://Traffic-Manager-host:9611"]
            traffic_manager_auth_urls = ["ssl://Traffic-Manager-host:9711"]
            ```

        -   Connecting the Developer Portal component of the Control Plane with the Traffic Manager.

            ``` toml tab="Traffic Manager with HA"
            [apim.throttling]
            throttle_decision_endpoints = ["tcp://Traffic-Manager-1-host:5672","tcp://Traffic-Manager-2-host:5672"]
            
            [[apim.throttling.url_group]]
            traffic_manager_urls = ["tcp://Traffic-Manager-1-host:9611"]
            traffic_manager_auth_urls = ["ssl://Traffic-Manager-1-host:9711"]
            
            [[apim.throttling.url_group]]
            traffic_manager_urls = ["tcp://Traffic-Manager-2-host:9611"]
            traffic_manager_auth_urls = ["ssl://Traffic-Manager-2-host:9711"]
            ```
            
            ``` toml tab="Single Traffic Manager"
            [apim.throttling]
            throttle_decision_endpoints = ["tcp://Traffic-Manager-host:5672"]
                    
            [[apim.throttling.url_group]]
            traffic_manager_urls = ["tcp://Traffic-Manager-host:9611"]
            traffic_manager_auth_urls = ["ssl://Traffic-Manager-host:9711"]
            ```

    3.  If required, encrypt the Auth Keys (access tokens, client secrets, and authorization codes), see [Encrypting OAuth Keys]({{base_path}}/design/api-security/oauth2/encrypting-oauth2-tokens/).

    4. Optionally, add the following configuration to enable distributed cache invalidation within the Control Plane nodes.

        ``` toml
        [apim.cache_invalidation]
        enabled = true
        domain = "control-plane-domain"
        ```

## Step 7 - Start the API-M nodes

Once you have successfully configured all the API-M nodes in the deployment, you can start the servers.

-   Starting the Gateway nodes

    Open a terminal, navigate to the `<API-M-GATEWAY-HOME>/bin` folder, and execute the following command:
    
    ``` java tab="Linux/Mac OS"
    cd <API-M_HOME>/bin/
    sh api-manager.sh -Dprofile=gateway-worker
    ```
    
    ``` java tab="Windows"
    cd <API-M_HOME>\bin\
    api-manager.bat --run -Dprofile=gateway-worker
    ```

-   Start the Control Plane nodes

    Open a terminal, navigate to the `<API-M-CONTROL-PLANE-HOME>/bin` folder, and execute the following command:

    ``` java tab="Linux/Mac OS"
    cd <API-M_HOME>/bin/
    sh api-manager.sh -Dprofile=control-plane
    ```

    ``` java tab="Windows"
    cd <API-M_HOME>\bin\
    api-manager.bat --run -Dprofile=control-plane
    ```

-   Start the Traffic Manager nodes

    Open a terminal, navigate to the `<API-M-TRAFFIC-MANAGER-HOME>/bin` folder, and execute the following command:

    ``` java tab="Linux/Mac OS"
    cd <API-M_HOME>/bin/
    sh api-manager.sh -Dprofile=traffic-manager
    ```

    ``` java tab="Windows"
    cd <API-M_HOME>\bin\
    api-manager.bat --run -Dprofile=traffic-manager
    ```

For more information on starting API-M profiles, see [API-M Profiles]({{base_path}}/install-and-setup/setup/distributed-deployment/product-profiles).
