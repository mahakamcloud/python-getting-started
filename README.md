# Getting Started on Mahakam with Python

This simple application is provided to demonstrate deployment to Mahakam. Inspired by https://books-example.herokuapp.com and getting started on [Heroku](https://devcenter.heroku.com/articles/getting-started-with-python).

## Prepare the app

In this step, you will prepare a simple application that can be deployed.

To clone the sample application so that you have a local version of the code that you can then deploy to Mahakam, execute the following commands in your local command shell or terminal:

```sh
$ git clone https://github.com/heroku/python-getting-started.git
$ cd python-getting-started
```

You now have a functioning git repository that contains a simple application, a `Dockerfile` specifying Python 2.7.0 and a `requirements.txt`, which is used by Python's dependency manager, Pip.

## Create the Kubernetes cluster

Before deploying the app, you need a Kubernetes cluster.

Create a cluster on Mahakam, which provisions new network and deploy Kubernetes cluster with number of nodes that you want:
```sh
$ mahakam create cluster --cluster-name kalimantan --num-nodes 2
Creating kubernetes cluster...

Name:         kalimantan
Cluster Plan: small
Worker Nodes: 2
Status:       Pending

Use 'mahakam describe cluster kalimantan' to monitor the state of your cluster
```
Mahakam will take around 7 mins to provision your new cluster.

## Create the App Components

In this step, you will create and deploy components required to run the app.

Create components first on Mahakam. For example, the getting started app requires Postgres. Create Postgres first by specifying password and database name:
```sh
$ cat <<EOF> postgres-values.yaml
postgresqlPassword: foo
postgresqlDatabase: bar
EOF
$ mahakam create app --cluster-name kalimantan --app-name psql --chart maha-incubator
/mahakam-postgres --values ./postgres-values.yaml
Creating your application...

Name:           psql
Cluster:        kalimantan
App endpoint:   psql-mahakam-postgres.default.svc.cluster.local

Use 'mahakam describe app psql' to monitor the state of your application
```

Your postgres is now deployed. You can use it by configuring your app later.

## Create the App

In this step, you will deploy the app to Mahakam.

Specify the Postgres component that you have deployed and other config that the app require.
```sh
$ cat <<EOF> values.yaml
APP_SETTINGS=config.ProductionConfig
DATABASE_URL=postgresql://postgres:foo@psql-mahakam-postgres.default.svc.cluster.local/bar
EOF
```

Now, create the app. Specify that you run the app using Python chart.
```sh
$ mahakam create app --cluster-name kalimantan --app-name get-started --chart maha-incubator/mahakam-python --values ./values.yaml
Creating your application...

Name:           get-started
Cluster:        kalimantan
App endpoint:   get-started.default.svc.cluster.local

Use 'mahakam describe app get-started' to monitor the state of your application
```

You should now be able to access the getting started app with kubectl proxy.

## Play with the App

This is a Flask+PostgreSQL simple web application. This application can add and retrieve book details.

* Get all data
http://<get-started-endpoint>/getall
  
* *Get by id 
(for id=1)  
http://<get-started-endpoint>/get/1
   
* *Add through html form
http://<get-started-endpoint>/add/form

* *Add a book
(for name=HarryPotter, author=JKRowling, published=2003)  
http://<get-started-endpoint>/add?name=HarryPotter&author=JKRowling&published=2003
