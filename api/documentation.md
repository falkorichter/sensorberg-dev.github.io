---
layout: page
title: API Documentation
permalink: /api/index.html
comments: true

---

# Notes:
We are using JSONAPI

# endpoints

currently we offer these endpoints:

```
 namespace :api do
    namespace :internal do
      jsonapi_resources :beacons
      jsonapi_resources :geofences
      jsonapi_resources :trigger_groups
      jsonapi_resources :gateways
      jsonapi_resources :gateway_groups
      jsonapi_resources :interactions
      jsonapi_resources :scenarios
      jsonapi_resources :event_reaction_log_entries, only: [:index, :show]
    end
end
```
Note: in the URLs, dashes are used. `event_reaction_log_entries`becomes [portal.sensorberg.com/api/internal/event-reaction-log-entries](https://portal.sensorberg.com/api/internal/event-reaction-log-entries)



# PostMan 

## collection
Available for download at [SensorbergAPI.postman_collection](SensorbergAPI.postman_collection)

## variables:
* `api_base_url` must be set to `https://portal.sensorberg.com`
* `auth_token´ must be set to your auth token available at [https://portal.sensorberg.com/manager/user_auth_tokens/1](portal.sensorberg.com/manager/user_auth_tokens/1)

# filtering
http://jsonapi.org/format/#fetching-filtering
http://jsonapi-resources.com/v0.9/guide/resources.html#Filters

sample

```
curl -X GET \
  'https://portal.sensorberg.com/api/internal/beacons?filter%5Bcreator-company-id%5D=f687a5b5-c9c2-4324-8221-35c0a1f22999' \
  -H 'authorization: Token token=5d7a06e4-98e2-48c3-9917-e92d4b9d05c0' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/vnd.api+json' \
```

# pagination
http://jsonapi-resources.com/v0.9/guide/resources.html#Pagination


sample:

```
curl -X GET \
  'https://portal.sensorberg.com/api/internal/beacons?page%5Bsize%5D=1&page%5Bnumber%5D=2' \
  -H 'authorization: Token token=5d7a06e4-98e2-48c3-9917-e92d4b9d05c0' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/vnd.api+json' \
```