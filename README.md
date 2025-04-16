# Fetch-SRE-takehome
## Installation:
Run the requirements.tx file using command 
pip3 install -r requirements.txt
## Execute:
python3 fetch_sre.py sample.yaml

## Description
fetch_sre.py contains code to
fetch the data from sample.yaml file
for every request check if the endpoint is available considering two conditions: 1. status code must be between 200 and 300 and 2. response time of the request must be less than 500 ms.
If above condition satisfies, then return 'up' else 'down'. Then continue with the next request in the file.
Once above cycle is completed, return the cumulative availability overall.
Continue the cycle and log availability results for every 15 seconds.

## How existing bugs were identified:
### 1. Replaced json with data during api call
Observed sample.yaml file to get a brief understanding of input data. Noticed data passed to body section is in string rather a JSON/Dictionary format.
Executed the code and confirmed above. Debugged for validation and witnessed error. It indicated something wrong with data (4xx error). Investigated and modified the code replaced the parameter JSON with data during api call. It worked.

### 2. Requests came up with empty method
In this case, it is safe to assume that the client is either trying to eshtablish connection with the host or must have been forgotten to include the method.
I think, given the benefit of doubt, it is possible for an engineer to use either HEAD or GET method. I tested both, and convinced to use GET.
 
### 3. Third and Fourth test cases went error
While fixing **1. Replaced json with data during api call** I know that 3rd test case might fail because of empty data. I believe the endpoints were setup in such a way that it api call doesn't accept an empty body.
Well, the last case went on for 500 error. I think this should be something to deal while designing an API.

### 4. Must ignore port numbers while determining domain
While the given code satisfied most of the other requirements, I think it lags this test case. While backtesting I identified if a url in the sample.yaml file contains domain:port, it doesn't align with the requirment.
Inital thought was to split the domain at : (colon), later-on realized IPv6 could break this logic. So imported library _urlparse_ to fetch domain and ignoring port. Introduced a method url_parse.

### 5. Including timeout parameter in the request
I intentionally set the timeout to 0.5 seconds, since the expected response time is ≤ 500ms. In other words, if establishing a connection takes longer than 0.5 seconds, there's no point in continuing — the request would be marked as 'DOWN' anyway.

## Testing
I tested the code by tweaking the sample.yaml file — removed the headers to observe the behavior without them, experimented with different HTTP methods like GET and HEAD, and added a custom port (e.g., :portnumber) to verify how the code handles port-specific endpoints.
Interestingly, I noticed the availability varies based on network, CPU usage. Below is what happend.
When executed from command prompt (Using CPU and WiFi), I see 0% availability per cycle and response time varies ~800ms for successful responses, whereas when tested in Google Colab (VM), availablity is 50% per cycle.

<img width="806" alt="image" src="https://github.com/user-attachments/assets/425771b2-4d47-458f-9056-289239d5cb1d" />
<sub>Local machine:</sub>


<img width="944" alt="image" src="https://github.com/user-attachments/assets/0e2bf1ec-8ef4-49f8-b9dc-f47c39560cca" />
<sub>Google colab:</sub>


Response time for fourth test case has not been recorded because it took more than 0.5 sec, to eshtablish connection and hence it went on to hit exception. Since this program does't handle Time Exception, it continues to execute with next record from the .yaml file.
