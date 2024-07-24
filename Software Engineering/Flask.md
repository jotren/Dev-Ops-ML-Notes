
# Flask Application #

Flask is an application you can delpoy in a local or VM machine that enables you to share data over the internet via an API (application programming interface)
```
from flask import Flask
```
When you run the command "Flask run" you need to tell the computer which file to look for. Now in pyhton if you insert \_\_name_\_ anywhere, it will take the name of the python program. So if you name the program Flask.py the \_\_name_\_  will return 'Flask'

This command tells the application to use a HTML code of its choosing and the '/' indicates that you don't want any other additions to the HTML code such as '/api' would give you http://127.0.0.1:5000/api:

```Python
app = Flask(__name__)
@app.route('/insert-tag')
```

You then tell the flask applciation what you would like to return
```Python
def index():
  return "Hello World"
```

Finally you can also designate the port number, usually wise to use a port number above 3000 with 5000 often free. You can think of the port number like a lane in a motorway. Port numbers are used by the internet to highlight what the endpoint is:
- 443 = HTTP Secure
- 20 = File Transfer
Each number tells the computer what kind of information it is recieving
The command below is important as it works as a boiler plate that protects users from accidentally invoking the script when they don't intend to.
```Python
if __name__ == '__main__':
    app.run(port=5000)
```
With all these components in place we then need to define what the user wants to return, which is the below example is a df convereted into a string

```Python
from flask import Flask
import os
import pandas as pd

app = Flask(__name__)

df = pd.read_csv("C:/Users/jtren/Documents/FlaskApp/Docker_files/API-key.csv", sep="delimiter", header=None, engine="python")

string = df.to_string(index=False)

@app.route('/')
def index():
  return string

if __name__ == '__main__':
    app.run(port=5000)
    
```
