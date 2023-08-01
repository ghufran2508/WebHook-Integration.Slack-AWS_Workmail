1. Create a new [App](https://api.slack.com/apps) or use exisiting.
2. Add Features And Functionality -> Incoming Webhooks -> Add New Webhook to workplace.
3. select the channel and the copy the link.
4. Now goto [AWS Lambda](https://docs.aws.amazon.com/workmail/latest/adminguide/lambda.html) and create a new lambda function.
U can use this one
```
const https = require('https');
// Replace this with your actual Slack webhook URL
const SLACK_WEBHOOK_URL = 'YOU_SLACK_API';

function postRequest(data) {
  return new Promise((resolve, reject) => {
    const url = new URL(SLACK_WEBHOOK_URL);
    const options = {
      host: url.hostname,
      path: url.pathname,
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
    };
    
    //create the request object with the callback with the result
    const req = https.request(options, (res) => {
      resolve(JSON.stringify(res.statusCode));
    });

    // handle the possible errors
    req.on('error', (e) => {
      reject(e.message);
    });
    
    //do the request
    req.write(JSON.stringify(data));

    //finish the request
    req.end();
  })
}

exports.handler = async (event) => {
    // const timestamp = (new Date().getTime()/1000;
    // console.log(event.envelope.recipients[0].address);
    
    const slackMessage = {
        attachments: [{
            text: `You got a new message`,
        }]
    };

    await postRequest(slackMessage);
};

```
5. Then goto AWS Cloud Shell and run this command (use your Lambda function name)
```
aws lambda add-permission \
    --function-name funtion_name \
    --action lambda:InvokeFunction \
    --statement-id workmail \
    --principal workmail.us-east-1.amazonaws.com
```

6. Then goto your organization workmail and open organization settings.
7. Goto inbound rules and create new rule.
8. Set Actions to Run Lambda and select your lambda function accordingly.
9. Create.

DONE.
