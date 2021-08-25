---
layout: post
title: "Micro Services"
date: 2021-07-13
excerpt: "Build Rome in a day."
tag:
comments: false
---

# How to become a Back End Software Engineer

_You can build Rome in a day. That is the kind of leverage software engineers has._

To do that, we must git gud at architecture - understanding how shapes fit together, by experience rather than by prescription. Otherwise, you will not be able to develop critical thinking or grow your intuition.

# Projects Flow Tech Spec

## Summary

Create a system that supports activities in project proposal, listing and details.

## Context

The crowd funding app has a front-end which allows users to propose, list and retrieve project details. We need a way to store and retrieve data to actually satify these user requests. For that, we will create a set of micro services and orchestrate them to support these functionalities.

## Goals

- Create the `project-service` micro service
- Create the `threads-service` micro service
- Test the service with unit tests and integration tests
- Support up/down votes

## Non-Goals

- Deploy the service on the cloud and expose it to the world

## Plan

We need to service our crowd funding app with micro services. We will create two high level micro services in separate repositories and support them with a lower level library in the Python programming language. We will be using Postgres to store and retrieve data, Docker to containerize the services, gRPC to facilitate inter-service communication and a REST API to service requests coming from the crowd funding app.

<figure>
	<a href="{{ site.url }}/assets/img/projects/micro-services/context.png"><img src="{{ site.url }}/assets/img/projects/micro-services/context.png"></a>
	<figcaption>This is the architecture diagram.</figcaption>
</figure>

### Projects Service (HTTP Service)

This will be the context when dealing with anything directly related to projects such as proposal and listing. Examples of what is not directly related include problems, solutions and comments for the project proposal. We want to support the following user requests with a REST API consisting of the following end-points:

**ProposeProject** adds a project with the given project name, tagline, tags, problems and solutions
```
POST /projects/propose
ProposeProject(project_name, tagline, tags, problems, solution)
    returns (project_id)
```

**GetProjects** returns a list of projects with names, taglines, and tags but no threads
```
GET /projects
GetProjects()
    return (projects)
```

**GetProjectDetail** returns name, tagline and tags of a project
```
GET /projects/{project_id}
GetProjectDetail(project_id)
    return (project)
```

**UpdateTagsList** adds and removes the list of tags for a project.
```
PUT /projects/tags
UpdateTags(tags)
    return ()
```

### Threads Service (gRPC Service)

This will be the context for threads. Threads are anything that can be up/down voted and replied to. In the context of this tech spec, this includes solution and problems. We need a gRPC to support storing and retrieve problems and solutions for given projects.

**GetProjectThreads** returns `limit` number of threads under a given project
```
GetProjectThreads (project_id, limit)
    returns ([]threads)
```

**UpdateThreads** updates content of existing threads or adds new ones
```
UpdateThreads ([]threads)
    returns ()
```

**Vote** increases the up or down vote count of the thread
```
Vote (thread)
    returns ()
```

### Objects

The following definitions helps us determine what field types we should use in protobuf messages and database tables

**Project** represents project details
```
Project:
    project_id - identifies the project record in the database
    project_name - name of the project
    tagline - project tagline
    threads - list of thread_ids
    tags - list of tag_ids
    created_at - timestamp denoting time of creation in database
```

**Tag** represents tags of a project
```
Tag:
    tag_id - identifies the tag record in the database
    project_id - identifies the project record in the database
    tag - content of the tag
```

**Thread** represents a comment, problem or solution for a given project or thread
```
Thread:
    thread_id -identifies the thread record in the database
    project_id - identifies the project record in the database
    thread_type - COMMENT, SOLUTION or PROBLEM
    message - the content of the thread
    up_vote - the number of times the thread was up-voted
    down_vote - the number of times the thread was down-voted
    created_at - timestamp denoting time of creation in database
)
```

## Security
This is a topic that deserve a separate blog post of its own but the scope of this tech spec involves securing the following aspect of our system.

