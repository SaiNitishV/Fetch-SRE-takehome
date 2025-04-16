# Fetch-SRE-takehome
**Installation**:
Run the requirements.tx file using command 
pip3 install -r requirements.txt
**Execute**:
python3 fetch_sre.py sample.yaml

fetch_sre.py contains code to
> fetch the data from sample.yaml file
> for every request check if the endpoint is available considering two conditions: 1. status code must be between 200 and 300 and 2. response time of the request must be less than 500 ms.
> If above condition satisfies, then return 'up' else 'down'. Then continue with the next request in the file.
> Once above cycle is completed, return the cumulative availability overall.
> Continue the cycle and log availability results for every 15 seconds.

How existing bugs were identified:
**1. Replaced json with data during api call**
>  Observed sample.yaml file to get a brief understanding of input data. Noticed data passed to body section is in string rather a JSON/Dictionary format.
>  Executed the code and confirmed above. Debugged for validation and witnessed error. It indicated something wrong with data (4xx error). Investigated and modified the code replacing JSON to data parameter during api call. It worked.
________
**2. Requests came up with empty method**
> In this case, it is safe to assume that the client is either trying to eshtablish connection with the host or must have been forgot to include the method.
> I think, given the benefit of doubt, it is possible for an engineer to use either HEAD or GET method. I'm convienced to use HEAD because, client wants to know if the server is up. So they are not expecting a response body. In that case, for the API to be lighter I used HEAD.
________
**3. Third and Fourth test cases went error**
> While fixing **1. Replaced json with data during api call** I know that this test case might fail because of empty data. I tried to using data, json parameters but still the output landed in error. I believe the endpoints were setup in such a way that it api call doesn't accept an empty body.
> Well the last case went on for 500 error. I think this should be something to deal while designing an API. (Not sure if that endpoint is available)
**Availability**
> Leveraged _time_ library and calculated start and stop times of a request. Since the requirements is in ms, obtained difference has been multiplied by 1000.
_____________
Below yet to implement
**4. Must ignore port numbers while determining domain**
> While the given code satisfied most of the other requirements, I think it lags this test case. While backtesting I identified if a url in the sample.yaml file contains domain:port, then the program would crash.
> Since, the requirements confirms that YAML format must not be changed (like sample.yaml), I take this as a confirmation that the domain is always like dev-sre-take-home........ instead of [123.45.6.78] this format.
> So with above assumption as soon as we see the domain, check if the domain has a port with a ':' (colon) indicator. if yes, modify domain to ignore the port.
