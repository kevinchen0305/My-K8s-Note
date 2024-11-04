# Service

# Ingress
Create ingress with obove yaml or:
```
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tekton-dashboard
  namespace: tekton-pipelines
spec:
  ingressClassName: nginx
  rules:
  - host: tekton.dashboard.io
    http:
      paths:
      - backend:
          service:
            name: tekton-dashboard
            port:
              number: 9097
        pathType: ImplementationSpecific
EOF
```
after creating ingress
```
vim /etc/hosts
```
add
```
127.0.0.1 ${your hostname}
```
