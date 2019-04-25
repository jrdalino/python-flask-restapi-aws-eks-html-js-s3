# python-calculator-rest-api-docker-kubernetes

## Prerequisites
Python
```
$ Python3 --version
```
Flask
```
$ flask --version
```
Docker
```
$ docker -v
```
Git
```
$ git --version
```
AWS CLI
```
$ aws --version
```
Working Directory
```
$ cd ~
$ mkdir environment
$ cd ~/environment
```

## Module 1: Backend Python Flask Rest API for Calculator Local
- Basic calculations (add, subtract, multiply, divide)
- Advanced calculations (square root, cube root, power, factorial)
- Calculator triggered by developers via a web api
- References: 
- https://github.com/ajportier/rest-calculator
- https://blog.miguelgrinberg.com/post/designing-a-restful-api-using-flask-restful
- TODO: Use Flask-RESTful https://blog.miguelgrinberg.com/post/designing-a-restful-api-using-flask-restful

```
HTTP METHOD | URI                                                         | Action
-----------   -----------------------------------------------------------   --------------------------------------------------
POST        | http://[hostname]/add {"argument1":a, "argument2":b }       | Adds two numbers (a + b)
POST        | http://[hostname]/subtract {"argument1":a, "argument2":b }  | Subracts two numbers (a - b)
POST        | http://[hostname]/multiply {"argument1":a, "argument2":b }  | Multiplies two numbers (a * b)
POST        | http://[hostname]/divide {"argument1":a, "argument2":b }    | Divides two numbers (a / b)
POST        | http://[hostname]/squareroot {"argument1":a }               | Gets the square root of a number (a)
POST        | http://[hostname]/cuberoot {"argument1":a }                 | Gets the cube root of a number (a)
POST        | http://[hostname]/exp {"argument1":a, "argument2":b }       | Gets the the exponent of a raised to b
POST        | http://[hostname]/factorial {"argument1":a }                | Get the factorial of number 5! = 5 * 4 * 3 * 2 * 1 
```

### Module 1.1 Create and Navigate to Directory
```
$ mkdir calculator-rest-api
$ cd calculator-rest-api
$ virtualenv flask
$ flask/bin/pip install flask
```

