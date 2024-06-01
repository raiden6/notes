# Rancher Desktop

## Use Rancher Desktop because Docker Desktop requires a paid license for large companies.
  - https://docs.rancherdesktop.io/
- Rancher vs. Rancher Desktop
	- While Rancher and Rancher Desktop share the Rancher name they do different things. Rancher Desktop is not Rancher on the Desktop.
  - Rancher is a powerful solution to manage Kubernetes clusters.
  - Rancher Desktop provides a local Kubernetes and container management platform.
  - The two solutions complement each other. If you want to run Rancher on your local system, you can install Rancher into Rancher Desktop.
 
## Troubleshoot
- If run into this error:
  "Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?"
  - SOLUTION:
		
    - Run this command:
    
		  sudo ln -s "$HOME/.docker/run/docker.sock" /var/run/docker.sock
    - REF: https://github.com/docker/for-mac/issues/6531
