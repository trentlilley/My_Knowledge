# Overview
This tutorial teaches you how to create a static website using AWS S3.

# Concepts
- You will upload all of your HTML and images to S3 and ensure all of these resources have public access so that browsers can retrieve them.

# Steps
1. Go to AWS Dashboard and navigate to S3. Create a new bucket

2. When creating a new bucket, give it a unique name and uncheck block all public access. Use default settings for everything else.

3. Upload all of your website pages and images into the bucket.

4. Configure a permissions policy to allow public access using the bucket's ARN

5. Return to the page listing the buckets and go to the properties tab. Scroll all the way down to see static website hosting

6. Specify the html page that should be your website's home page. Now enter the bucket name in your address bar (or select it from static website hosting on the properties tab) and see that it works.
