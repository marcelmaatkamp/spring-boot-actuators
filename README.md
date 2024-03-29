# spring boot actuators
Small example on how to use spring boot actuators. This example will later be used to show how in kubernetes these mettrics with liveness and readiness signals will automically heal our application.

![](https://github.com/marcelmaatkamp/spring-boot-actuators/blob/master/images/Untitled%20Diagram.png?raw=true)

See for some excellent discussion the following pages:
 * https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-a-liveness-command 
 * https://cloud.google.com/blog/products/gcp/kubernetes-best-practices-setting-up-health-checks-with-readiness-and-liveness-probes
 * https://medium.com/spire-labs/utilizing-kubernetes-liveness-and-readiness-probes-to-automatically-recover-from-failure-2fe0314f2b2e
 
# prerequisites
 * java
 * gradle
 * docker
 
## mac osx
```
$ brew install sdkman jq
$ sdk install java 
$ sdk install gradle
```

# installation
```
$ git clone https://github.com/marcelmaatkamp/spring-boot-actuators.git &&\
  cd spring-boot-actuators &&\
  gradle bootRun
```

# run standalone
```
$ gradle bootRun
$ curl -s localhost:8081/actuator | jq --indent 1
{
 "_links": {
  "self": {
   "href": "http://localhost:8081/actuator",
   "templated": false
  },
  "auditevents": {
   "href": "http://localhost:8081/actuator/auditevents",
   "templated": false
  },
  "beans": {
   "href": "http://localhost:8081/actuator/beans",
   "templated": false
  },
  "caches-cache": {
   "href": "http://localhost:8081/actuator/caches/{cache}",
   "templated": true
  },
  "caches": {
   "href": "http://localhost:8081/actuator/caches",
   "templated": false
  },
  "health": {
   "href": "http://localhost:8081/actuator/health",
   "templated": false
  },
  "health-component-instance": {
   "href": "http://localhost:8081/actuator/health/{component}/{instance}",
   "templated": true
  },
  "health-component": {
   "href": "http://localhost:8081/actuator/health/{component}",
   "templated": true
  },
  "conditions": {
   "href": "http://localhost:8081/actuator/conditions",
   "templated": false
  },
  "configprops": {
   "href": "http://localhost:8081/actuator/configprops",
   "templated": false
  },
  "env": {
   "href": "http://localhost:8081/actuator/env",
   "templated": false
  },
  "env-toMatch": {
   "href": "http://localhost:8081/actuator/env/{toMatch}",
   "templated": true
  },
  "info": {
   "href": "http://localhost:8081/actuator/info",
   "templated": false
  },
  "loggers": {
   "href": "http://localhost:8081/actuator/loggers",
   "templated": false
  },
  "loggers-name": {
   "href": "http://localhost:8081/actuator/loggers/{name}",
   "templated": true
  },
  "heapdump": {
   "href": "http://localhost:8081/actuator/heapdump",
   "templated": false
  },
  "threaddump": {
   "href": "http://localhost:8081/actuator/threaddump",
   "templated": false
  },
  "metrics": {
   "href": "http://localhost:8081/actuator/metrics",
   "templated": false
  },
  "metrics-requiredMetricName": {
   "href": "http://localhost:8081/actuator/metrics/{requiredMetricName}",
   "templated": true
  },
  "scheduledtasks": {
   "href": "http://localhost:8081/actuator/scheduledtasks",
   "templated": false
  },
  "httptrace": {
   "href": "http://localhost:8081/actuator/httptrace",
   "templated": false
  },
  "mappings": {
   "href": "http://localhost:8081/actuator/mappings",
   "templated": false
  }
 }
}
$ curl -s localhost:8081/actuator/health | jq --indent 1
{
 "status": "UP",
 "details": {
  "diskSpace": {
   "status": "UP",
   "details": {
    "total": 170011897856,
    "free": 98954768384,
    "threshold": 10485760
   }
  }
 }
}

```

# run in docker
```
$ gradle jibDockerBuild
$ docker run -p 8080:8080 marcelmaatkamp/spring-boot-actuators
$ curl -s http://localhost:8080/actuator | jq --indent 1
```

# run in kubernetes
https://kubesail.com
```
$ brew install kubernetes-helm
$ helm create helm 
$ helm template helm \
  --name spring-boot-actuators \
  --set image.repository=marcelmaatkamp/spring-boot-actuators \
  --set image.tag=latest \
  --set image.pullPolicy=Always | kubectl apply -f -
$ kubectl get pods
NAME                                    READY   STATUS    RESTARTS   AGE
spring-boot-actuators-54c7f98f6-5f2qz   1/1     Running   0          59s
```
