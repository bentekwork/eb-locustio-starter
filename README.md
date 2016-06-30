# AWS Elastic Beanstalk Load Generator Starter
This starter application uses the [Locust](http://locust.io/) open source load testing tool to create a simple load generator for your applications. The sample test definition *[locustfile.py](locustfile.py)* tests the root of an endpoint passed in as an environment variable *(TARGET_URL)*. For more information on the format of the test definition file, see [Writing a locustfile](http://docs.locust.io/en/latest/writing-a-locustfile.html).

If or when you change this file for deployment to the Elastic Beanstalk instance, it is imperative that you do NOT commit it back into your repo. Test files will often have information (test user accounts, etc...) that should not be public knowledge. At this point in time, this repo isn't locked. Also, please be sure to fork from this repo for each test you're going to do. Thanks!

You can get started using the following steps:
  1. [Install the AWS Elastic Beanstalk Command Line Interface (CLI)](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html).
  2. Create an IAM Instance Profile named **aws-elasticbeanstalk-locust-role** with the policy in [policy.json](policy.json). For more information on how to create an IAM Instance Profile, see [Create an IAM Instance Profile for Your Amazon EC2 Instances](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-iam-instance-profile.html).
  3. Run `eb init -r <region> -p "Java 8"` to initialize the folder for use with the CLI. Replace `<region>` with a region identifier such as `us-west-2` (see [Regions and Endpoints](https://docs.amazonaws.cn/en_us/general/latest/gr/rande.html#elasticbeanstalk_region) for a full list of region identifiers). For interactive mode, run `eb init` then,
    1. Pick a region of your choice.
    2. Select the **[ Create New Application ]** option.
    3. Enter the application name of your choice.
    4. Answer **no** to *It appears you are using Python. Is this correct?*.
    5. Select **Java** as the platform.
    6. Select **Java 8** as the platform version.
    7. Choose whether you want SSH access to the Amazon EC2 instances.  
      *Note: If you choose to enable SSH and do not have an existing SSH key stored on AWS, the EB CLI requires ssh-keygen to be available on the path to generate SSH keys.*  
  4. Run `eb create -i c3.large --scale 1 --envvars TARGET_URL=<test URL> --instance_profile aws-elasticbeanstalk-locust-role` to begin the creation of your load generation environment. Replace `<test URL>` with the URL of the web app that you want to test.
    1. Enter the environment name of your choice.
    2. Enter the CNAME prefix you want to use for this environment.
  5. Once the environment creation process completes, run `eb open` to open the [Locust](http://locust.io/) dashboard and start your tests.
  6. To make changes to the test definition, edit the *[locustfile.py](locustfile.py)*, save and commit the changes, and run `eb deploy`.
  7. If you'd like to scale out the environment to more than 1 EC2 instance,
    1. Run `eb config`, it'll open the configuration file in the default editor.
    2. Set the `BatchSize` option under the `aws:elasticbeanstalk:command` namespace to `100`.
    3. Save the configuration file and exit the editor. This will start the environment update.
    4. After the update completes, run `eb scale <number of instances>`. Replace `<number of instances>` with the number of EC2 instances you would like the environment to scale out to.
    5. After the additional instances have been added, run `eb deploy` to reselect the master instance.
    6. Run `eb open` to open the [Locust](http://locust.io/) dashboard and start your tests.
  8. When you are done with your tests, run `eb terminate --all` to clean up.

*Note: Running Locust in distributed mode requires a master/slave architecture. The sample application selects a master on each deployment, which requires `eb deploy` to be run after you change the number of instances running in the environment. Also, this sample requires that the auto scaling minimum and maximum be set to the same value*
