### Question - Trivy

A number of pods in the "spectacle" namespace have been created. Using the trivy tools, identify and delete the pods with the least number of CRITICAL vulnerabilities.

#### 1 - get all images of pods running in the spectacle namepsace

```sh

kubectl -n spectacle get pods -o yaml | grep -E "image"

```

#### 2 - scan each image using "trivy image NAME"

```sh

trivy image --severity CRITICAL nginx:1.16

If the image has any CRITICAL vulnerabilities, delete the pod associated with that image.

```

#### 3 - Delete the pods

```sh

kubectl -n spectacle delete pod <PODNAME>

Do the above procedure as many times as required to delete the pod 

```
