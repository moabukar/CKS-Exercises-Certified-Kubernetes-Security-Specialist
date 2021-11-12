#### Kubernetes GitHub Repository:

  https://github.com/kubernetes/kubernetes/releases

#### Step 1 - Binary download:
  ```sh
  wget https://github.com/kubernetes/kubernetes/releases/download/v1.22.3/kubernetes.tar.gz
  ```

  #### Step 2 - Verify the binary:
  ```sh

  apt install hashalot
  sha256 kubernetes.tar.gz
  sha512sum kubernetes.tar.gz
  ```