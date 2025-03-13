# ingress-https-http-kubernetes
This projects describes how to setup ingress for http and https in a minikube cluster

<h1>We will be using NGINX as the base image for this task</h1>
Before proceeding, ensure minikube is up and running: mikikube start (for first time: mikikube start --vm-driver=hyperkit) Ensure hyperkit virtual machine is installed.
<br>
<h4>Enable ingress: minikube addons enable ingress <br> Confirm if it's running: <i>kubectl get pods -n ingress-nginx</i></h4>
<p>The below files contains the deployment, service and ingress:</p>
<h3>
  ![Screenshot 2025-03-13 at 13 18 12](https://github.com/user-attachments/assets/3a41559e-de24-4c37-a143-f6e33adda616)
</h3>

<h3>
  ![Screenshot 2025-03-13 at 13 18 12](https://github.com/user-attachments/assets/1a9cc3f9-e453-4941-890b-71cbe75b6e36)

</h3>

<h3>At this stage kindly ignor the tls section until the below is completed.</h3>
Run the scripts in the same folder using: kubectl apply -f ./ this will run all the files at the same time. 
Get the IP of the Ingress and save it to the hosts file: 
<p><i>echo "$(minikube ip) example.com" | sudo tee -a /etc/hosts</i></p>
Confirm this: <i>cat /etc/hosts | tail -n 1</i>
<h1>Now install TLS cert to the cluster:</h1>
<p>We will be using openssl for this task, use the below command:
<i>openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout tls.key -out tls.crt -subj "/CN=example.com" -days 365</i></p>
<p>Next: use this key in kubernetes: <i>kubectl create secret tls example-com-tls --cert=tls.crt --key=tls.key</i></p>
<p>Confirm this: kubectl get secret -o yaml.</p>
<p>Modify ingress file: spec:
  tls:
    -  secretName: example-com-tls
       hosts:
         - example.com
</p>
<p> Apply this yaml file again and confirm by running this in command line: cur; -k https://example.com</p>
<p>With all done properly you should be able to access nginx via ingree on both http and https protocol.</p>
