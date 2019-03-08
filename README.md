
# Send Email from a custom domain with Mailgun API using Python

### Intro

There are plenty of tutorials on the Internet teaching how to send an Email using Python. Why bothering with another tutorial talking about sending Email with a custom domain? Well at this point, I agree with you that you're right. But this tutorial is aiming for the folks who might be running a professional online business or website and looking for possibilities of sending customers and marketing emails in an easy and scalable manner while being able to maintain their brandings (Such as using their domains or sub-domains). Luckily, Mailgun makes this way a lot easier to do this.

So, what exactly is Mailgun?

Mailgun is a service that offers you an easy-to-use API (Application Programming Interface) in many programming languages(Python, Go, Curl and Ruby etc.) to send emails scalably and at the same time, abstracts away the complex server credentials or SSL logics with just a simple post request to do the all the heavy liftings for you.




### Step 1 - Sign up for a Mailgun account( Credit card required - Monthly free usage up to 10000 emails. !!!)

Go to the [Mailgun sign up page](https://signup.mailgun.com/new/signup) for account sign-up. The sign-up process is pretty straight-forward. If you just follow the instructions there, you will be most likely just fine.


### Step 2 - Log in and Add a Domain/Sub Domain

After Sign up process, you can log in in the Mailgun [Login Page](https://app.mailgun.com/sessions/new). After logging in, please go to [Domains](https://app.mailgun.com/app/domains) Section in the Dashboard.


Then you need to hit the "Add New Domain" button to add a new domain or a subdomain, in the [new page](https://app.mailgun.com/app/domains/new), you can add a SUBDOMAIN like **"example.domain.com"** or your main DOMAIN **"domain.com"**.

Howevever, it's **recommended** by Mailgun that you should add a subdomain to avoid any potential DNS records conflicting with your current DNS records under your MAIN domain.

Let's assume in this case, you added a SUBDOMAIN as **"subdomain.domain.com"**. After adding the new sub domain, you will be directed to the DNS Verifying instructions page.

In this text-basded tutorial, I won't dive into details of how to verify your domain in your DNS providers. As the case might vary that each DNS provider is slightly different, so if you have struggles following Mailgun instructions how to verify your domain. Please reach out to me via  the comment section of [YouTube video](https://www.youtube.com/watch?v=zPfXCZyRjxk&t=39s) of this tutorial, I will be happy to help!!!

 ### How to proceed with code?

#### 1. Get your API Key and API Base URL

After your domain name is verified by Mailgun, you can go to the credentials page of your domain at https://app.mailgun.com/app/domains/yourdomain.com to view the credentials.

In the **"Domain Information"** section, your **API Key** and **API Base URL** will be available to you and you will need them later in your Python code.

#### 2. Code it here

In order this to work, you will need **"requests"** pip installed in your computer.:

**& pip install requests**


```python
import requests

# Instantiate a post request
requests.post(

        # Here goes your Base API URL
        "https://api.mailgun.net/v3/YOURDOMAIN/messages",
        # Authentication part - A Tuple
        auth=("api", "your api key"),

        # mail data will be used to send emails
        data={"from": "Your Name <you@yourdomain.com>",
                 "to":["user@example.com"], # passing a list or a signle email address with string data type.
                 "subject": "Testing some awesomeness of Mailgun",
                 "text":" Mailgun test on the first day of 2019."}
                                                                    )

```

Send with **cc** and **bcc**


```python
requests.post(

        # Here goes your Base API URL
        "https://api.mailgun.net/v3/YOURDOMAIN/messages",
        # Authentication part - A Tuple
        auth=("api", "your api key"),

        # mail data will be used to send emails
        data={"from": "Your Name <you@yourdomain.com>",
                 "to":["user@example.com"], # passing a list or a signle email address with string data type.
                  "cc":["cc@example.com"],
                  "bcc":["bcc@example.com"],
                 "subject": "Testing some awesomeness of Mailgun",
                 "text":" Mailgun test on the first day of 2019."}
                                                                    )
```

If your code prints a **"Response [200]"** message, your message is then successfully sent.









---
title: "Send Email in Batch with Mailgun API using Python"
date: 2019-03-08
draft: False
---



# Batch Sending With Mailgun API



Step 1: create a mailing list in mailgun [dashboard](https://app.mailgun.com/app/lists):

1. You can upload a csv file to bulk upload to create a mailing list
2. Or add one recipient at a time to create a mailing list.


Imagine you created a mailing list: **no-reply@yourdomain.com**


## How to proceed with coding:

#### A. Send a general email without a custom greeting using Mailgun Mailing List


```python
import requests
```


```python
def batch_mailing():

    requests.post(

        "https://api.mailgun.net/v3/yourdomain/messages",
        auth=("api", "your-api"),
        data = {
            "from": "you@yourdomain.com",
            "to":"no-reply@yourdomain.com", # this is the mailing list created in mailgun dashboard
            "subject":" Prank message for you",
            "text": '''
                        Hello,
                        Can you hear me? It's me again.
                    '''})

# Run the function
batch_mailing()
```

Running above code sends the message to all the members of your mailing list created in the Mailgun dashboard **WITHOUT** disclosing each member's individual email address in the "to" section of the email **DESPITE** this mailing list is currently placed in the "to" parameter.

#### B. Send a custom & personalized email via Mailgun **Template Variable**.

The code will take very much after. The only caveat is that we need to pass a **template variable** into the text body:

Find more about template variable in the [mailgun documentation page](https://documentation.mailgun.com/en/latest/user_manual.html#batch-sending)

Here's how we proceed: we will pass the first name of each member from the mailing list. the template variable for first name is: **%recipient_fname%**





```python
import requests

def batch_mailing():

    requests.post(

        "https://api.mailgun.net/v3/yourdomain/messages",
        auth=("api", "your-api"),
        data = {
            "from": "you@yourdomain.com",
            "to":"no-reply@yourdomain.com", # this is the mailing list created in mailgun dashboard
            "subject":" Prank message for you",
            # passing the template variable for first name to send a custom email.
            "text": '''
                        Hello %recipient_fname%,
                        Can you hear me? It's me again.
                    '''})

# Run the function
batch_mailing()
```

Running above code sends a custom message that looks like:(with the person's first name in the body text.)

"Hello Frank,
Can you hear me? It's me again. ",

"Hello Alice,
Can you hear me? It's me again. ",

........


**
Do not worry about how a template variable is associated with an actual member from the mailing list. This mapping gets automatically created for you by Mailgun when a mailing list created. You just have to learn how to reference them correctly.
**

#### C. Send an email **WITHOUT ** Mailgun's mailing list feature and custom greetings.

In this case, your script will read the csv file of different recipients and send the email right up without using a mailing list. The down side of this is you lose the functionality of applying the template variables.


```python
import requests

# Import pandas to read the recipients csv file.
import pandas as pd
```


```python
# Convert the pandas Series "Email" to a list

recipients = pd.read_csv("MailingList.csv")["Email"].values.tolist()
```


```python
def batch_mailing():

    requests.post(

        "https://api.mailgun.net/v3/your domain/messages",
        auth=("api", "your api key"),
        data = {
            "from": "you@yourdomain.com",
            "to":"you@yourdomain",
            "bcc":recipients,
            "subject":" Prank message for you",
            "text": '''
                    Hello,
                    Can you hear me? It's me again.
                    '''})
#run the func
batch_mailing()
```

In order for the above code to work, you have to **at least** pass one recipient or passing a list of recipients in "to", and pass your recipients' list in **bcc** so that you won't accidentally disclose all emails in the "to" field while sending the email. If **no** variable passed in "to" key, a 404 error will be throwed in this case.
