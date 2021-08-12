# Create cluster:
    kind create cluster --config ./kind-config.yaml
# (Optional) Label 2 worker node:
    kubectl label node istio-testing-worker node-role.kubernetes.io/worker=worker1
    kubectl label node istio-testing-worker2 node-role.kubernetes.io/worker=worker2
# Install istio:
    istioctl install --set profile=demo -y
# Inject label:
    kubectl label namespace default istio-injection=enabled
# Check for istio health:
    istioctl analyze
# Install prometheus:
    kubectl apply -f prometheus.yaml
# Install Kiali:
    kubectl apply -f kiali.yaml
    kubectl apply -f kiali-ingress.yaml
# Install Jeager:
    kubectl apply -f jaeger.yaml
# Install BookInfo:
    kubectl apply -l version!=v2,version!=v3 -f booking.yaml
# Install productpage gateway:
    kubectl apply -f booking-gateway.yaml
# Install Sleep (used to call other service):
    kubectl apply -f sleep.yaml
# Apply Destination rules:
    kubectl apply -f destination.yaml
# Apply request routing:
    kubectl apply -f traffic-control-v1.yaml
# Apply request routing to v2 (with jason username):
    kubectl apply -f traffic-control-v2.yaml

# Sleep gọi review: 
    kubectl exec $(kubectl get pod -l app=sleep -o jsonpath='{.items[0].metadata.name}') -- curl -sS http://reviews:9080/reviews/7
# Sleep gọi productpage:
    kubectl exec $(kubectl get pod -l app=sleep -o jsonpath='{.items[0].metadata.name}') -c sleep -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
