# MetalLB

## K8S ConfigMap manifest

### Layer 2 Configuration

The following configuration gives MetalLB control over IPs from 192.168.1.240 to 192.168.1.250, and configures Layer 2 mode

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: config
data:
  config: |
    address-pools:
      - name: default
        protocol: layer2
        addresses:
          - 192.168.1.240-192.168.1.250
```