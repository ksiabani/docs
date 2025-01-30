# The AIandMe Firewall

The AIandMe firewall is a contextual filter acting as the actual firewall in the cybersecurity world. Its primary purpose is to analyse a user prompt and filter it accordingly, as per the business information added in the project configuration.

The AIandMe firewall is automatically deployed once you create your project. Just navigate to the project Settings → Integration page to find the custom endpoint and api key to integrate with your GenAI assistant you wish to protect:

![Screenshot 2025-01-26 at 13.31.37.png](./Screenshot_2025-01-26_at_13.31.37.png)

To use the endpoint you just need to issue a `POST` request to the projects specific endpoint. The endpoint is build in a serverless architecture to support scalability by design and is also optimised for low latency.

Find bellow a Python example for a firewall API request, with explanation of the input arguments and the output status codes and params:

```python
import os, request

# get the data bellow from your AIandMe account
FIREWALL_ENDPOINT = os.getenv("AIANDME_FIREWALL_ENDPOINT", "")
FIREWALL_APIKEY = os.getenv("AIANDME_FIREWALL__APIKEY", "")

# 1. Ping the API Firewall
resp = requests.post(
	FIREWALL_ENDPOINT,
	headers = {
		"Content-Type": "application/json",
		"X-Api-Key": FIREWALL_APIKEY
	}, 
	json = {
		"messages": [{
			"role": "user",
			"content": "...put here the user's prompt to filter..."
		}]
	}
)

# 2. Handle response
"""
	If no error occured (i.e., status_code = 200 | 406, then resp is a json as follows:
		resp = {
			"log_id": "...a unique ID for the delivered assesment log...",
	    "explanation": "pass | off_topic | violation | restriction"
		}
  else,
	  resp = {
		    "message": "...error explanation message..."
			}
"""
if resp.status_code == 200:
	# PASS - the user prompt is ok to pass to the underlying GenAI agent.
	...
elif resp.status_code == 406:
	# FAIL - the user prompt should be filtered (either `off_topic`, `violation`, `restriction`).
	...
elif resp.status_code == 429:
	# ERROR - Quota exceeded (cannot write logs).
	...	
elif resp.status_code == 403:
	# ERROR - Authorization failed. Probably wrong API key.
	...
else:
	# ERROR - Not related to authorisation (probably 500 error).
	...
```

Each firewall API call request produces a log entry - the log ID is returned in the JSON response of the API call.  Bellow we show two examples of the related logs, created after a firewall API call.

![Screenshot 2025-01-26 at 13.38.58.png](./Screenshot_2025-01-26_at_13.38.58.png)

![Screenshot 2025-01-26 at 13.38.47.png](./Screenshot_2025-01-26_at_13.38.47.png)

**IMPORTANT: To improve the efficiency of the firewall, the API returns in two phases:**

1. Initially delivers a log ID and a general assessment (PASS | FAIL) for fast user response handling
2. An explanation of the assessment rationale that can be accessed on a later stage via the log ID of phase 1

**Please note that the AIandMe firewall is released as an open source software, to promote the safe and responsible use of AI and assist the development of the AI tech ecosystem. More details in the official github repo, [here](https://github.com/aiandme-io/firewall).**