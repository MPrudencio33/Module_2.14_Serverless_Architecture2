# Module_2.14_Serverless_Architecture2
Answers to Assignment on Module 2.14 Serverless Architecture 2

Answer the following:-
1. Does SNS guarantee exactly once delivery to subscribers?
   ANSWER:
      No, Amazon SNS does not guarantee exactly-once delivery to subscribers.
      Delivery Semantics of SNS:
        a. SNS guarantees at-least-once delivery:
              i. Each message published to a topic will be delivered at least once to each subscribed endpoint (SQS, Lambda, HTTP/S, email, SMS, etc.).
              ii. Duplicates are possible, especially in cases of network retries, subscriber errors, or transient failures.
        b. No built-in exactly-once guarantee:
              i. If the application requires exactly-once processing, deduplication logic needs to be implemented at the subscriber side.
                  For example:
                    SQS FIFO queues can help achieve exactly-once processing when SNS is configured to publish to an SQS FIFO queue.
                    Use idempotent operations in your Lambda functions or consumer logic to ignore duplicates.
   
2. What is the purpose of the Dead-letter Queue (DLQ)? This is a feature available to SQS/SNS/EventBridge.
   ANSWER:
      The Dead-letter Queue (DLQ) is a mechanism for handling messages that cannot be processed successfully by a consumer after a defined number of attempts.
      Purpose of a DLQ:
        a. Capture failed messages - when a message cannot be processed (e.g., Lambda function errors, downstream service failures),
             it is sent to a DLQ after exceeding the maximum retry attempts.
        b. Prevent message loss - instead of being lost or endlessly retried, the message is safely stored in the DLQ for later inspection or reprocessing.
        c. Enable debugging and monitoring - DLQs allow developers and DevOps teams to analyze failed messages, identify root causes, and take corrective action.
        d. Improve system reliability - by isolating problematic messages, DLQs prevent them from blocking or slowing down processing of other messages in the main queue or topic.
   
3. How would you enable a notification to your email when messages are added to the DLQ
   ANSWER:
     To get email notifications when messages are added to a DLQ, you can integrate SNS with your DLQ. Here’s how it works step by step:
     Steps to Enable Email Notifications from a DLQ
        1. Create an SNS Topic
               a. Go to AWS SNS → Topics → Create topic.
               b. Choose a name (e.g., DLQAlertTopic).
        2. Subscribe Your Email to the Topic
               a. In the SNS topic, click Create subscription.
               b. Protocol: Email
               c. Endpoint: your email address.
               d. You will receive a confirmation email—click the link to activate the subscription.
        3. Configure the DLQ to Send Notifications
             Option A: DLQ is SQS
               a. Create a Lambda function that is triggered when messages arrive in the DLQ.
               b. Lambda function publishes the message content to the SNS topic.
             Option B: DLQ is SNS
               a. Directly configure the DLQ as an SNS topic and add the email subscription.
        4. Test the Setup
               a. Send a message that will fail processing so it goes to the DLQ.
               b. Check your email for the notification containing the message details.
