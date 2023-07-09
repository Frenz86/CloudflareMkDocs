## Create a Dockerfile

Cloud Run works closely with [Cloud Build](https://lynn-kwong.medium.com/build-a-docker-image-with-cloud-build-in-google-cloud-platform-5f6840af2c05) which builds Docker images from your GitHub repositories automatically. Cloud Run uses the Docker images built by Cloud Build to spin up your applications. Therefore, to use Cloud Run, you should also have the Cloud Build API enabled.

The Dockerfile provided by the [official documentation](https://cloud.google.com/run/docs/quickstarts/build-and-deploy/deploy-python-service) of Cloud Run is a good example and can work as it is in most cases:

<iframe src="https://betterprogramming.pub/media/fc1da66d39f49b637d604a3529c2dac8" allowfullscreen="" frameborder="0" height="496" width="692" title="6967d7c7d42a-cloud-run-flask.Dockerfile" class="fj aq as ag cf" scrolling="auto" style="box-sizing: inherit; height: 496px; top: 0px; left: 0px; width: 692px; position: absolute;"></iframe>

This Dockerfile has good documentation and is straightforward to understand. Especially, the **PORT** virtual environment will be passed in by Cloud Run automatically, as we will see soon when we set up Cloud Run.

Besides, it’s also helpful to specify a `.dockerignore` file to exclude some metadata or temporary files from your Docker image. It can make your image smaller and look cleaner. A sample `.dockerignore` file can contain the following contents:

<iframe src="https://betterprogramming.pub/media/6f8e5c89067b910b9f99b0fb91e99109" allowfullscreen="" frameborder="0" height="457" width="692" title="6967d7c7d42a-cloud-run-flask.dockerignore" class="fj aq as ag cf" scrolling="auto" style="box-sizing: inherit; height: 456.991px; top: 0px; left: 0px; width: 692px; position: absolute;"></iframe>

## Deploy to Cloud Run

You can deploy your Flask application locally with the following command:

```
$ gcloud run deploy
```

Just follow the instructions to deploy your application step by step.

However, if you want to have more control over the deployment procedure, it’s better to use the GCP console. And if you want to set up a continuous deployment (CD) pipeline, it will be much more straightforward to do it in the console.

So let’s head over to the Cloud Run page in GCP Console. You can find Cloud Run on the left navigation bar, or just search for it in the top search box. Then click “CREATE SERVICE” to create a new Cloud Run service:

![img](https://miro.medium.com/max/1225/1*bwrKTa29L4xAQ9L7R4rmdw.png)

On the page opened, since we want to set up a continuous deployment pipeline, click the second option to “Continuously deploy new revisions from a source repository”:

![img](https://miro.medium.com/max/1124/1*VaEWSC1dhN0Fn5iRWBWrvw.png)

Then click “SET UP WITH CLOUD BUILD” to choose a GitHub repository for Cloud Run. This repository will contain the source code and the configurations to build and deploy your application.

If you want to follow, you can fork [this repository](https://github.com/lynnkwong/flask-cloud-run-app) to your GitHub account and proceed to the following steps. Alternatively, you can add the Dockerfile file as demonstrated above to your own repository.

If it’s the first time you deploy a Cloud Run service from a GitHub repository, you would need to install the “Google Cloud Build” tool in your GitHub repository:

![img](https://miro.medium.com/max/991/1*DaEeNjNHE0Ya95l7XccNuA.png)

When you click “INSTALL GOOGLE CLOUD BUILD”, a new window will be opened and you will be prompted to install “Google Cloud Build” in all your GitHub repositories or a specific repository. Select the one your want and click “Install” to install it:

![img](https://miro.medium.com/max/1047/1*2Z307jKroiTkaZyngB0lBQ.png)

You will be asked to authenticate your GitHub account. After that, you will be able to see your GitHub repository in Cloud Build. Select it and accept the agreement for using your GitHub repository in your GCP project:

![img](https://miro.medium.com/max/1006/1*6RCN2_YxGXB2PzuuOpbYiw.png)

Click “Next” to specify the “Build Configuration”. You would need to choose the branch against which Cloud Build will run. Besides, you need to specify a Build Type. We will use a Dockerfile to build our application. Both options can be left as default in most cases.

![img](https://miro.medium.com/max/985/1*ai7KLs1wMOHmbsLJtsJgpw.png)

Click “SAVE” to save the configurations for Cloud Build.

Then you need to specify some basic configurations for Cloud Run. Again, the default configurations would be enough in most cases:

![img](https://miro.medium.com/max/1019/1*P02mvue8p9h873bgPx8TZg.png)

The options are quite self-explanatory and have helpful tips as well. This is why the Console is recommended for deployment, especially for beginners. You can have more understanding and more control over what you are doing, rather than treat it as a black box.

Especially, The “CPU allocation and pricing” option can increase the bills dramatically. If you choose the default optional “CPU is only allocated during request processing”, then normally you don’t have to pay for it if you don’t have a lot of traffic. However, If you choose “CPU is always allocated”, you would need to pay quite some bucks every month. Please do check [Google Cloud Pricing Calculator](https://cloud.google.com/products/calculator) for more details.

As you may remember, we read an environment variable `APP_NAME` in our Flask application. Let’s set a value for it and see if the value can be read by Cloud Run. To do this, expand “Container, Variables & Secrets, Connections, Security” and click the “VARIABLES & SECRETS” tab. Add an environment `APP_NAME` with a value you like, say “My First Cloud Run Service”.

![img](https://miro.medium.com/max/1073/1*R_0bkdqpgItt_5A4DcL0tQ.png)

Then click “CREATE” to create your service. It will take less than one minute for Cloud Build to build your Docker container before it can be used in your Cloud Run service.

When the deployment is done, you will land on a page like the one below. You can check the metrics, logs, revisions, etc on this page. If something is wrong with your service, the logs are always the first place to check.

![img](https://miro.medium.com/max/1225/1*7vTTWps6IuVzntCnnR8vaQ.png)

Now if you click on the URL on this page, you should be able to open the simple website built by Flask:

![img](https://miro.medium.com/max/1195/1*FyGwmoM45HycHOi3cnP0EA.png)

Congratulations! Your CI/CD pipeline for your Flask web application has been built in Cloud Run. Now if you make some changes on the main branch and push the code to GitHub, a new Docker image will be automatically built and a new revision of Cloud Run will be deployed automatically for you.