# Lab 04 - Cloud Pub/Sub: Qwik Start - Python

## Lab Summary

This lab focused on using Google Cloud Pub/Sub for asynchronous messaging between applications. The lab demonstrated how to create Pub/Sub topics and subscriptions, publish messages to a topic, and receive messages using a subscriber application written in Python.

## Lab Objective

The objective of this lab was to learn the fundamentals of Google Cloud Pub/Sub, including creating topics and subscriptions, publishing messages, and consuming messages using the Python client library.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Cloud Pub/Sub
- Cloud Shell
- Python 3
- Python Virtual Environment (venv)
- Google Cloud Pub/Sub Python Client Library
- gcloud CLI
- GitHub
- Linux Commands

## Key Steps Performed

### 1. Created a Python Virtual Environment

- Installed the virtual environment package.

```bash
sudo apt-get install -y virtualenv
```

- Created a virtual environment.

```bash
python3 -m venv venv
```

- Activated the virtual environment.

```bash
source venv/bin/activate
```

### 2. Installed the Pub/Sub Client Library

- Installed the Google Cloud Pub/Sub Python client library.

```bash
pip install --upgrade google-cloud-pubsub
```

- Verified successful installation.

### 3. Downloaded Sample Application Code

- Cloned the Google Pub/Sub sample repository from GitHub.

```bash
git clone https://github.com/googleapis/python-pubsub.git
```

- Navigated to the sample code directory.

```bash
cd python-pubsub/samples/snippets
```

### 4. Explored Pub/Sub Publisher Operations

- Examined the publisher script.

```bash
cat publisher.py
```

- Reviewed available publisher commands.

```bash
python publisher.py -h
```

- Learned how to create, list, publish, and delete Pub/Sub topics.

### 5. Created a Pub/Sub Topic

- Verified the current project ID.

```bash
echo $GOOGLE_CLOUD_PROJECT
```

- Created a topic named `MyTopic`.

```bash
python publisher.py $GOOGLE_CLOUD_PROJECT create MyTopic
```

- Verified that the topic was successfully created.

- Listed available topics.

```bash
python publisher.py $GOOGLE_CLOUD_PROJECT list
```

### 6. Created a Subscription

- Created a pull subscription named `MySub` for `MyTopic`.

```bash
python subscriber.py $GOOGLE_CLOUD_PROJECT create MyTopic MySub
```

- Verified subscription creation.

- Listed subscriptions within the project.

```bash
python subscriber.py $GOOGLE_CLOUD_PROJECT list-in-project
```

### 7. Published Messages to the Topic

- Published a test message.

```bash
gcloud pubsub topics publish MyTopic --message "Hello"
```

- Published additional messages.

```bash
gcloud pubsub topics publish MyTopic --message "Publisher's name is <YOUR NAME>"
```

```bash
gcloud pubsub topics publish MyTopic --message "Publisher likes to eat <FOOD>"
```

```bash
gcloud pubsub topics publish MyTopic --message "Publisher thinks Pub/Sub is awesome"
```

- Verified that all messages were successfully published to the topic.

### 8. Retrieved Messages Using the Subscription

- Started a subscriber to receive messages from the subscription.

```bash
python subscriber.py $GOOGLE_CLOUD_PROJECT receive MySub
```

- Received and displayed messages published to `MyTopic`.

Example output:

```text
Received message: Hello
Received message: Publisher's name is <YOUR NAME>
Received message: Publisher likes to eat <FOOD>
Received message: Publisher thinks Pub/Sub is awesome
```

- Confirmed successful message delivery between publisher and subscriber.

### 9. Stopped the Subscriber

- Used the keyboard interrupt command to stop listening for messages.

```text
Ctrl + C
```

- Verified that the subscriber terminated successfully.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created and configured a Python virtual environment.
- Installed the Google Cloud Pub/Sub Python client library.
- Created a Pub/Sub topic.
- Created a pull subscription.
- Published messages to a Pub/Sub topic.
- Retrieved messages using a subscriber application.
- Gained practical experience with asynchronous messaging systems.
- Learned how publishers and subscribers communicate through Cloud Pub/Sub.

## Reflection

This lab provided hands-on experience with Google Cloud Pub/Sub and event-driven messaging architecture. I learned how topics act as communication channels and how subscriptions enable applications to receive messages asynchronously. The lab improved my understanding of publish-subscribe systems, message delivery, and the use of Python client libraries to interact with Google Cloud services. This knowledge is valuable for building scalable, decoupled, and event-driven cloud applications.