### Module 1.2 Add calculator.py
```
$ vi calculator.py
```
```
#!/usr/bin/env python
from flask import (Flask, jsonify, request, abort, render_template)
import math

app = Flask(__name__)

@app.route('/')
def index_page():
    return "This is a RESTful Calculator App built with Python Flask!"
#    return render_template('index.html')

@app.route('/add', methods=['POST'])
def add_args():
    if not request.json:
        abort(400)
    try:
        arg1 = request.json['argument1']
        arg2 = request.json['argument2']
        answer = arg1 + arg2
        return (jsonify({'answer':answer}), 200)
    except KeyError:
        abort(400)

@app.route('/subtract', methods=['POST'])
def subtract_args():
    if not request.json:
        abort(400)
    try:
        arg1 = request.json['argument1']
        arg2 = request.json['argument2']
        answer = arg1 - arg2
        return (jsonify({'answer':answer}), 200)
    except KeyError:
        abort(400)

@app.route('/multiply', methods=['POST'])
def multiply_args():
    if not request.json:
        abort(400)
    try:
        arg1 = request.json['argument1']
        arg2 = request.json['argument2']
        answer = arg1 * arg2
        return (jsonify({'answer':answer}), 200)
    except KeyError:
        abort(400)

@app.route('/divide', methods=['POST'])
def divide_args():
    if not request.json:
        abort(400)
    try:
        arg1 = request.json['argument1']
        arg2 = request.json['argument2']
        answer = arg1 / arg2
        return (jsonify({'answer':answer}), 200)
    except KeyError:
        abort(400)
    except ZeroDivisionError:
        abort(400)
        
# TODO Square Root
# TODO Cube Root
        
@app.route('/exp', methods=['POST'])
def exponent_args():
    if not request.json:
        abort(400)
    try:
        arg1 = request.json['argument1']
        arg2 = request.json['argument2']
        answer = arg1 ** arg2
        return (jsonify({'answer':answer}), 200)
    except KeyError:
        abort(400)

# TODO Factorial

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

### Module 1.3 Create the requirements.txt file
```
$ vi requirements.txt
```
```
flask
```

### Module 1.4 Run Locally and Test
```
$ chmod a+x calculator.py
$ ./calculator.py
$ curl http://localhost:5000
```

### Module 1.5: TODO - Backend Unit Tests
- References: http://liangshang.github.io/2014/01/17/a-simple-calculator-by-python-and-tdd


### Module 1.6 Create the Dockerfile
```
$ vi Dockerfile
```
```
FROM python:2.7
COPY . /calculator
WORKDIR /calculator
RUN pip install -r requirements.txt
ENTRYPOINT ["python"]
CMD ["calculator.py"]
```

### Module 1.7 Build, Tag and Run the Docker Image locally
Replace:
- AccountId: 707538076348
- Region: us-east-1

```
$ docker build . -t 707538076348.dkr.ecr.us-east-1.amazonaws.com/jrdalino/calculator-rest-api:latest
$ docker run -d -p 5000:5000 707538076348.dkr.ecr.us-east-1.amazonaws.com/jrdalino/calculator-rest-api:latest
```

### Module 1.8 Test Math Operations
- Test Add
```
$ curl -i -H "Content-Type: application/json" -X POST -d '{"argument1":2, "argument2":1 }' http://localhost:5000/add
{
  "answer": 3
}
```

- Test Subtract
```
$ curl -i -H "Content-Type: application/json" -X POST -d '{"argument1":4, "argument2":3 }' http://localhost:5000/subtract
{
  "answer": 1
}
```

- Test Mulitply
```
$ curl -i -H "Content-Type: application/json" -X POST -d '{"argument1":2, "argument2":3 }' http://localhost:5000/multiply
{
  "answer": 6
}
```

- Test Divide
```
$ curl -i -H "Content-Type: application/json" -X POST -d '{"argument1":12, "argument2":4 }' http://localhost:5000/divide
{
  "answer": 3
}
```

- TODO: Test Square Root
- TODO: Test Cube Root

- Test Power
```
$ curl -i -H "Content-Type: application/json" -X POST -d '{"argument1":2, "argument2":3 }' http://localhost:5000/exp
{
  "answer": 8
}
```

- TODO: Test Factorial


### Module 1.9 Create the ECR Repository
```
$ aws ecr create-repository --repository-name jrdalino/calculator-rest-api
```

OUTPUT
```
{
    "repository": {
        "registryId": "707538076348", 
        "repositoryName": "jrdalino/calculator-rest-api", 
        "repositoryArn": "arn:aws:ecr:us-east-1:707538076348:repository/jrdalino/calculator-rest-api", 
        "createdAt": 1556234899.0, 
        "repositoryUri": "707538076348.dkr.ecr.us-east-1.amazonaws.com/jrdalino/calculator-rest-api"
    }
}
```

### Module 1.10 Run login command to retrieve credentials for our Docker client and then automatically execute it (include the full command including the $ below).
```
$ $(aws ecr get-login --no-include-email)
```

### Module 1.11 Push our Docker Image
```
$ docker push 707538076348.dkr.ecr.us-east-1.amazonaws.com/jrdalino/calculator-rest-api:latest
```

### Module 1.12 Validate Image has been pushed
```
$ aws ecr describe-images --repository-name jrdalino/calculator-rest-api:latest
```

### (Optional) Clean up
```
$ aws ecr delete-repository --repository-name jrdalino/calculator-rest-api --force
```

## Module 2: Frontend HTML, CSS, JS and Bootstrap for Calculator Local
- Calculator triggered by end users through a web page
### Modile 2.1 Create and Navigate to Directory
```
$ mkdir calculator-frontend
$ cd calculator-frontend
$ mkdir css
$ mkdir js
$ touch index.html
$ cd css
$ touch base.css
$ cd ..
$ cd js
$ touch querycalc.js
```

### Module 2.2 Create index.html file
```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Simple RESTful Calculator - Web Client</title>
    </head>
    <body>
        <div class="outer-container">
            <h1>Simple RESTful Calculator</h1>
            <form id="calcform" method="POST">
            <div class="arguments">
                Argument 1: <input type="text" id="argument1" value = ""/><br/>
                Argument 2: <input type="text" id="argument2" value = ""/><br/>
            </div>
            <div class="buttons">
                <div class="button-row">
                    <input class= "func-btn" type="button" id="add" value="Add"/>
                    <input class= "func-btn" type="button" id="subtract" value="Subtract"/>
                </div>
                <div class="button-row">
                    <input class= "func-btn" type="button" id="multiply" value="Multiply"/>
                    <input class= "func-btn" type="button" id="divide" value="Divide"/>
                </div>
                <div class="button-row">
                    <input class= "func-btn" type="button" id="exp" value="Exponent"/>
                </div>
            </div>
            </form>
        </div>

        <link rel="stylesheet" type="text/css" href="./css/base.css">
        <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
        <script src="./js/querycalc.js"></script>
        <script type="text/javascript">
            // once the DOM is ready start the calculator listener function
            $( document ).ready( calcListener ); 
        </script>

    </body>
