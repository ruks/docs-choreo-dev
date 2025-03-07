# Develop a REST API

Choreo is a platform that allows you to create and deploy applications in any language.  In this guide, you will use Choreo to create a service component that exposes a REST API in Go. You are not required to have any prior knowledge of the Go language to follow the guide. 

A REST API is a web service that follows the REpresentational State Transfer (REST) principles of using HTTP methods to access and manipulate resources. In this guide, you create a containerized service component in Go, deploy it on Choreo, and later use it in a client application.

In this guide, you will:

- Build a simple greeting service. The greeter service has a single resource named “greet” and accepts a single query parameter as input.
- Deploy it in Choreo using a Dockerfile. It runs on port 9090.
- Test the service.

## Prerequisites

1. To deploy a containerized component, you will need a GitHub account with a repository that contains a Dockerfile. Fork the [Choreo examples repository](https://github.com/wso2/choreo-sample-apps/), which contains the sample for this guide.
2. The Choreo GitHub App requires the following permissions:
    - Read access to issues and metadata
    - Read and write access to code, pull requests, and repository hooks.

### Learn the repository file structure

Let's familiarize ourselves with the key files in the sample greeter application. The below table gives a brief overview of the important files in the greeter service.

!!! note 
    The following file paths are relative to the path <sample-repository-dir>/go/greeter

|Filepath               |Description                                                                   |
|-----------------------|------------------------------------------------------------------------------|
| main.go               | The Go-based Greeter service code.
| Dockerfile            | Choreo uses the Dockerfile to build the container image of the application.  |
|.choreo/endpoints.yaml | Choreo-specific configuration that provides information about how Choreo exposes the service.|
|openapi.yaml           | OpenAPI contract of the greeter service. This is needed to publish our service as a managed API. This openapi.yaml file is referenced by the .choreo/endpoints.yaml.|

Let's get started!

## Step 1: Create a service component from a Dockerfile

Let's create a containerized service component by following these steps:

1. Sign in to the Choreo Console at [https://console.choreo.dev](https://console.choreo.dev).
2. Create a project to add the service component. You can follow the instructions under Prerequisites in the Connect Your Own GitHub Repository to Choreo guide.
3. On the Components page, click Create on the Service card.
4. Enter a unique name and a description of the service. For this guide, let's enter the following values:

    |Field          |     Value              |
    |---------------|------------------------|
    |Name           | Greetings              |
    |Description    | Sends greetings        |

5. Click Next.
6. To allow Choreo to connect to your GitHub account, click **Authorize with GitHub**.
7. If you have not already connected your GitHub repository to Choreo, enter your GitHub credentials, and select the repository you created in the prerequisites section to install the [Choreo GitHub App](https://github.com/marketplace/choreo-apps).

    !!! info
         The **Choreo GitHub App** requires the following permissions:<br/><br/>- Read and write access to code and pull requests.<br/><br/>- Read access to issues and metadata.<br/><br/>You can [revoke access](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/reviewing-your-authorized-integrations#reviewing-your-authorized-github-apps) if you do not want Choreo to have access to your GitHub account. However, write access is only used to send pull requests to a user repository. Choreo will not directly push any changes to a repository.


8. In the **Connect Repository** pane, enter the following information:

    | **Field**             | **Description**                               |
    |-----------------------|-----------------------------------------------|
    | **GitHub Account**    | Your account                                  |
    | **GitHub Repository** | choreo-sample-apps |
    | **Branch**            | **`main`**                               |
    | **Build Preset**      | Dockerfile|
    | **Dockerfile Path**       | go/greeter/Dockerfile |
    | **Docker Context path**              | go/greeter |

    !!! info
        1.  To successfully build your container with Choreo, it is essential to explicitly define a User ID (UID) under the USER instruction in your Dockerfile. For reference, see [sample Dockerfile](https://github.com/wso2/choreo-sample-apps/blob/main/go/greeter/Dockerfile).
        To ensure that the defined USER instruction is valid, it must conform to the following conditions:
            - A valid User ID is a numeric value between 10000-20000, such as `10001` or `10500`.
            - Usernames are considered invalid and should not be used. For example, `my-custom-user-12221` or `my-custom-user` are invalid User IDs.

        2. The Dockerfile utilized in this guide is a Multi-stage Dockerfile, which is designed to keep the final image size small and provides the ability to build the application with a specific version of tools and libraries.

9. Click Create. Once the component creation is complete, you will see the component overview page.

You have successfully created a Service component from a Dockerfile. Now let's build and deploy the service.

## Step 2: Configure the service port with endpoints

We expect to run our greeter service on port 9090. To securely expose the service through Choreo, we must provide the port and other required information to Choreo. In Choreo, we expose our services with endpoints. You can read more about endpoints in our [endpoint documentation](https://wso2.com/choreo/docs/develop-components/develop-services/develop-a-service/#what-are-endpoints-in-service-components).

Choreo looks for an endpoints.yaml file inside the `.choreo` directory to configure the endpoint details of a containerized component. Place the `.choreo` directory at the root of the Docker build context path.

In our greeter sample, the endpoints.yaml file is at go/greeter/.choreo/endpoints.yaml. 

## Step 3: Build and deploy
Now that we have connected the source repository, and configured the endpoint details, it's time to build and deploy the greeter service.

To build and deploy the service, follow these steps:

1. On the Deploy page, click **Deploy Manually**.

    !!! note
        Deploying the service component may take a while. You can track the progress by observing the logs. Once the deployment is complete, the deployment status changes to Active in the corresponding environment card.

2. Check the deployment progress by observing the console logs on the right of the page.
    You can access the following scans under **Build**. 

    - **The Dockerfile scan**: Choreo performs a scan to check if a non-root user ID is assigned to the Docker container to ensure security. If no non-root user is specified, the build will fail.
    - **Container (Trivy) vulnerability scan**: This detects vulnerabilities in the final docker image. 
    -  **Container (Trivy) vulnerability scan**: The details of the vulnerabilities open in a separate pane. If this scan detects critical vulnerabilities, the build will fail.

!!! info
    If you have Choreo environments on a private data plane, you can ignore these vulnerabilities and proceed with the deployment.

Once you have successfully deployed your service, you can [test](../../testing/test-rest-endpoints-via-the-openapi-console.md), [manage](../../api-management/lifecycle-management.md), and [observe](../../monitoring-and-insights/observability-overview.md) it like any other component type in Choreo.

To perform a more detailed diagnosis of this Dockerfile-based REST API by viewing Kubernetes-level insights, see [Choreo's DevOps capabilities](../../devops-and-ci-cd/view-runtime-details.md).


