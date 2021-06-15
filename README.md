# PowerShell Zendesk Tutorial

This exercise will get you familiar working with Zendesk API in conjunction with PowerShell. It will also introduce PowerBI.

Zendesk is a customer service software that offers a ticketing system, among others, to help track, prioritise and solve customer support interaction. This exercise will focus on manipulating tickets.

Windows PowerShell is a Microsoft framework for automating tasks using command-line shell and an associated scripting language. This exercise will involve writing scripts on PowerShell to parse information to and fro Zendesk.

PowerBI is a business analytics solution that creates robust visualisation of data in order to share insights.

## Exercise

This exercise will involve the following deliverables:
1.  Use PowerShell and the Zendesk API to generate a few hundred tickets using randomized data.
2.  Use PowerShell and the Zendesk API to extract the ticket data into a CSV.
3.  Connect to the CSV with PowerBI and generate a report with insights like:
	1.  Number of open tickets
	2.  Number of closed tickets
	3.  Age of open tickets
	4.  Breakdown of ticket status in a pie chart

Zendesk makes use of REST API. 

## Getting Started

### Setting up Zendesk
Create a free-trial account with Zendesk at [https://www.zendesk.com/](https://www.zendesk.com/). Note that the free-trial lasts for 2 weeks only.

Once in, navigate the dashboard and find the '**Admin**' option at the toolbar on the left. From **Admin > Channels > API**, enable '**Password Access**'. It is recommended to also enable 'Token Access' when using APIs across any services. However, for simplicity sake, this will be ignored for the exercise.

To learn more about Zendesk's API, visit [https://developer.zendesk.com/rest_api/docs/zendesk-apis/resources](https://developer.zendesk.com/rest_api/docs/zendesk-apis/resources)

### Setting up PowerShell

Ensure that the PowerShell execution policy is set to one that gives more privileges. If it has not been configured so, run PowerShell as an Administrator and enter:

    Set-ExecutionPolicy RemoteSigned

**RemoteSigned execution policy** is designed to prevent remote PowerShell scripts and configuration files that aren't digitally signed by a trusted publisher from running or loading automatically. This will suffice in most cases.

### Setting up PowerBI

Access PowerBI from [https://powerbi.microsoft.com/en-us/](https://powerbi.microsoft.com/en-us/) using your company's credentials. Download the desktop app if wished.

## Create Tickets

The first deliverable would be using PowerShell and the Zendesk API to generate tickets.

The Zendesk API in question to be used would be 'Create Ticket'.

    POST /api/v2/tickets.json

See [https://developer.zendesk.com/rest_api/docs/support/tickets#create-ticket](https://developer.zendesk.com/rest_api/docs/support/tickets#create-ticket) for more information.

Create a PowerShell script with the following code inside:

    $user = "{username}"
    $pass = "{password}"
    $uri = "https://{domainname}.zendesk.com/api/v2/tickets.json"
    
    For ($i=0; $i={x}; $i++) {
    	$json = '{"ticket": {"subject": "Test, "comment": { "body": "Test"}}}'
    
    	$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass)))
    
    	Invoke-RestMethod -Uri $uri -Method Post -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -ContentType "application/json" -Body $json
    }

Replace {username} and {password} with your appropriate credentials.  Replace {x} with the amount of tickets you want to create.

This script will essentially iterate a call to the 'create ticket' API a fixed number of times. It uses the Invoke-RestMethod, a PowerShell cmdlet, to call the API.

## Create CSV

The second deliverable would be to extract ticket data into a CSV file.

The Zendesk API in question to be used would be 'Search'.

    .../api/v2/search.json?query={search_string}

See [https://developer.zendesk.com/rest_api/docs/support/search](https://developer.zendesk.com/rest_api/docs/support/search)
for more information.

In this case, the {search_string} will be **type:ticket**

Create a PowerShell script with the following code inside:

    $user = "{username}"
    $pass = "{pass}"
    $uri = "https://{domainname}.zendesk.com/api/v2/search.json?query=type:ticket"
    
    $base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass)))
    
    $response = Invoke-RestMethod -Uri $uri -Method Get -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -ContentType "application/json"
    
    $tickets = $response.results
    $tickets | Export-Csv -Path "C:\tickets.csv" -NoTypeInformation

Replace {username} and {password} with your appropriate credentials.

This script will essentially call the 'search' API, find all the tickets inside the system, take the results of the response and pipe it into a CSV file.

## PowerBI

Once inside PowerBI, navigate to the 'Get Data' option and select the appropriate file format, in this case, CSV.