</html>
```

### Module 2.3 Create base.css file
```
body {
    margin: 0;
    padding: 0;
    background-color: white;
}

.outer-container {
    width: 500px;
    height: 500px;
    margin-left: auto;
    margin-right: auto;
    overflow: auto;
    text-align: center;
    background-color: lightblue;
}

.arguments {
    margin: auto;
    padding: 5px;
}

.buttons {
    padding: 5px;
}

.button-row {
    height: 50px;
}

.func-btn {
	-webkit-appearance: none;
    width: 50%;
    height: 50px;
    float: left;
    padding: 5px;
}
```

### Module 2.4 Create querycalc.js file
```
function calcListener ( jQuery ) {
    console.log( "READY!" );

    $( "#add" ).click( function ( e ) {
        var arg1 = $( "#argument1" ).val();
        var arg2 = $( "#argument2" ).val();
        doMath (arg1, arg2, 'add');
        e.preventDefault();
    });

    $( "#subtract" ).click( function ( e ) {
        var arg1 = $( "#argument1" ).val();
        var arg2 = $( "#argument2" ).val();
        doMath (arg1, arg2, 'subtract');
        e.preventDefault();
    });

    $( "#multiply" ).click( function ( e ) {
        var arg1 = $( "#argument1" ).val();
        var arg2 = $( "#argument2" ).val();
        doMath (arg1, arg2, 'multiply');
        e.preventDefault();
    });

    $( "#divide" ).click( function ( e ) {
        var arg1 = $( "#argument1" ).val();
        var arg2 = $( "#argument2" ).val();
        doMath (arg1, arg2, 'divide');
        e.preventDefault();
    });
    
    $( "#exp" ).click( function ( e ) {
        var arg1 = $( "#argument1" ).val();
        var arg2 = $( "#argument2" ).val();
        doMath (arg1, arg2, 'exp');
        e.preventDefault();
    })
    
    function doMath( arg1, arg2, resource ) {
        var textStatus, jqXHR, errorThrown = '';

        console.log( "Calling " + resource + " on " + arg1 + " and " + arg2 );

        
        try {
            arg1 = Number( arg1 );
            arg2 = Number( arg2 );
            //TODO handle non-numeric inputs
            if ( isNaN(arg1) || isNaN(arg2) ) throw "NaN";

            // Flask requires a JSON string and the following content-type
            $.ajaxSetup({
                contentType: "application/json"
            });

            // Makes an ajax call to url "resource" supplying arg1 and arg2
            $.ajax({
                type: "POST",
                url: resource,
                data: JSON.stringify({ argument1: arg1, argument2: arg2 }),
                dataType: "json",
                success: function ( data ) {
                    var answer = String(data[ 'answer' ]);
                    console.log( "We got an answer! " + answer );

                    // Put the answer in argument1 and blank out argument2
                    $( "#argument1" ).val( answer );
                    $( "#argument2" ).val( '' );
                },
                error: function ( textStatus, jqXHR, errorThrown ) {
                    console.log("Something has gone wrong:" + errorThrown);
                    $( "#argument1" ).val( '' );
                    $( "#argument2" ).val( '' );
                }
            });
        }

        catch( err ) {
            console.log("Error: " + err);
            $( "#argument1" ).val( '' );
            $( "#argument2" ).val( '' );
        }
    }
    
}
```

### Module 2.5 Add default.conf file
```
$ vi default.conf
```
```
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

### Module 2.6 Create the Docker File
```
$ vi Dockerfile
```
```
FROM nginx:alpine
COPY default.conf /etc/nginx/conf.d/default.conf
COPY . /usr/share/nginx/html
```

