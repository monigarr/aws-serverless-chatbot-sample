# AWS Serverless Chatbot Sample
Example code for running a Slack chatbot using AWS Lambda and Amazon API Gateway. 

# Getting Started

1. Create your Lambda function that will process all Slack Requests
2. Create an API Gateway API
3. Create a method of type: POST
4. Select Integration Type: Lambda
5. Select the region in which you created your Lambda function
6. Select the Lambda Function you created
7. Click "Integration Request"
8. At the bottom of this Page select "Add mapping Template"
9. For content type please specify: "application/x-www-form-urlencoded"
10. Insert the template code below into the text field for the template. This code converts a URL Encoded form post into JSON for your Lambda function to parse
11. Deploy your API
12. In Slack, Go to Apps and Integrations
13. Click Build in the top right
14. Select Make a Custom Integration
15. Select Outgoing Webhooks
16. Pick a trigger word for your Bot!
17. In URL, put the URL created by your API Gateway Deployment

# Requirements due by September 20th 2016
1. http://awschatbot.devpost.com/?utm_source=Devpost+Weekly+Newsletter&utm_campaign=702526bbf5-Hacker_8_11_16&utm_medium=email&utm_term=0_294421ffd0-702526bbf5-224954713
2. Slackbot that runs on AWS Lambda.
2. SlackBot must use AWS Lambda and Amazon API Gateway. See our sample bot code for an example.
3. SlackBot must Be a new or existing bot
4. optional: Integrate Slack APIs, such as the Events API
5. optional: Integrate other APIs, SDKs, and datasets as long as the developer is authorized to use them
6. optional: Showcase natural language processing of chat conversations, use open source NLP libraries
7. optional: Use additional AWS services
8. optional: Be submitted to the Slack App Directory
9. All code should function as depicted in the submitted video and text description 
10. Submission must be in English 
11. Be substantially different from any other app submitted by the same submitter/team/organization 
12. Be the original work of the submitter, be solely owned by the submitter, and not violate the IP rights of any other person or entity


# Submission Requirements
1.Include a demonstration video of the bot in action.
2. Include a brief explanation of what the bot does and what makes it unique 
3. A way to access your bot for judging and testing 
4. Include a link to your bot code on GitHub (this repo may be public or private – if private, access must be given in the testing instructions provided). GitHub code will be used only for bot review and testing. 
5. Include all deployment files and testing instructions needed for testing your bot.


#### Template code for Integration Request:
```
## convert HTML POST data or HTTP GET query string to JSON
 
## get the raw post data from the AWS built-in variable and give it a nicer name
#if ($context.httpMethod == "POST")
 #set($rawAPIData = $input.path('$'))
#elseif ($context.httpMethod == "GET")
 #set($rawAPIData = $input.params().querystring)
 #set($rawAPIData = $rawAPIData.toString())
 #set($rawAPIDataLength = $rawAPIData.length() - 1)
 #set($rawAPIData = $rawAPIData.substring(1, $rawAPIDataLength))
 #set($rawAPIData = $rawAPIData.replace(", ", "&"))
#else
 #set($rawAPIData = "")
#end
 
## first we get the number of "&" in the string, this tells us if there is more than one key value pair
#set($countAmpersands = $rawAPIData.length() - $rawAPIData.replace("&", "").length())
 
## if there are no "&" at all then we have only one key value pair.
## we append an ampersand to the string so that we can tokenise it the same way as multiple kv pairs.
## the "empty" kv pair to the right of the ampersand will be ignored anyway.
#if ($countAmpersands == 0)
 #set($rawPostData = $rawAPIData + "&")
#end
 
## now we tokenise using the ampersand(s)
#set($tokenisedAmpersand = $rawAPIData.split("&"))
 
## we set up a variable to hold the valid key value pairs
#set($tokenisedEquals = [])
 
## now we set up a loop to find the valid key value pairs, which must contain only one "="
#foreach( $kvPair in $tokenisedAmpersand )
 #set($countEquals = $kvPair.length() - $kvPair.replace("=", "").length())
 #if ($countEquals == 1)
  #set($kvTokenised = $kvPair.split("="))
  #if ($kvTokenised[0].length() > 0)
   ## we found a valid key value pair. add it to the list.
   #set($devNull = $tokenisedEquals.add($kvPair))
  #end
 #end
#end
 
## next we set up our loop inside the output structure "{" and "}"
{
#foreach( $kvPair in $tokenisedEquals )
  ## finally we output the JSON for this pair and append a comma if this isn't the last pair
  #set($kvTokenised = $kvPair.split("="))
 "$util.urlDecode($kvTokenised[0])" : #if($kvTokenised[1].length() > 0)"$util.urlDecode($kvTokenised[1])"#{else}""#end#if( $foreach.hasNext ),#end
#end
}
```
