
---

# Simple, One-way Integration to Notify From Terraform (OSSS, the Open Source version)
This is a simple one-way integration to enable you to fire notifications and alerts from within your Terraform scripts, for example when a new EC2 instance is successfully deployed.

I call it an integration but it's more the methodology to enable you to join the two systems together.


[Watch the video](https://www.youtube.com/watch?v=5jYkxs63Qjo)


# How it works

The set of files used to describe infrastructure in Terraform is simply known as a Terraform configuration, when you 'run' them they do stuff.  One of the things you can do as part of that configuration is run a local command... we use that to run a CURL command to trigger an integration in xMatters.

The information on what's sent and to whom etc is all put into the Terraform configuration script, there's a possibility that could use the variables and properties within the script but for my example I've hard coded the information.



# Pre-Requisites

* Terraform installed on a server
* An xMatters account.
* Developer access.


# Files

* [Terraform.zip](Terraform.zip) - The comm plan (that has all the scripts and such).


# Installation


## The set up (xMatters)

1. Install the Terraform communication plan attached.
2. Edit the Terraform Message form, remove voice call (I haven't set much of a format for this, you could spruce it up yourself).  You don't need a recipient on this one, that will be passed though by the Terraform script. Set to Web Service only.
3. Head into Integration Builder and find the Terraform Incoming inbound integration script. Click on View Instructions at the bottom of the page where you edit the incoming integration and copy the CURL command.

## The set up (Terraform)

`provider "aws" { 
`	access_key = "myaccesskey" 
`	secret_key = "mysecretkey" 
`	region = "us-east-1" 
`} 

`resource "aws_instance" "example" { 
`	ami = "ami-c998b6b2" 
`	instance_type = "t2.micro" 
	
provisioner "local-exec" { command = "curl --request POST --header 'Content-Type: application/json' --data '{\"properties\": {\"message\": \"The message you want to send\",\"subject\": \"The message subject\"},\"recipients\":[\"xMattersusername orgroupname\"]}' \"https://mydomain.xmatters.com/api/integration/1/functions/a1d8e257-aaaa-bbbb-cccc-fd5df7f48606/triggers?apiKey=myxMattersapikey\"" } }`



# Testing

Apply your configuration in terraform using terraform apply (once you've done the whole init and plan bits), which will go build your EC2 instance or whatever.  At the appropriate stage it will trigger xMatters and send out your notification!


# Troubleshooting

Check the activity stream on the inbound integration.
If you're still stuck, reach out to an xPert. 