### Module 2.7 Build and Run Locally
```
$ docker build -t calculator-frontend:latest .
$ docker run -d -p 8080:80 calculator-frontend:latest
```
### Module 2.8 Test User Interface
```
$ curl http://localhost:8080
```

### Module 2.9 Create the ECR Repository
```
$ aws ecr create-repository --repository-name jrdalino/calculator-frontend
```

### Module 2.10 Run login command to retrieve credentials for our Docker client and then automatically execute it (include the full command including the $ below).
```
$ $(aws ecr get-login --no-include-email)
```

### Module 2.11 Push our Docker Image
```
$ docker push 707538076348.dkr.ecr.us-east-1.amazonaws.com/jrdalino/calculator-frontend:latest
```

### Module 2.12 Validate Image has been pushed
```
$ aws ecr describe-images --repository-name jrdalino/calculator-frontend:latest
```

### (Optional) Clean up
```
$ aws ecr delete-repository --repository-name jrdalino/calculator-frontend --force
```

## Module 3: Configure Prerequisites for EKS
### Module 3.1: Create the default ~/.kube directory for storing kubectl configuration
```
$ mkdir -p ~/.kube
```

### Module 3.2: Install kubectl
```
$ sudo curl --silent --location -o /usr/local/bin/kubectl "https://amazon-eks.s3-us-west-2.amazonaws.com/1.11.5/2018-12-06/bin/linux/amd64/kubectl"
$ sudo chmod +x /usr/local/bin/kubectl
```

### Module 3.3: Install IAM Authenticator
```
$ go get -u -v github.com/kubernetes-sigs/aws-iam-authenticator/cmd/aws-iam-authenticator
$ sudo mv ~/go/bin/aws-iam-authenticator /usr/local/bin/aws-iam-authenticator
```

### Module 3.4 Install JQ and envsubst
```
$ sudo yum -y install jq gettext
```

### Module 3.5 Verify the binaries are in the path and executable
```
$ for command in kubectl aws-iam-authenticator jq envsubst
  do
    which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND"
  done
```

### Module 3.6 Clone the Frontend and Backend Sevice Repos
```
$ cd ~/environment
$ git clone https://github.com/jrdalino/calculator-frontend.git
$ git clone https://github.com/jrdalino/calculator-python.git
```

### Module 3.7 Update IAM Settings for your Workspace
- Reference: https://eksworkshop.com/prerequisites/workspaceiam/

### Module 3.7 Create an SSH Key
- Reference: https://eksworkshop.com/prerequisites/sshkey/

## Module 4: Launch EKS using EKCTL

### Module 4.1
```
$ cd ~/environment
$ git clone https://github.com/jrdalino/calculator-frontend.git
$ git clone https://github.com/jrdalino/calculator-python.git
```
### Module 4.2 Install EKSCTL prerequisites
```
$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
$ sudo mv -v /tmp/eksctl /usr/local/bin
$ eksctl version
```

### Module 4.3 Create an EKS Cluster (This will take ~15 minutes) and test cluster
```
$ eksctl create cluster --name=eksworkshop-eksctl --nodes=3 --node-ami=auto --region=${AWS_REGION}
```
```
$ kubectl get nodes
```

## Module 5: Deploy MicroServices to EKS
- containerized with Docker and Kubernetes for the orchestration

## Module 6: Setup CI/CD for Back End Service
- proper CI/CD processes to put in place
- Reference: https://eksworkshop.com/codepipeline/

### Module 6.1 Create an S3 Bucket for Pipeline Artifacts
```
$ aws s3 mb s3://jrdalino-workshop-artifacts
```

### Module 6.2 Modify S3 BUcket Policy
Replace:
- REPLACE_ME_CODEBUILD_ROLE_ARN
- REPLACE_ME_CODEPIPELINE_ROLE_ARN
- ArtifactsBucketName

```
$ vi ~/environment/modern-app-workshop/aws-cli/artifacts-bucket-policy.json
```

### Module 6.3 Grant S3 Bucket access to your CI/CD Pipeline
Replace:
- ArtifactsBucketName

```
$ aws s3api put-bucket-policy \
--bucket jrdalino-workshop-artifacts \
--policy file://~/environment/modern-app-workshop/aws-cli/artifacts-bucket-policy.json
```

### Module 6.4 Create a CodeCommit Repository
```
$ aws codecommit create-repository \
--repository-name MythicalMysfitsService-Repository
```

