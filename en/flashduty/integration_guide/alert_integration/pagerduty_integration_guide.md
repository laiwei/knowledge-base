---
brief: Alarm events can be pushed to Kuaimao StarCloud via the PagerDuty protocol, enabling automated noise reduction in the processing of alarm events
---

# PagerDuty Integration

---

Synchronization via webhook

Flashduty has implemented the PagerDuty Events API, with full compatibility for inputs and responses. Thus, you can push alarm events to Flashduty using the PagerDuty protocol to automate noise reduction processing.

Similarly, for alarm systems that already support event forwarding to PagerDuty (such as ElastAlert), simply modify the destination address to route events to Flashduty using the PagerDuty protocol.

## In Flashduty
---
使用专属集成

### Use Proprietary Integrations

When you do not need to route alarm events to different collaboration spaces, this method is preferred because it is simpler.


|+| Expand

    1. Enter the Flashduty console, select **Collaboration Space**, and navigate to the details page of a specific space
    2. Click on the **Integrated Data** tab, then click **Add an Integration** to proceed to the integration creation page
    3. Choose the **PagerDuty** integration and click **Save** to generate a card.
    4. Click on the generated card to view the **Push Address**, copy it for later use, and you're done.

### Use Shared Integrations

When you need to route alarms to different collaboration spaces based on the payload information of the alarm event, this method is preferred.


|+| Expand

    1. Enter the Flashduty console, select **Integration Center > Alert Events**, and go to the integration selection page.
    2. Select the **PagerDuty** integration:
    - **Integration Name**: Define a name for the current integration.
    3. Click **Save**, then copy the newly generated **Push Address** on the current page for future reference.
    4. Click **Create Route** to configure routing rules for the integration. You can route different alerts to different collaboration spaces based on specific conditions, or set a default collaboration space as a fallback, which can be adjusted as needed later on.
    5. Completed.


## In PagerDuty
---
### Request Address

```
{api_host}/event/push/alert/pagerduty
```

This address supports both the PagerDuty V1 and V2 Events API. **You must update the PagerDuty address to this one.**

### Pagerduty V2 Events

<div id="!"><h4>Reference documentation:</h4><p> [PagerDuty V2 Events](/0)</p><h4> Authentication method:</h4><p> Choose one of two ways:</p><ul><li> Method 1: Include parameter integration_key in QueryString</li><li> Method 2: Pass integration_key as routing_key parameter Payload</li></ul></div>

### Pagerduty V1 Events

<div id="!"><h4>Reference documentation:</h4><p> [PagerDuty V1 Events](/0)</p><h4> Authentication method:</h4><p> Choose one of two ways:</p><ul><li> Method 1: Include parameter integration_key in QueryString</li><li> Method 2: Pass integration_key as service_key parameter Payload</li></ul></div>

### Configuration Example

Take [ElastAlert2](https://github.com/jertel/elastalert2) as an example:

<div id="!"><ol><li>Step 1: Get the push address</li></ol><p> Fill in the integration name on the current page and save it, reopen the integration details, and copy the push address, such as:</p><pre> `{api_host}/event/push/alert/pagerduty?integration_key=xxx
`</pre><ol start="2"><li> Step 2: Modify push address</li></ol><p> Modify the source code corresponding to the deployed ElastAlert instance, [check diff](/0) :</p><img alt="drawing" width="600" src="https://fcdoc.github.io/img/bgbLujRxqtPVvOHrDFsFH6pmWYFB9d5p9AT2jnZtlxY.avif"><ol start="3"><li> Step 3: Report the alarm incident</li></ol><p> Follow the steps [of ElastAlert PagerDuty Push Configuration Document](/1) to configure alerts:</p><pre> `name: "b"
type: "frequency"
index: "pgy_audit*"
is_enabled: true
num_events: 1
realert:
minutes: 1
terms_size: 50
scan_entire_timeframe: true
timeframe:
minutes: 60
timestamp_field: "created_at"
timestamp_type: "unix_ms"
use_strftime_index: false
alert_subject: "Test {0} 123 aa☃ {1}"
alert_subject_args:
- "account_id"
- "operation"
alert_text: "Test {0}  123 bb☃ {1}"
alert_text_args:
- "request_id"
- "operation_name"
filter:
- query:
query_string:
query: "created_at:*"

# ------- FlashDuty ----------------
alert: pagerduty
pagerduty_service_key: xxx
pagerduty_client_name: wahaha
pagerduty_api_version: v2
pagerduty_v2_payload_class: ping failure
pagerduty_v2_payload_component: mysql
pagerduty_v2_payload_group: app-stack
pagerduty_v2_payload_severity: error
pagerduty_v2_payload_source: mysql.host.name
# ------- FlashDuty ----------------
`</pre><ol start="4"><li> Step 4: Restart ElastAlert , wait for the alarm to be triggered</li></ol></div>