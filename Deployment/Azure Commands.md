### Azure Commands

## Function Apps

This is a python function that you can deploy serveless so that it will scale with the number of calls. First we head to the Azure portal: 
```URL
https://portal.azure.com/#@cognitive.business
```
First we need to create our resource: 

- From the Azure portal menu or the Home page, select Create a resource.
- In the New page, select Compute > Function App.

On the Basics page, use the function app settings as specified in the following table:
```javascript
Resource Group = "Your resource Group"
Function App Name "Name that has to be globally unique"
```
Then click on Review & Create. Once deployed we can "Go To Resource", which will look like this:
 
 ![Example View of Function App](./images/image-1.png)

Below you need to click on "Create Function" and input these values into the fields: 
![Create Function](./images/image-3.png)
![Template Details](./images/image-4.png)

We want to have anonymous so that we don't have any security functions.

Next we want to navigate to our IDE. Here we need to have signed into Azure. Then you can navigate to the Azure section which has Resources. Here you will now see our deployed function: 

![Azure Resources](./images/image-2.png)

Now we want to create on our local machine the files that we would like to push to the portal. The structure of the files need to look like this:

```bash
YourProjectRoot/
├── .vscode
│
├── function-name/      # Your Azure Functions project
│   ├── function.json   # Provides information on the type of function that is being deployed "httpTrigger" etc.
│   └── __init__.py     # Other function app files and folders
│
├── requirements.txt    # This provides the function with your packages
├── host.json           # This provides information to the deployment package
└── ...                     
```
In our case we named the function "http_example" so the file structure would look like this:
```bash
cognitive-oil-test/
├── .vscode
│
├── http_example/
│   ├── function.json
│   └── __init__.py     
│
├── requirements.txt    
├── host.json           
└── ...                     
```
This is what the function.json looks like:

```json
{
    "bindings": [
      {
        "authLevel": "anonymous",
        "type": "httpTrigger",
        "direction": "in",
        "name": "req",
        "methods": [
          "get",
          "post"
        ]
      },
      {
        "type": "http",
        "direction": "out",
        "name": "$return"
      }
    ]
  }
  
```
Also make sure that the settings.json has the correct deploySubpath, the .vscode folder needs to be inside the workspace folder or it won't deploy:
```json
{
    "azureFunctions.deploySubpath": ".",
    "azureFunctions.scmDoBuildDuringDeployment": true,
    "azureFunctions.pythonVenv": ".venv",
    "azureFunctions.projectLanguage": "Python",
    "azureFunctions.projectRuntime": "~4",
    "debug.internalConsoleOptions": "neverOpen"
}
```
Once this has been set up we need to then insert our function. In this case I will just use something that returns "Hello World" 

```javascript
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    return func.HttpResponse(
         "Hello World",
         status_code=200
    )

```
Once the files have been set up correctly, we then right click on the folder that is our project name "http_example" and select "Deploy to Function App". I will ask you what resource you would like to push this function and what language it it in.

In order to find the api URL you then need to navigate to the function app and cliCK "Get Function URL", this will give you the raw URL but you will then also need the API keys which you can find in "Function Keys".

![alt text](./images/image-5.png)

The structure of your call should be: 

```
https://cognitive-oil-test.azurewebsites.net/api/{ functio name }?code={ function key }

For Example

https://cognitive-oil-test.azurewebsites.net/api/http_example?code={API_KEY}
```

You will then see the output of your code either in postman or in your browser.

### Errors

If you are having any problems with deployment, or errors, you need to navigate to the function page. Here, we will see the "Monitor" tab which will highlight all the potential errors:

![](./images/image-6.png)

