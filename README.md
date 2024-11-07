# terraform-windows-provisioner
Terraform-based automation for Windows BYOH (Bring Your Own Host) node provisioning in OpenShift clusters.

## Overview
This tool automates the provisioning and management of Windows BYOH worker nodes across multiple platforms using Terraform. It enables seamless integration of Windows nodes into OpenShift clusters with automated configuration and cluster joining.

## Prerequisites
- OpenShift Cluster with exported KUBECONFIG
- Terraform ≥ 1.0.0
- `oc` CLI tool
- `jq` for JSON processing
- Windows Machine Config Operator (WMCO) installed

## Supported Platforms
- **AWS**: EC2 instances with automated credential management
- **Azure**: VM provisioning with native cloud integration
- **GCP**: Google Cloud Platform instances
- **vSphere**: VMware infrastructure deployment
- **Nutanix**: Prism Central managed infrastructure
- **Baremetal**: UPI-based deployment for non-cloud environments

## Usage
The tool uses `byoh.sh` script for all operations:

### Create BYOH nodes:
```bash
./byoh.sh apply <name> <number-of-workers> [suffix] [windows-version]
```

### Examples:
```bash
# Create 2 BYOH instances with Windows Server 2019
./byoh.sh apply byoh 2 '' 2019

# Create 4 instances with custom name
./byoh.sh apply my-byoh 4

# Create on Nutanix
./byoh.sh apply ntnx-byoh 2

# Destroy instances
./byoh.sh destroy my-byoh 4
```

### Parameters
| Parameter | Description | Default | Required |
|-----------|-------------|---------|----------|
| ACTION | Operation (apply/destroy/arguments/configmap/clean) | apply | Yes |
| NAME | Base name for BYOH instances | byoh-winc | No |
| NUM_WORKERS | Number of BYOH workers | 2 | No |
| FOLDER_SUFFIX | Temporary folder suffix for multiple runs | "" | No |
| WINDOWS_VERSION | Windows Server version (2019/2022) | 2022 | No |

## Platform Requirements

### AWS
- AWS credentials in `$HOME/.aws/config` or `$HOME/.aws/credentials`
- Proper IAM permissions for EC2 and networking

### Azure
- Valid subscription and service principal
- Required resource provider permissions

### GCP
- Service account with compute permissions
- Proper network access configuration

### Nutanix
- Prism Central access configured
- Pre-configured Windows image
- Subnet with DHCP enabled

### vSphere
- vCenter access credentials
- Network and datastore permissions
- Template VM access

### Baremetal
- Network accessibility
- IPMI or BMC access
- OS installation media

## Integration with OpenShift

### BYOH Node Labels
Nodes are automatically labeled:
```yaml
kubernetes.io/os: windows
windowsmachineconfig.openshift.io/byoh: true
```

### Verification
Check BYOH node status:
```bash
# List BYOH Windows nodes
oc get nodes -l windowsmachineconfig.openshift.io/byoh=true

# Check node readiness
oc wait nodes -l kubernetes.io/os=windows --for condition=Ready=True
```

## Troubleshooting

### Common Checks
```bash
# Check WMCO status
oc get deployment -n openshift-windows-machine-config-operator

# Verify node status
oc get nodes -l kubernetes.io/os=windows

# Check cluster capacity
oc describe node -l windowsmachineconfig.openshift.io/byoh=true
```

## Contributing
- Fork the repository
- Create a feature branch
- Submit pull request
- Add tests for new features

## License
Apache License 2.0