- Database: This requires safely storing database passwords and restricting the access to the two new services.
- gRPC communication between services: Services are communicating via gRPC and their connections needs to be encrypted to prevent information leak of unintended parties.
- Exposing REST API to a web browser client: Only our app should be authorized to use our micro-services. Otherwise, we will grant access to unintended users.

## Scalability
We have separated the projects and threads into two different services. We know that threads / discussions are much more likely to occur than project proposal and listing. This allows us to allocate more computing resources to threads than projects, thus scaling each functionality independently.

## Other Considerations
- Securing and scaling a system of micro services is beyond the scope of this exercise.

<figure>
	<a href="{{ site.url }}/assets/img/what.gif"><img src="{{ site.url }}/assets/img/what.gif"></a>
	<figcaption>What are you even talking about?</figcaption>
</figure>

# [Toriel](https://undertale.fandom.com/wiki/Toriel) has come to the rescue

To help you out, I simplified the problem~! But still, you have to understand what I did. Just follow along!

## Setup

### Docker

Install [Docker](https://docs.docker.com/get-docker/). Docker is the industry standard for containerization, which crudely means it can gaurantee to run the same code on the same way everytime no matter if you own a Mac, Windows, or Linux computer. This makes testing your code repeatable whether you are running your code on your computer or in the cloud.

### Python

Install [Python3](https://www.python.org/downloads/). Python is a simple language to learn and a lot of people use it in real life. It makes job interviews easier and the code is a lot more friendly to read which won't distract one from learning and applying concepts like many other languages do.

Go to terminal and install `virtualenv`. It allows you to contain your requirements installations, so that you can import right packages in your code.
```
python -m pip install --user virtualenv
```

Also in the terminal, install `pip-upgrader`. This allows you to update requirements automatically instead of having to manually changing version numbers in you `requirements.txt` file.
```
pip install pip-upgrader
```

## Overview

### The Projects REST Service

A REST API is a server that a web client can access through your web browser, phone or other servers. It can perform some task or manipulate data upon client requests. What we are creating here cannot be formally called a REST API but let's sacrifice correctness instead of having mass confusion.

### The Threads gRPC Service

A gRPC API is a server that another server can access. The reason we use gRPC instead of REST here because gRPC allows for efficient communication between services; more specifically, gRPC packages data using protocol buffer serialization which is more compact than the raw binary in REST. This is limited to server-server communication (as of today) which is why we cannot use it for the projects service.

## Running the Services on your computer

Clone the repositories:
```
git clone https://github.com/missAliceX/projects-service.git
git clone https://github.com/missAliceX/threads-service.git
```

**In two separate tabs in your terminal**, go into the folder of each service repositories, create and activate the virtual envrionment:
```
virtualenv -p python3 venv
source venv/bin/activate
```

Start the database in just one of the services. This is a just a convenient command I crafted to start a Postgres database Docker container instance.
```
make resetdb
```

Install stuff, wait a little bit... and then run each service:
```
make install
make run
```

In a 3rd tab on the terminal, make an HTTP POST request to the Projects micro service:
```
curl -X POST -d '{
    "project_name": "Popee the Performer",
    "tagline": "an apprentice clown in Wolf Circus",
    "problems": [
        "short temper",
        "self centered"
    ],
    "solutions": [
        "play the drums"
    ],
    "tags": [
        "clown",
        "circus"
    ]
}' http://localhost:8080/projects/propose
```

Congrats! You can now run micro services and use them on your local machine, but... what actually happened?

### Environment Variables

Open the `Makefile` file. Looks like `make run` just executes `python3 main.py`. Try runing just `python3 main.py` in your terminal...
```
Traceback (most recent call last):
  File "/Users/missAliceX/Documents/GitHub/projects-service/venv/lib/python3.9/site-packages/pylib/postgres/__init__.py", line 23, in connect
    host=cfg["POSTGRES_HOST"],
  File "/usr/local/Cellar/python@3.9/3.9.6/Frameworks/Python.framework/Versions/3.9/lib/python3.9/configparser.py", line 1257, in __getitem__
    raise KeyError(key)
KeyError: 'POSTGRES_HOST'
```

Something about `POSTGRES_HOST`. You can actually go into the file (e.g. `/Users/missAliceX/Documents/GitHub/projects-service/venv/lib/python3.9/site-packages/pylib/postgres/__init__.py`), trace upwards, and see what's causing the problem:

1. Some thing is wrong with `cfg["POSTGRES_HOST"]` in `pylib/postgres/__init__.py`
1. `cfg` is a parameter to the `connect()` classmethod, which looks like:
```
def connect(cls, cfg, max_retry=5):
    ...
```
1. `connect()` is called in `pylib/postgres/service.py`, like so:
```
PostgresClient.connect(cfg)
```
1. Again, `cfg` comes from somewhere else - as a parameter in the Service class which ultimately gets called in `main.py`
```
svc = Service(ConfigParser(os.environ)["DEFAULT"])
```
1. Now we are confused again. Let's look it up `ConfigParser` and `os.environ`... It seems like we are putting some **environment variables** in the ConfigParser.

Here's anther clue. Let's [search our entire folders](https://code.visualstudio.com/docs/editor/codebasics#:~:text=Search%20across%20files%23,each%20file%20and%20its%20location.). We see `POSTGRES_HOST` in the `Makefile`:
```
.EXPORT_ALL_VARIABLES:

POSTGRES_HOST = localhost
POSTGRES_USER = test-user
POSTGRES_PASSWORD = test-password
POSTGRES_DB = test-db
```

These are called environment variables, something that your terminal **shell** can use. For example, you can set them in the terminal, run some script and the script will be able to use it. **Type these out by hand**:

```
export GIVE_ME=bread
echo ${GIVE_ME}
```

Let's see if we can read the environment variable in our Python script:
```
python3
>>> import os
>>> os.getenv("GIVE_ME")
'bread'
```

### Challenge 1: Run the server manually

Now think - how can you make use of environment variables to run `python3 main.py` without error messages?

## Docker Containers

<figure>
	<a href="{{ site.url }}/assets/img/works-on-my-machine.jpeg"><img src="{{ site.url }}/assets/img/works-on-my-machine.jpeg"></a>
	<figcaption>Very simply put...</figcaption>
</figure>

When you run `python3 main.py`, you will see the following:

```
[2021-08-16 20:07:17 -0700] [41495] [INFO] connected to postgres
```

But how? Remember how we ran `make resetdb` before running `make run`? Well, that starts the database. We can check:

```
docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED          STATUS          PORTS                                       NAMES
b897c04d3556   postgres   "docker-entrypoint.s…"   45 minutes ago   Up 45 minutes   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   postgres
```

That is our database =D It's running as a Docker container. Let's remove the container and start the database manually:

```
docker rm -f postgres
docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

=[ It's running no more. You can run it with the command below.

```
docker run --name postgres \
		-p 5432:5432 \
		-e POSTGRES_HOST=${POSTGRES_HOST} \
		-e POSTGRES_USER=${POSTGRES_USER} \
		-e POSTGRES_PASSWORD=${POSTGRES_PASSWORD} \
		-e POSTGRES_DB=${POSTGRES_DB} \
		-d postgres
```
- `--name` is the name of the container you are running
- `-p` is the publish list. Our projects service runs on port 8080 like here `Goin' Fast @ http://0.0.0.0:8080`; likewise, Postgres runs on port 5432 inside the container but we cannot directly access the container's port so we ask Docker to publish the port to our local machine's 5432 port.
- `-e` telling Doccker to set the environment variable when running the container. `-d` is detach mode, try running the command without it and see what's different. `postgres` at the very end is the image - more on image later.

What's an image? There are so many dash this - dash that - so many things to memorize... @ 皿 @

```
docker run --help
docker ps --help
docker rm --help
```

Doesn't that (っ◔◡◔)っ ¿ help ?

## Docker Image

You should be able to run `python3 main.py` on your terminal at this point, but it's running locally on your machine. To run it as a container on Docker, you need to first build an image.

```
docker build -t projects:tag0 -f Dockerfile .
docker images
REPOSITORY       TAG            IMAGE ID       CREATED         SIZE
projects         tag0           092595adf206   5 seconds ago   971MB
```

Take a look at the `Dockerfile` file:
```
FROM python:3.9.6

WORKDIR /app
COPY main.py main.py
COPY requirements.txt .
COPY src src

RUN pip install --no-cache-dir -r requirements.txt

CMD [ "python3", "main.py" ]
```
1. It builds the image from the base image [python:3.9.6](https://hub.docker.com/_/python) which is a container that can run `python3`. You can use other base images if your project runs in the [Rust](https://hub.docker.com/_/rust) language or if it is a different key-value store like [Redis](https://hub.docker.com/_/redis). It's also not limited to anything as you can run an ingress controller like [Traefik](https://hub.docker.com/_/traefik).
1. It sets the work directory so that all the code goes in there.
1. `COPY` copies files from your host computer to a volume designated for the container when it's run. A volume is just a storage space for folders and files.
1. `RUN` just executes the command now which is `pip install`. This installs the packages listed in `requirements.txt` into our container.
1. `CMD` is the command that gets run when you start the container.

You can read more about Dockerfiles in [this documentation](https://docs.docker.com/engine/reference/builder/).

### Challenge 1.2: Run the container you just built

Think about how you started the container for the postgres image. If you need help, remember to use `--help`.

## Challenge 2: Implement your first end-point

Go to `projects-service/src/service.py`. See how `project_propose()` is implemented? Mimic that. Try putting this code under `project_list()`:
```
return json([
    {
        "name": "project_1"
    },
    {
        "name": "project_2"
    }
])
```

Press <kbd>Ctrl + c</kbd> in your terminal to stop the projects server, then start it again:
```
make run
```

Open a new terminal tab, and make a request to the corresponding endpoint:
```
curl -X GET http://localhost:8080/projects
```

See how that works?

## Database Query

Let me show you another thing. Go to your terminal again and run the Postgres Query Language (aka psql) command line interface (aka cli). Try making the following queries:
```
make psql
test-db=# select * from projects;
test-db=# select * from tags;
test-db=# select * from threads;
```

### Challenege 2.1: Make a **select** query in code

You can see how `update_project_details()` in `projects-service/src/repo.py` makes a query to **insert** some data **into** the **projects** and **tags** table in two queries. Your objective is to implement `get_projects_listing()` to get the project names and taglines.

#### How to think

1. How can you make a **select** query in psql to get all the information you need?
1. Can you trace back to see what Python library I used to make these queries? You can find this in `requirements.txt`.
1. Find and read the online documentation to translate your psql query to the query to code similar to `update_project_details()`.

### Challenge 3: Implement get_project_details() also

A little practice don't hurt.

## Database Schema and Migration

While you were coding, you realized that the threads table did not have fields to represent up and down votes. Darn, how are these tables even **created**?

Did you by any chance find the library? You should find it now. We kind of need it now. There's a file in there called `0_create_tables.up.sql`.

```
CREATE TABLE IF NOT EXISTS threads (
    thread_id bigserial PRIMARY KEY,
    project_id integer REFERENCES projects NOT NULL,
    thread_type thread_type NOT NULL,
    message text NOT NULL
);
```

Looks like we just need to add a up and down vote count. Let's run this query in `psql`:
```
CREATE TABLE IF NOT EXISTS threads (
    thread_id bigserial PRIMARY KEY,
    project_id integer REFERENCES projects NOT NULL,
    thread_type thread_type NOT NULL,
    up_vote integer,
    down_vote integer,
    message text NOT NULL
);
```

...

```
NOTICE:  relation "threads" already exists, skipping
```

<figure>
	<a href="{{ site.url }}/assets/img/random-keys.gif"><img src="{{ site.url }}/assets/img/random-keys.gif"></a>
	<figcaption>Don't do this to me</figcaption>
</figure>

Let me make a prediction, you just instinctively thought to search online for "add field to table postgres". Okay, maybe not but it will come to you =) Try again after you updated your table.

```
make psql
test-db=# select * from threads;
 thread_id | project_id | thread_type |    message     | up_votes | down_votes 
-----------+------------+-------------+----------------+----------+------------
         1 |          1 | PROBLEM     | short temper   |          |           
         2 |          1 | PROBLEM     | self centered  |          |           
         3 |          1 | SOLUTION    | play the drums |          |           
(3 rows)
```

## Tooling

It should be no more secret that our secret library is [pylib](https://github.com/missAliceX/pylib). This is a library meant to support all of our micro-services needs. Having library code help abstract away confusing set-ups that get in the way of business and "high-level" logic.

### `pylib/service`

This package supports starting up HTTP and gRPC servers. You can see that we use the [Sanic web framework](https://github.com/sanic-org/sanic) to run HTTP servers and the [gRPC protocol](https://grpc.io/docs/languages/python/) to run gRPC servers. This way, we can just instantiate a service and run it for the most part.
```
svc = HTTPService("my-servicce")
svc.start()
```

### `pylib/postgres/migrations`

This folder contain `psql` scripts that helps us migrate Postgres database tables automatically. Remember you had to add a `up_vote` and `down_vote`? Imagine your numerous colleages changed these tables without telling you. Keeping the code in a code-base help us version control and collaborate.

### Challenge 3: Show me that you understand

### `pylib/postgres`

This package helps us interact with the Postgres database and automate migration.

### connect()

`connect()` establishes a connection with the database via a connection pool. A connection pool operates like a public bathroom; people can take turns but when it's fully occupied, you got to wait until one opens up. Except... you have to wait longer each time to take a look because it's increasingly awkward. This is known as an **exponential backoff retry**. Its structure boils down to this:

```
keep trying until connected OR
rage quit when failing too many times {
    borrows a connection from the pool to connect to Postgres
    confirm that it connected by running a query
    wait/sleep for a while, but wait longer each time
}
```

#### Challenge 3.1: Experiment with retries

Show me that you understand that the services will retry to connect with Postgres if Postgres goes down. Confirm that:

- Starting the service when Postgres is not running will trigger the retries
- Starting Postgres while the service is retrying will let the service eventually properly connect
- Wait until the service retry more than `max_retry` number of times and see it crash and quit completely.

### repo()

`repo()` starts an *atomic transaction* by giving the caller a *database cursor* to work within a *context*, denoted by the `@contextmanager` *decorator* at the top of the function and the `yield conn.cursor()` line.

```
with PostgresClient.repo() as some_cursor:
    some_cursor.execute("some query")
    some_cursor.execute("some other query")
```

It runs all the code in repo() before `yeild` is called. `conn.cursor()` is now assigned to `some_cursor`. This structure is known as a **context**. Context can mean other things in context, but loosely, it means it carries some information with it or wraps around something covertly.

This context closes after `with` block is over and returns to the line after the `yield` line. In our case iin `repo()`, it commits the queries you just ran and returns the connection to the connection pool via `pool.putconn(conn)`.

If any code under the `with` block fails, an exception is raised and `conn.rollback()` under `repo()` is called which reverts all the queries you ran under the `with` block. This behavior is what we call **atomic**, when all queries must succeed or else they all revert. `finally`, whether or not they succeeded, it returns the connection to the connection pool again  via `pool.putconn(conn)`.

#### Challenge 3.2: Design a transaction that may or may not be atomic

Implement `UpdateTagsList()`. Make sure you have add and remove queries. This will require you to understand atomic transactions.

### migrate()

`migrate()` reads all the `.up.sql` or `.down.sql` files in the `pylib/postgres/migrations` and runs them as queries using the `repo()` context manager we just mentioned.

#### Challenege 3.3: Miigrate automatically

Notice that `migrate()` is called when you start the `projects-service` or the `threads-service`. Make use of this fact to introduce your addition of the `up_vote` and `down_vote` in the `threads` table.

## Docker in Unit Tests

We haven't even talked about testing!