### Module 6.5 View/Modify Buildspec file
No need to modify for this workshop
```
$ vi ~/environment/modern-app-workshop/app/buildspec.yml
```

### Module 6.6: Modify CodeBuild Project Input File
Replace:
- REPLACE_ME_ACCOUNT_ID
- REPLACE_ME_REGION
- REPLACE_ME_CODEBUILD_ROLE_ARN
```
$ vi ~/environment/modern-app-workshop/aws-cli/code-build-project.json
```

### Module 6.7 Create the CodeBuild Project
```
$ aws codebuild create-project \
--cli-input-json file://~/environment/modern-app-workshop/aws-cli/code-build-project.json
```

### Module 6.8: Modify CodePipeline Input File
Replace:
- roleArn = REPLACE_ME_CODEPIPELINE_ROLE_ARN
- location = REPLACE_ME_ARTIFACTS_BUCKET_NAME
```
$ vi ~/environment/modern-app-workshop/aws-cli/code-pipeline.json
```

### Module 6.9: Create a pipeline in CodePipeline
```
$ aws codepipeline create-pipeline \
--cli-input-json file://~/environment/modern-app-workshop/aws-cli/code-pipeline.json
```

### Module 6.10: Modify ECR Policy
Replace:
- REPLACE_ME_CODEBUILD_ROLE_ARN = arn:aws:iam::486051038643:role/MythicalMysfitsServiceCodeBuildServiceRole
```
$ vi ~/environment/modern-app-workshop/aws-cli/ecr-policy.json
```

### Module 6.11: Enable automated Access to the ECR Image Repository
```
$ aws ecr set-repository-policy \
--repository-name mythicalmysfits/service \
--policy-text file://~/environment/modern-app-workshop/aws-cli/ecr-policy.json
```

### Module 6.12 Configure Git
```
$ git config --global user.name "REPLACE_ME_WITH_YOUR_NAME"
$ git config --global user.email REPLACE_ME_WITH_YOUR_EMAIL@example.com
$ git config --global credential.helper '!aws codecommit credential-helper $@'
$ git config --global credential.UseHttpPath true
$ cd ~/environment/
```

### Module 6.13 Clone Repository
```
$ git clone https://git-codecommit.ap-southeast-1.amazonaws.com/v1/repos/MythicalMysfitsService-Repository
```

### Module 6.14: Copy the application files into our repository directory
```
$ cp -r ~/environment/modern-app-workshop/app/* ~/environment/MythicalMysfitsService-Repository/
```

### Module 6.15 Make a small code change
```
$ vi ~/environment/MythicalMysfitsService-Repository/service/mysfits-response.json
```

### Module 6.16 Push the Code Change
```
$ cd ~/environment/MythicalMysfitsService-Repository/
$ git add .
$ git commit -m "I changed the age of one of the mysfits."
$ git push
```

## Module 7: Setup CI/CD for Front End Service (Same as Module 10)

## Module 8: Install Helm

### Module 8.1: Install Helm CLI
```
$ cd ~/environment
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
$ chmod +x get_helm.sh
$ ./get_helm.sh
```

### Module 8.2: Configure Helm access with RBAC
```
cat <<EoF > ~/environment/rbac.yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
EoF
```

### Module 8.3: Apply the config
```
$ kubectl apply -f ~/environment/rbac.yaml
```

### Module 8.4: Install helm and tiller into the cluster which gives it access to manage resources in your cluster.
```
$ helm init --service-account tiller
```

## Module 9: Deploy Prometheus
- Basic monitoring
- References: https://eksworkshop.com/monitoring/

### Module 9.1: Install Prometheus
```
$ kubectl create namespace prometheus
$ helm install stable/prometheus \
    --name prometheus \
    --namespace prometheus \
    --set alertmanager.persistentVolume.storageClass="gp2" \
    --set server.persistentVolume.storageClass="gp2"
```

### Module 9.2: Check if Prometheus components deployed as expected
```
$ kubectl get all -n prometheus
```

### Module 9.3: Access the Prometheus server URL w/ kubectl port-forward and access /targets Web UI
```
$ kubectl port-forward -n prometheus deploy/prometheus-server 8080:9090
```

## Module 10: Deploy Grafana
- References: https://eksworkshop.com/monitoring/deploy-grafana/

