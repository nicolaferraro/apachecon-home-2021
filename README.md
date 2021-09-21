# Kamelets and Apache Camel K at ApacheCon @Home 2021

The demo creates 3 bindings:
- A Telegram Chat is bound to a Knative Channel
- The Knative Channel is bound to a log sink to display messages on the console
- The Knative Channel is also bound to a AWS S3 sink using streaming upload, to progressively create files that contain batches of messages

Writing at least 5 messages on the Telegram chat will trigger the creation of a file on S3. Every 5 messages will trigger a creation of a new file. All data is printed on the logs by the `log-sink` binding.

## Requirements

- A Kubernets or OpenShift cluster with the Apache Camel K 1.6.0 operator installed
- OpenShift Serverless installed globally on the cluster (both Knative Serving and Eventing)
- The Camel K `kamel` CLI and the Knative `kn` CLI
- A Telegram bot created using the [@botfather](https://t.me/botfather) with related authorization token
- An AWS account, a S3 bucket and a service account to access it

## Running the first part

1. Create a Knative channel for the messages:

```
kn channel create messages
```

2. First sync the Kamelet (to ensure you're using the right version of them):

```
kubectl apply -f kamelets/
```

3. Edit the `telegram-source.yaml` file to add your Telegram credentials.

4. Run `telegram-source`:

```
kubectl apply -f telegram-source.yaml
```

5. Run `log-sink`:

```
kubectl apply -f log-sink.yaml
```

Write a *message on the chat with your Telegram bot*, then watch the logs of `log-sink` to see the messages:

```
kubectl logs -f -l camel.apache.org/integration=log-sink
```


6. Edit the `s3-sink.yaml` file to add your S3 credentials.

7. Run `s3-sink`:

```
kubectl apply -f s3-sink.yaml
```

Write *at least 5 messages on the chat* with your Telegram bot, then watch the files on the S3 bucket.

Play with it!


## Running the second part

1. Create the `beer-source` Kamelet:

```
kubectl apply -f beer-source.kamelet.yaml
```

2. Bind the `beer-source` kamelet to the channel:

You can do it from the OpenShift console, or with `kamel bind beer-source channel:messages`.

Look at the files on S3... Enjoy!
