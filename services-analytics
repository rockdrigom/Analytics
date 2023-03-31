### DEVELOPED BY PAGERDUTY PROFESSIONAL SERVICES/SUCCESS ON DEMAND
### THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
### IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
### FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
### AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
### LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
### OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
### THE SOFTWARE.

### This code gets analytics API per service DATA max 3 years

from pdpyras import APISession
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import *

API_ACCESS_KEY = 'YOUR READ ONLY API HERE'


session = APISession(API_ACCESS_KEY)
early_access_header = {"X-EARLY-ACCESS": "analytics-v2"}
timeFrame = {"aggregate_unit": "day"}

session.headers.update(early_access_header)
session.headers.update(timeFrame)

years_to_get = 4

today = datetime.today()
start_date = today - relativedelta(years=int(years_to_get - 1))


list_priorities = []
list_analytics_service = pd.DataFrame()

for x in range(years_to_get):
    start = start_date + relativedelta(years=int(x))
    end = start_date + relativedelta(years=int(x + 1),days=-1)
    offset = 0

    response = session.get("/priorities")
    list_priorities = response.json()["priorities"]

    for p in list_priorities:

        body = {"filters": {
            "created_at_start": str(start.replace(hour=00, minute=00, second=00)),
            "created_at_end": str(end.replace(hour=23, minute=59, second=59)),
            "urgency": "high",
            "priority_names": [p['summary']]},
            "aggregate_unit": "day"
            }
        out = session.post("/analytics/metrics/incidents/services", json=body).json()

        dataframe_analytics_services = pd.json_normalize(out["data"], max_level=None)
        dataframe_analytics_services["priority"] = p['summary']
        dataframe_analytics_services["urgency"] = 'high'
        list_analytics_service = pd.concat([list_analytics_service, dataframe_analytics_services], ignore_index=True, axis=0)

        body = {"filters": {
            "created_at_start": str(start.replace(hour=00, minute=00, second=00)),
            "created_at_end": str(end.replace(hour=23, minute=59, second=59)),
            "urgency": "low",
            "priority_names": [p['summary']]},
            "aggregate_unit": "day"
        }
        out = session.post("/analytics/metrics/incidents/services", json=body).json()

        dataframe_analytics_services = pd.json_normalize(out["data"], max_level=None)
        dataframe_analytics_services["priority"] = p['summary']
        dataframe_analytics_services["urgency"] = 'low'
        list_analytics_service = pd.concat([list_analytics_service, dataframe_analytics_services], ignore_index=True, axis=0)