### Module 10.1: Install Grafana
```
$ kubectl create namespace grafana
$ helm install stable/grafana \
    --name grafana \
    --namespace grafana \
    --set persistence.storageClassName="gp2" \
    --set adminPassword="EKS!sAWSome" \
    --set datasources."datasources\.yaml".apiVersion=1 \
    --set datasources."datasources\.yaml".datasources[0].name=Prometheus \
    --set datasources."datasources\.yaml".datasources[0].type=prometheus \
    --set datasources."datasources\.yaml".datasources[0].url=http://prometheus-server.prometheus.svc.cluster.local \
    --set datasources."datasources\.yaml".datasources[0].access=proxy \
    --set datasources."datasources\.yaml".datasources[0].isDefault=true \
    --set service.type=LoadBalancer
```

### Module 10.2: Check if Grafana is deployed
```
$ kubectl get all -n grafana
```

### Module 10.3: Get Grafana ELB URL
```
$ export ELB=$(kubectl get svc -n grafana grafana -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
$ echo "http://$ELB"
```

### Module 10.4: Login using admin and password
```
$ kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

### Module 10.5: Create Grafana Dashboards

### (Optional) Clean up
```
$ helm delete prometheus
$ helm del --purge prometheus
$ helm delete grafana
$ helm del --purge grafana
```

## Module 11: Implement Liveness Probe Health Checks
- The API being a crucial part of the application it needs to be highly available
- References: https://eksworkshop.com/healthchecks/livenessprobe/

### Module 11.1: Configure the Probe
```
$ mkdir -p ~/environment/healthchecks
$ cat <<EoF > ~/environment/healthchecks/liveness-app.yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-app
spec:
  containers:
  - name: liveness
    image: brentley/ecsdemo-nodejs
    livenessProbe:
      httpGet:
        path: /health
        port: 3000
      initialDelaySeconds: 5
      periodSeconds: 5
EoF
```

### Module 11.2: Create the pod using the manifest
```
$ kubectl apply -f ~/environment/healthchecks/liveness-app.yaml
$ kubectl get pod liveness-app
$ kubectl describe pod liveness-app
```

### Module 11.3: Introduce a Failure to Test
```
$ kubectl exec -it liveness-app -- /bin/kill -s SIGUSR1 1
$ kubectl describe pod liveness-app
$ kubectl get pod liveness-app
$ kubectl logs liveness-app
```

### (Optional) Clean Up
```
$ kubectl delete -f ~/environment/healthchecks/liveness-app.yaml
```

## Module 12: Implement Readiness Probe Health Checks
- The API being a crucial part of the application it needs to be highly available
- References: https://eksworkshop.com/healthchecks/readinessprobe/

### Module 12.1: Configure the Probe
```
$ cat <<EoF > ~/environment/healthchecks/deployment-app.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: readiness-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: readiness-deployment
  template:
    metadata:
      labels:
        app: readiness-deployment
    spec:
      containers:
      - name: readiness-deployment
        image: alpine
        command: ["sh", "-c", "touch /tmp/healthy && sleep 86400"]
        readinessProbe:
          exec:
            command:
            - cat
            - /tmp/healthy
          initialDelaySeconds: 5
          periodSeconds: 3
EoF
```

### Module 12.2: Create a deployment
```
$ kubectl apply -f ~/environment/healthchecks/readiness-deployment.yaml
$ kubectl describe deployment readiness-deployment | grep Replicas:
```

### Module 12.3: Restore pod to Ready status
```
$ kubectl exec -it <YOUR-READINESS-POD-NAME> -- touch /tmp/healthy
$ kubectl get pods -l app=readiness-deployment
```

### Module 12.4: Clean Up
```
$ kubectl delete -f ~/environment/healthchecks/readiness-deployment.yaml
```

## Module 13: Implementing Auto Scaling
- References: https://eksworkshop.com/scaling/

## Module 14: Log Amazon EKS API Calls with CloudTrail
- References: https://docs.aws.amazon.com/eks/latest/userguide/logging-using-cloudtrail.html

## Module 15: Log REST operations performed to S3
- Reference: https://github.com/aws-samples/aws-modern-application-workshop/tree/python/module-5 ?

## Module 16: S3 and Athena Report
- Daily/weekly/monthly report showing the operations that have been performed during that time period
- Refences: https://aws.amazon.com/blogs/big-data/analyzing-data-in-s3-using-amazon-athena/

## Module 17: Add additional feature
- Additional killer feature of your choice
