---
### Title the install tools article with the name of the tool to be installed
### Include vendor name where appropriate
title: Azure login

### Optional additional search terms (one per line) to assist in finding the article
additional_search_terms:

### Estimated completion time in minutes (please use integer multiple of 5)
minutes_to_complete: 10

### Link to official documentation
official_docs: https://learn.microsoft.com/en-us/cli/azure/reference-index?view=azure-cli-latest#az-login

### PAGE SETUP
weight: 1                       # Defines page ordering. Must be 1 for first (or only) page.
tool_install: true              # Set to true to be listed in main selection page, else false
multi_install: false            # Set to true if first page of multi-page article, else false
multitool_install_part: false   # Set to true if a sub-page of a multi-page article, else false
layout: installtoolsall         # DO NOT MODIFY. Always true for tool install articles
---

Before moving ahead make sure that azure cli is installed in your local sysytem. To install azure cli, follow this [documentation](/install-guides/azure-cli).

In this section you will see on how to authenticate to the Azure environment using Azure CLI. Once logged in, the CLI allows you to query and interact with the cloud resource.

Feel free to seek out additional azure authentication tutorials or add more information to this page.

## Sign in interactively

Run the following command to initiate the login session:

```console
az login
```

The output will be similar as shown below.

```output
ubuntu@ip-172-31-17-218:~$ az login
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code E5NKJA2HM to authenticate.
```

Open the link in a browser and enter the code that you receive in the above message. Then login into azure account. You will see details in command line as below after logging in browser.

![image](https://user-images.githubusercontent.com/42368140/197953418-ddb9cd41-72b9-4a97-88f1-1f490644f36b.PNG)
