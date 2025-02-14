# The AIandMe Firewall

The AIandMe firewall is a contextual filter acting as the actual firewall in the cybersecurity world. Its primary purpose is to analyse a user prompt and filter it accordingly, as per the business information added in the project configuration.

The AIandMe firewall is automatically deployed once you create your project. Just navigate to the project Settings → Integration page to find the custom endpoint and api key to integrate with your GenAI assistant you wish to protect:

![Screenshot 2025-01-26 at 13.31.37.png](./Screenshot_2025-01-26_at_13.31.37.png)

To use the endpoint you just need to issue a `POST` request to the projects specific endpoint. The endpoint is build in a serverless architecture to support scalability by design and is also optimised for low latency.

Find bellow a Python example for a firewall API request, with explanation of the input arguments and the output status codes and params:


## Installation
Install using pip:

```bash
pip install aiandme

```

## Dependencies
The AIandMe FirewallOS lib relies on the `Pydantic` lib for data validation (schemas).


## An integration example

```python
# include basic libs
from aiandme import (
    Firewall,
    AIANDME_Firewall_CannotDecide,
    AIANDME_Firewall_NotAuthorised,
)
from aiandme.schemas import Integration as IntegrationSchema

# initialise with project data from the platform (endpoint + api key)
fw = Firewall(
    IntegrationSchema(
        endpoint="...",
        api_key="...",
    )
)

# use the firewall
try:
    u_prompt = "...put here the prompt you wish to analyse..."
    r = fw.eval(u_prompt)

    # firewall assessment is completed -> handle the assessment
    if r.status:
        # prompt passes => pass it to your assistan
        pass

    # prompt fails => handle according the fail case
    elif r.fail_category == "off_topic":
        print("off-topic")
    elif r.fail_category == "violation":
        print("intent violation")
    elif r.fail_category == "restriction":
        print("restriction triggered")

except AIANDME_Firewall_CannotDecide:
    # handle here the case were the Firewall could not assess the user prompt
    # probably out of context, but not very sure
    print("Could not decide...")
except AIANDME_Firewall_NotAuthorised:
    # handle here the case you have used a wrong credentials
    # make sure you copy-paste correctly from your AIandMe app
    print("Please check the firewall api credentials.")
except:
    # something else went wrong.
    print("Something else went wrong.")
```

Each firewall API call request produces a log entry - the log ID is returned in the JSON response of the API call.  Bellow we show two examples of the related logs, created after a firewall API call.

![Screenshot 2025-01-26 at 13.38.58.png](./Screenshot_2025-01-26_at_13.38.58.png)

![Screenshot 2025-01-26 at 13.38.47.png](./Screenshot_2025-01-26_at_13.38.47.png)

**IMPORTANT: To improve the efficiency of the firewall, the API returns in two phases:**

1. Initially delivers a log ID and a general assessment (PASS | FAIL) for fast user response handling
2. An explanation of the assessment rationale that can be accessed on a later stage via the log ID of phase 1

**Please note that the AIandMe firewall is released as an open source software, to promote the safe and responsible use of AI and assist the development of the AI tech ecosystem. More details in the official github repo, [here](https://github.com/aiandme-io/firewall).**