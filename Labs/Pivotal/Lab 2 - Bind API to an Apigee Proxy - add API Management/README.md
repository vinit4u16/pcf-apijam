# Apigee Edge Service Broker Org Plan: Secure a CF App

*Duration : 45 mins*

*Persona : API Team*

# Use case

You have an API Created in Pivotal Cloud Founday. You want to proxy it through Apigee Edge

# How can Apigee Edge help?

The [Apigee Edge Service Broker for PCF](http://docs.pivotal.io/partners/apigee/index.html) enables developers to manage APIs for their PCF apps through the Apigee Edge management console.

This lab describes how to push a sample app to Pivotal Cloud Foundry (PCF), create an Apigee Edge service instance, and bind the application to it. After binding the application to the Apigee Edge service instance, requests to the app will be forwarded to an Apigee Edge API proxy for management. Its the same lab as listed in [PCF documentation](http://docs.pivotal.io/partners/apigee/proxying.html)

# Pre-requisites

* You have [installed and configured](http://docs.pivotal.io/partners/apigee/installing.html) the Apigee Edge Service Broker for PCF tile. Or you got a set of credentials from your instructor that has access to a PCF environment with Apigee Edge Service Broker for PCF tile. 

* You have installed [cf CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html).

* You have an Apigee account and have access to an Apigee org

* You have deployed a sample app to PCF, by completing the Lab 1 - API Design, Developement - Build a microservice on PCF from an OpenAPI Specification 

# Instructions

### Windows Users: replace "export" commands with Windows' "set" command.  In addition, when  environment variables such as $APIGEE_TOKEN are used, please replace it with %APIGEE_TOKEN%

1. Login to Apigee, through Apigee's SSO

    If you are on Linux/mac, the following commands should give you a valid token that you can use for the next step
    
    ```
    curl https://login.apigee.com/resources/scripts/sso-cli/ssocli-bundle.zip -o "ssocli-bundle.zip"
    tar xvf ssocli-bundle.zip
    mkdir ~/.sso-cli
    ./get_token
	<<export the token to a environment variable>>
	export APIGEE_TOKEN={token}
    ```
    
    
    If you are on windows/or want to get the access token manually, you have to issue an HTTP call, similar to this
    
    ```
    curl -X POST   https://login.apigee.com/oauth/token \
    -H 'authorization: Basic ZWRnZWNsaTplZGdlY2xpc2VjcmV0' \
    -H 'Content-type: application/x-www-form-urlencoded' \
    --data-urlencode 'username={USER_NAME}' \
    --data-urlencode 'password={Password}' \
    --data-urlencode 'grant_type=password'
	
	export APIGEE_TOKEN={token}
    ```
    
    You will use the access_token from the JSON 
    
2. Creating an Apigee Edge Service Broker binding for your app
    We will now add apigee-edge service to our PCF_ORG.
    First lets make sure that apigee-edge tile has been enabled for this org
    ```
    cf marketplace
    ```
    This should show apigee-edge as one of the available services.
    ```
    cf create-service apigee-edge org apigee_org_service -c '{"org":"'$(echo  $APIGEE_ORG)'","env":"'$(echo  $APIGEE_ENV)'"}'
    ```
    
    To check that the service has been enabled, try the following command
    
    ```
    cf service apigee_org_service
    ```
    Now we will bind the app (Our Node.js app that servers the Hello API) to an Apigee ORG with the following command.
    ```
    cf bind-route-service $PCF_DOMAIN apigee_org_service --hostname $PCF_APPHOST -c '{"org":"'$(echo  $APIGEE_ORG)'","env":"'$(echo  $APIGEE_ENV)'", "bearer":"'$(echo $APIGEE_TOKEN)'", "action":"proxy bind"}'
    ```
	
	


3. Testing the API
    Login to [https://apigee.com/edge](https://apigee.com/edge)
    Go to API Proxies
    You should see an API Proxy created by the PCF Service Broker- with the following name `cf-{your_initials}_helloapi.YOUR-SYSTEM-DOMAIN`
    Select the API and select `TRACE` tab on the top right
    Click on the `Start Trace Session`, the green button on the top left
    Send a request to the same endpoint, as you did in step 2 - by copy/pasting the URL under the urls section of your app, to a new browser tab. `https://{URL OF YOUR APP}`
	
      
    If you forgot the URL OF YOUR APP, you can get if through the following command (the output will have a urls section corresponding to your app)
    ```
    cf apps
    ```
    This time, when you send the request, the requst should show up in the trace of your API Proxy.
    In essence, the API you have created from PCF is now managed by Apigee. oh, yeah! *Congratulations!*...
    What does this mean
    - You have analytics across all your APIs, created through PCF
    - You can add authentication, traffic management and few more directly from your cf CLI, without logging into Apigee
    - When you do that the business teams can create API Products, and scale the consumption
    - If you have swagger spec for this API, you can enable your developers to access these APIs through smartdocs
    
4. Where to from here
    We will add more labs on Apigee PCF Usecases. 
    - You can add authentication, traffic management and few more directly from your cf CLI, without logging into Apigee
    - You can choose to have these APIs proxied by Apigee Edge Microgateway, instead of the central runtime
    - You can choose to decorate your App, with the Apigee Edge Microgateway meta build pack
    - If you are using Apigee Private cloud, you can choose to manage the private cloud through PCF's BOSH


# Earn Extra-points

Try deploying the PCF App to a diffrent environment.

# Quiz

1. What are different ways in which Apigee can help apps created/deployed in PCF?


# Summary

In this lab you have added API Management to an API created in PCF.

# References

* Link to Apigee docs page

    * OAuth 2.0: Configuring a new API proxy [http://docs.apigee.com/api-services/content/understanding-default-oauth-20-configuration ](http://docs.apigee.com/api-services/content/understanding-default-oauth-20-configuration)

    * Secure an API with OAuth [http://docs.apigee.com/tutorials/secure-calls-your-api-through-oauth-20-client-credentials](http://docs.apigee.com/tutorials/secure-calls-your-api-through-oauth-20-client-credentials) 

* [Link](https://community.apigee.com/topics/oauth+2.0.html) to Community posts and articles with topic as "OAuth 2.0" 

* Search and Revoke tokens - [https://community.apigee.com/articles/1571/how-to-enable-oauth-20-token-search-and-revocation.html](https://community.apigee.com/articles/1571/how-to-enable-oauth-20-token-search-and-revocation.html)

# Rate this lab

How did you like this lab? Rate [here](https://docs.google.com/forms/d/e/1FAIpQLSf048tEsGEfy6f6B0dd3ujg5MrkBgmcXKf9zVzgIubHtTEwnw/viewform?c=0&w=1).

