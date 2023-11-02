# Kubernetes Notes
 [Course Link](https://www.educative.io/courses/practical-guide-to-kubernetes)

## Content

1. Pods
2. ReplicaSets
3. Services
4. Deployments
5. Ingress
6. Volumes
7. [ConfigMaps](## ConfigMaps)

## ConfigMaps

ConfigMap takes a configuration from a source and mounts it into running containers as a volume.
For setting ConfigMap use this command after creating the cluster:
```kubectl config current-context```

ConfigMap takes a config from a source and **mounts** it into running containers as a **volume**.
Configs can be injected from different sources:
- from a file, multiple files or directory:

  ```kubectl create cm my-config --from-file=prometheus-conf.yml```
  
  ```kubectl create cm my-config --from-file=prometheus-conf.yml  --from-file=file.yml``` 
  
  ```kubectl create cm my-config --from-file=directory```
  
  Verify:
  ```kubectl exec -it alpine -- ls -l /etc/config```
  
  ```kubectl describe cm my-config```

- from key-value literals:
  ```kubectl create cm my-config --from-literal=key=value```

  Verify:
  ```kubectl exec -it alpine -- cat /etc/config/key```

 - from env file:
   ```kubectl create cm my-config --from-env-file=env.yml```

  With **env.valueFrom.configMapKeyRef** syntax, we need to specify each ConfigMap key separately. 
  That gives us control over the scope and the relation with the names of container variables.
  
  The **envFrom.configMapRef** converts all ConfigMap’s data into environment variables. 
  That is often a better and simpler option.

 - define as YAML:
  ```
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: prom-conf
  data:
    prometheus.yml: |
      global:
        scrape_interval:     15s
```

The ConfigMap object’s data section has only one key (prometheus.yml). Once this ConfigMap is mounted as a volume, 
the name of the file will be the same as the key (prometheus.yml).

The value has a bit of “special” syntax. Unlike the previous example where the value was a single word written directly 
after the colon, the structure of the value is now a bit more complex. To be more precise, it contains multiple lines.

When working with a large value, we can start with the pipe sign (|). Kubernetes will interpret the value as “everything
 that follows, as long as it is indented.” You’ll notice that all the lines of the value are at least two spaces to the
 right of the beginning of the key (prometheus.yml). If you’d like to insert an additional key, all you’d need to do is 
 to add it on the same level (indentation), as the other prometheus.yml.      
     
**Make sure** configMap IS NOT defined in the same file as the objects that mount it.

