---
layout: default
---

## Welcome to the Amazon ECS-Weave-Shippable Booth Challenge!

In this challenge, you'll set up fully automated deployments of the front-end
component for the containerized [Socks Shop eCommerce](https://microservices-demo.github.io/) application, in 30 minutes or less.

The sample application will feature:

  * **Amazon ECS**{: style="color: orange"} for container orchestration
  * **Amazon ECR**{: style="color: orange"} for container registry
  * **Weave Scope**{: style="color: orange"} for service discovery and container visualization
  * **Shippable**{: style="color: orange"} for automated CI/CD

To complete the challenge, you will:

  * Configure a CI/CD pipeline and deploy the <a href="https://github.com/microservices-demo/front-end" style="color: orange">
  front-end</a> service using Shippable
  * Explore the Amazon ECS, Weave Scope, and Shippable services
  * Register for the prize drawing

Stop by the `Shippable booth (#1243)` or the `Weave booth (#1140)` at any time during
expo hours for assistance.

---

### Fork and clone the repos
To get started, you'll need to fork and clone two GitHub repos.

1. Fork the [pipelines](https://github.com/aws-weave-ship-challenge/pipelines){: style="color: orange"}
GitHub repo to your personal [GitHub account](https://github.com/join?source=header-home){: style="color: orange"}

2. <p>Clone your fork locally on your machine</p>  
  * Click the green `Clone or download` button
  * Copy the URL for your forked repo that appears
  * Open a command line on your local machine and execute the command:
      <pre>$ git clone <i>your_forked_url</i></pre>

{:start="3"}
3. <p>Repeat steps 1 and 2 for the <a href="https://github.com/aws-weave-ship-challenge/front-end"
style="color: orange">
front-end</a> repo

---

### Configure an automated CI/CD pipeline

A Weave-enabled Amazon ECS cluster is already running all of the services
of the Socks Shop application, except one. You'll use Shippable to set up an automated CI/CD pipeline to deploy the <a href="https://github.com/microservices-demo/front-end" style="color: orange">
front-end</a> service.

1. Create a <a href="https://app.shippable.com/login.html" style="color: orange">Shippable</a> account using your GitHub credentials

2. <p>Enable the <span style="color: orange">front-end</span> repo for <span style="color: orange">
CI</span> in Shippable</p>
    * In your local copy of the `front-end` repo, make the image tag you'll push
    unique by updating the `IMAGE_TAG` environment variable in the shippable.yml
    file with your last name:
    * Replace `yourName` with your last name:
      <pre>
      env:
        global:
          - IMAGE_TAG=yourName.$BRANCH.$BUILD_NUMBER
      </pre>

    * Commit and push your changes to Github
      <pre>
      $ git commit -am 'update shippable.yml with my last name'
      $ git push origin master
      </pre>
    * Select your Subscription from the drop-down menu (three horizontal lines)
    in upper left
    * Select `Enable project` in left-hand nav
    * Find the `front-end` repo in the list and select `Enable`
    * Verify that your `front-end` project has been enabled:
    ![front-end-enable](../assets/img/shippable-pipeline-2.png){:width="600px"}

{:start="3"}
3. <p>Configure an integration between <span style="color: orange">Shippable</span>
 and <span style="color: orange">Amazon ECR</span></p>
  * Navigate to `Account Settings` via the gear icon in the upper right
  * Select `Integrations` tab
  * Select `Add Integration`
    * Select `Amazon ECR` from the list and complete the fields, as follows:
      * Integration Name: name your integration `shippable-ecr`
      * For this challenge, you'll use a pre-configured user with keys AKIAJN2MBNFZ5QO5K6BQ and TtS/ic0hQYQubWasp3Qht0xbiIoLvjqYH/9YZo0J
      * Copy/paste the Aws_access_key_id and Aws_secret_access_key into the
      Shippable fields
      * Select `Save`
  * Now, assign your Account Integration for use by your Subscription
    * Select your Subscription from the dropdown menu in upper left (three
      horizontal lines)
    * Select `Settings` tab, `Integrations` tab, and `Add Integration`
    * Complete the fields with the following values:
      * Name: shippable-ecr
      * Account Integrations: select `shippable-ecr` from the list
      * Project Permissions: leave `All projects` selected
      * Select `Save`
      <p></p>  
  ![sub-integration-ecr](../assets/img/shippable-pipeline-4.png){:width="600px"}

{:start="4"}
4. <p>Configure an integration between <span style="color: orange">Shippable</span>
and <span style="color: orange">Amazon ECS</span></p>
  * Navigate to `Account Settings` via the gear icon in upper right
  * Select `Integrations` tab
  * Select `Add Integration`
    * Select `AWS` from the list
    * Name your integration `shippable-aws`
    * Copy/paste the `Aws_access_key_id` and `Aws_secret_access_key` into the
    Shippable fields (use the same values from step 4 above)
    * Select `Save`
  * Now, assign your Account Integration for use by your Subscription
    * Select your Subscription from the dropdown menu in upper left (three lines)
    * Select `Settings` tab, `Integrations` tab, and `Add Integration`
    * Complete the fields with the following values:
      * Name: shippable-aws
      * Account Integrations: select `shippable-aws` from the list
      * Project Permissions: leave `All projects` selected
      * Select `Save`

{:start="5"}
5. <p>Create the <span style="color: orange">front-end CD pipeline</span></p>
In your local copy of the `pipelines` repo, you'll need to update
the `shippable.resource.yml` configuration file with a unique port mapping for
the service you'll deploy:
    * Resource `img-opts-front-end-test`

      <pre>
      # Docker image options for TEST environment
        - name: img-opts-front-end-test
          type: dockerOptions
          version:
            portMappings:
              - 40000:40000
      </pre>
      * Replace the portMappings with a port number between 40001-49999,  e.g. `40833:40833`
      * When accessing your Test instance of your front-end service, you'll do so
      at SockShopALB-351062557.us-east-1.elb.amazonaws.com:`{your test port number}`, e.g. SockShopALB-351062557.us-east-1.elb.amazonaws.com:40833
    * Resource `params-front-end-test`

      <pre>
      # Environment variables for TEST environment
        - name: params-front-end-test
          type: params
          version:
            params:
              ENVIRONMENT: "development"
              NODE_ENV: "development"
              PORT: 40000
      </pre>
      * Replace the value for `PORT` to match the value you supplied in `portMappings` above, e.g. `40833`
      <p></p>
    * Resource `img-opts-front-end-prod`

      <pre>
      # Docker image options for PROD environment
        - name: img-opts-front-end-prod
          type: dockerOptions
          version:
            portMappings:
              - 50000:50000
      </pre>
      * Replace the portMappings with a port number between 50000-59999,  e.g. `50833:50833`
      * When accessing your Test instance of your front-end service, you'll do so
      at SockShopALB-351062557.us-east-1.elb.amazonaws.com:`{your prod port number}`, e.g. SockShopALB-351062557.us-east-1.elb.amazonaws.com:50833      
    * Resource `params-front-end-prod`

      <pre>
      # Environment variables for PROD environment
        - name: params-front-end-prod
          type: params
          version:
            params:
              ENVIRONMENT: "production"
              NODE_ENV: "production"
              PORT: 50000
      </pre>
      * Replace the value for `PORT` to match the value you supplied in
      `portMappings` above, e.g. `50833`
      <p></p>

      Now, update the `shippable.resource.yml` configuration file with your
      unique port mapping for the service you'll deploy:

      * Resource `yourName-ecs-deploy-test-front-end`

        <pre>
        # TEST deployment to Amazon ECS
          - name: yourName-ecs-deploy-test-front-end
            type: deploy
            steps:
              - IN: man-front-end
              - IN: params-front-end-test
              - IN: trigger-front-end-test
              - IN: cluster-demo-ecs
              - IN: alb-front-end-test
                applyTo:
                  - manifest: man-front-end
                    image: img-front-end
                    port: 40000
              - TASK: managed
        </pre>
        * Replace `yourName` in the `name` value with your last name, e.g.
        `name: smith-ecs-deploy-test-front-end`
        * Replace the `port` value with your Test port number from above, e.g.
        `40833`
        <p></p>
      * Resource `yourName-ecs-deploy-prod-front-end`

        <pre>
        # PROD deployment to Amazon ECS
          - name: yourName-ecs-deploy-prod-front-end
            type: deploy
            steps:
              - IN: yourName-ecs-deploy-test-front-end
                switch: off
              # - IN: release-front-end
              #   switch: off
              - IN: img-opts-front-end-prod
              - IN: params-front-end-prod
              - IN: replicas-front-end-prod
              - IN: trigger-front-end-prod
              - IN: alb-front-end-prod
                applyTo:
                  - manifest: man-front-end
                    image: img-front-end
                    port: 50000
              - IN: cluster-demo-ecs
              - TASK: managed        </pre>
        * Replace `yourName` in the `name` value with your last name, e.g.
        `name: smith-ecs-deploy-test-front-end`
        * Replace `yourName` in the first `IN` value with your last name, e.g.
        `IN: smith-ecs-deploy-test-front-end`
        * Replace the `port` value with your Prod port number from above, e.g.
        `50833`
        <p></p>

    Now, load your Pipeline configuration files into Shippable:

    * Select the `Pipelines` tab, `Resources` view, and then `Add Resource`
    button (far right)
    * In the Subscription Integrations dropdown: choose `Add integration` and
    complete the fields, as follows:
      * Name: name your integration `github`
      * Account Integrations: select `github` from the list
      * Projects Permissions: leave as `All projects`
      * Select `Save`
    * Select Project dropdown: choose `pipelines` project
    * Select Branch dropdown: choose `master`
    * Select `Save`
    * Select `SPOG` view and verify that your pipeline has been loaded
    ![pipeline-load](../assets/img/shippable-pipeline-3.png){:width="600px"}

{:start="6"}
6. <p>Link <span style="color: orange">CI</span> to your <span style="color: orange">
Pipeline</span> via an <span style="color: orange">Event Trigger</span></p>
  * Navigate to `Account Settings` via the gear icon in upper right
  * Navigate to the 'API tokens' tab, create an API Token, and save it (you'll need
  it again shortly)
  * Select `Integrations` tab
  * Select `Add Integration`
    * Select `Event Trigger` from list
    * Name your integration `trigger-img-front-end`
    * Select `Resource` in the `Select Trigger` dropdown
    * Select the `img-front-end` resource you created in your pipeline
    * In Authorization field, enter 'apiToken ' + your API token from above
    * Select `Save`
    ![acct-integration-trigger](../assets/img/shippable-pipeline-6-1.png){:width="600px"}  
  * Now, assign your Account Integration for use by your Subscription
    * Select your Subscription from the dropdown menu in upper left
    * Select `Settings` tab, `Integrations` tab, and `Add Integration`
    * Complete the fields with the following values:
      * Name: trigger-img-front-end
      * Account Integrations: select `trigger-img-front-end` from the list
      * Project Permissions: leave `All projects` selected
      * Select `Save`
      <p></p>
    ![sub-integration-trigger](../assets/img/shippable-pipeline-6-2.png){:width="600px"}

{:start="7"}
7. <p>Run CI and trigger deployment of the `front-end` service to the
<span style="color: orange">Test environment</span></p>
  * Select the `CI` tab
  * Select the `Build` button for the `front-end` project
  * View the CI console as your CI run executes
  ![ci-front-end](../assets/img/shippable-pipeline-7-1.png){:width="600px"}
  * Navigate to the `Pipelines` tab and see your Pipeline execute
    * You'll see the CI job run and push a new image to Amazon ECR
    * Then a new Manifest job will run to update with the newest image tag
    * Then a Deploy job will run to deploy to Amazon ECS
  ![pipeline-test](../assets/img/shippable-pipeline-7-2.png){:width="600px"}
  * View your application running your `Test` front-end in your browser at
  `SockShopALB-351062557.us-east-1.elb.amazonaws.com:8080`
  ![front-end-test](../assets/img/shippable-pipeline-7-3.png){:width="600px"}

{:start="8"}
8. <p>Deploy to the <span style="color: orange">Prod environment</span></p>
  * Right-click the `ecs-deploy-prod` job and select `Run`
  * A Deploy job will run and deploy a Prod instance of `front-end` to Amazon ECS
  * View your application running your `Prod` front-end in your browser at
  `SockShopALB-351062557.us-east-1.elb.amazonaws.com` (it should look identical to your `Test` front-end)

{:start="9"}
9. <p>Make a change to your front-end service and <span style="color: orange">
auto-deploy to the Test environment</span></p>
  * In your editor, open the `public/css/style.blue.css` file for the `front-end` repo
  * Toggle lines 1273 and 1274 (i.e. comment out line 1273, and un-comment line 1274 or
    vice-versa). This will switch the color of the active tab on the home page
    between blue and green.
    * Commit your changes to GitHub
      <pre>
      $ git commit -am 'update color of active tab on front-end home page'
      $ git push origin master
      </pre>
  * View the automated CI/CD flow in Pipeline view in Shippable, which will result
  in the code change being deployed to your Test environment
  * In your browser, navigate again to your Test environment (
    `SockShopALB-351062557.us-east-1.elb.amazonaws.com:8080`)
    and confirm that the change was deployed successfully
  ![front-end-test-2](../assets/img/shippable-pipeline-9.png){:width="600px"}

{:start="10"}
10. <p>Explore!</p>
  * Navigate to `http://54.166.157.73:4040` to view the Weave visualization of
  your containerized application. Click around to see various info on your services.
  ![weavescope](../assets/img/weavescope-10-1.png){:width="600px"}  
  * Login with username `booth` and password `Challenge2016` <a href="https://betaship.signin.aws.amazon.com/console"
  style="color: orange">to the AWS Management Console</a>
  * Explore <a href="https://console.aws.amazon.com/ecs/home#/clusters/ecs-weave-shippable-demo/services" style="color: orange">
  the different elements of the cluster in Amazon ECS</a>
  ![amazon-ecs](../assets/img/amazon-ecs-10-2.png){:width="600px"}
  * Navigate to Amazon ECR repository to view your newly created Docker images
  * Select `Repositories` in the left-hand nav from your cluster page
  ![amazon-ecr](../assets/img/amazon-ecr-10-3.png){:width="600px"}

    * Explore additional elements of your Shippable Pipelines:
      * Select the `Jobs` view in the Pipelines tab and click on the `Latest Version`
      number for the `ecs-deploy-test-front-end` job.
      * For the most recent version, select `More` and `Trace` to see details of the
      elements included in this latest deployment to the Test environment
      * Expand the `man-front-end` Resource Name
  ![pipeline-trace](../assets/img/shippable-pipeline-10-4.png){:width="600px"}

---

### Register for the prize drawing!
When finished exploring, return to the `Shippable booth (#1243)` and show your
pipeline to register for the prize drawing and be eligible to win one of these
great prizes:

  * MacBook Air
  * Vive VR Headset
  * Bose SoundLink
  * Amazon Echo Dot
