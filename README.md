# Plausible Analytics Helm Chart for Kubernetes
 
Plausible Analytics is a Simple, lightweight privacy-friendly website analytics  alternative to Google Analytics.

## Install Chart

```shell
helm repo add david-crty https://david-crty.github.io/plausible-helm-chart/
helm repo update

# Helm 3
$ helm install [RELEASE_NAME] david-crty/plausible-analytics
```


## Requirements

Before you start make sure you have the following dependencies ready and working: 

- Helm > 3
- Postgres DB

## Configuration

- Plausible specific values.
- Clickouse specific values. 
> The shown values represent defaults and comments provide a better description if needed. 

```yaml
# Default values for plausible-analytics.

# Plausible specific values

disableAuth: false # Disables authentication completely, no registration, login will be shown.
disableRegistration: false # Disables registration of new users, keep your admin credentials handy ;)
baseURL: # The hosting url of the server, used for URL generation. In production systems, this should be your ingress host.

adminUser:
  email:  # Admin user's email
  name:   # Display name admin user
  password: # Admin user password

# SECRET_KEY_BASE is Helm randAlphaNum 90

database: # Postgres Database
  enabled: true
  url: "postgres://user:password@0.0.0.0:5432/plausible" # The URL to the Postgres Database Connection String see -> https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING

clickhouse: # Clickhouse Database
  enabled: true
  url: "http://plausible-clickhouse:8123/plausible_events_db" # The URL Connection String to clickhouse DB see -> https://clickhouse.tech/docs/en/interfaces/http/

smtp: # Plausible uses and SMTP server to send transactional emails e.g. account activation, password reset, weekly reports, etc.
  enabled: false # Enable/Disable SMTP functionality
  mailer:
    emailAddress: # the email address of the email sender
    adapter:
  host: # The host address of your smtp server.
  port: # The port of your smtp server.
  username: # The username/email in case SMTP auth is enabled.
  password: # The password in case SMTP auth is enabled.
  ssl:
    enabled: false # If SSL is enabled for SMTP connection
  retries: 2 # Number of retries to make until mailer gives up.

postmark: #Alternatively, you can use Postmark to send transactional emails. In this case, use the following parameters:
  apiKey:

geolocation: # MaxMind geolocation database#
  enabled: false # Enable/Disable the automated fetch of
  account_id: # Account/User ID from maxmind.com
  license_key: # My License Key from maxmind.com

# Google Search Integration
# See: https://docs.plausible.io/self-hosting-configuration#google-search-integration
google:
  clientID: # The Client ID from the Google API Console for your Plausible Analytics project
  clientSecret: # The Client Secret from the Google API Console for your Plausible Analytics project
#Twitter Integration
# https://docs.plausible.io/self-hosting-configuration#twitter-integration
twitter:
  consumer:
    key: # The API key from the Twitter Developer Portal
    secret: # The API key secret from the Twitter Developer Portal
  access:
    token: # The access token you generated in the steps above
    secret: # The access token secret you generated in the steps above

labels: {} # Extra Labels to apply on your k8s deployment
extraEnv: [] # Extra Env Variables that are passed down to plausible 1:1


# Default form Helm Generator

replicaCount: 1

image:
  repository: plausible/analytics
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: ""

imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""

serviceAccount:
  # Specifies whether a service account should be created
  create: true
  # Annotations to add to the service account
  annotations: {}
  # The name of the service account to use.
  # If not set and create is true, a name is generated using the fullname template
  name: ""

podAnnotations: {}

podSecurityContext: {}
# fsGroup: 2000

securityContext: {}
  # capabilities:
  #   drop:
  #   - ALL
  # readOnlyRootFilesystem: true
  # runAsNonRoot: true
# runAsUser: 1000

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  annotations: {}
    # kubernetes.io/ingress.class: nginx
  # kubernetes.io/tls-acme: "true"
  hosts:
    - host: chart-example.local
      paths: []
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local

resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  # limits:
  #   cpu: 100m
  #   memory: 128Mi
  # requests:
  #   cpu: 100m
#   memory: 128Mi

autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80
  # targetMemoryUtilizationPercentage: 80

nodeSelector: {}

tolerations: []

affinity: {}

clickhouseServer:
  timezone: Europe/Paris
  clickhouse:
    replicas: 1
    imageVersion: 20.8
    persistentVolumeClaim:
      enabled: true
      dataPersistentVolume:
        enabled: false # Set to true to enable persistence
        storageClassName: local # set storage class
        storage: "5Gi"
      logsPersistentVolume:
        enabled: false
    configmap:
      logger:
        console:
          level: warning
      remote_servers:
        replica:
          backup:
            enabled: false
  tabix:
    enabled: false
```

## Special Thanks

Source Code for this Helm Chart [8gears/plausible-analytics-helm-chart](https://github.com/8gears/plausible-analytics-helm-chart)
