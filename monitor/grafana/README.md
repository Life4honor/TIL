# Grafana

## Install Grafana

### Docker
```sh
$ docker pull grafana/grafana
```

## Managing Dashboard 

### Edit default admin user
User can edit the default admin user By uncommenting the `admin_user`, `admin_password` fields in `grafana.ini`'s `security` tab

> Docker container's default configuration file path is predefined as `${GF_PATHS_CONFIG}`

**Default `grafana.ini`**
```ini
...
[security]
# default admin user, created on startup
;admin_user = admin

# default admin password, can be changed before first start of grafana,  or in profile settings
;admin_password = admin

...
```

**`grafana.ini` After editing the `admin_user`, `admin_password` fields**
```ini
...
[security]
admin_user = life4honor
admin_password = life4honor-pw

...
```
### Export dashboard configuration as JSON
User can export the customized dashboard as JSON like `dashboard.json`.

### Add configuration yaml
grafana's code based configuration became available recently just by adding `yaml` file on `${GF_PATHS_PROVISIONING}`

> Docker container has the default value of `GF_PATHS_PROVISIONING` as a env variable

### Dashboard
```yaml
# dashboard.yaml
apiVersion: 1
providers:
  - name: "<Unique Provider Name>"
    type: file
    disableDeletion: false
    editable: true
    updateIntervalSeconds: 30
    allowUiUpdates: true
    options:
      path: /var/lib/grafana/dashboards
```

> To import the customized dashboard with file, the `dashboard.json` file path must be matched with the value of `providers.options.path`

### Datasource
```yaml
# datasource.yaml
apiVersion: 1
datasources:
  - name: Elasticsearch
    type: elasticsearch
    access: proxy
    url: <Elasticsearch_Endpoint>
    database: "<Index Name>"
    basicAuth: true
    basicAuthUser: <user>
    jsonData:
      esVersion: 70
      timeField: "@timestamp"
    secureJsonData:
      basicAuthPassword: <password>
    version: 1
    editable: false

  - name: Prometheus
    type: prometheus
    access: proxy
    url: <prometheus_endpoint>

```

### Notifier
```yaml
# notifier.yaml
notifiers:
  - name: teams
    type: teams
    uid: teams-channel
    is_default: true
    send_reminder: false
    disable_resolve_message: true
    settings:
      url: <teams_webhook_URL>
```
> To add notification to dashboard, the value of `panels.alert.notifications.uid` in `dashboard.json` must be matched with the value of `notifiers.uid` in `notifier.yaml`
