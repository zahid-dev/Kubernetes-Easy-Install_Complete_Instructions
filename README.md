<!DOCTYPE html><html><head><meta charset="utf-8"></head><body id="preview">
<h2 class="code-line" data-line-start=0 data-line-end=1 ><a id="Kubernetes_Easy_Bug_Free_Setup_for_Ubuntu_1604_and_Ubuntu_1804_0"></a>Kubernetes Easy, Bug Free Setup for Ubuntu 16.04 and Ubuntu 18.04</h2>
<p class="has-line-data" data-line-start="1" data-line-end="2">Installing kubernetes can be a real pain and there are no tutorials that cover all the instruction needed to complete the successful setup of kubernetes, atleast not in a single place. This guide attempts to mitigate this problem.</p>
<p class="has-line-data" data-line-start="3" data-line-end="4">This Guide covers.</p>
<ul>
<li class="has-line-data" data-line-start="5" data-line-end="6">Installation of Docker</li>
<li class="has-line-data" data-line-start="6" data-line-end="7">Installation of kubernetes</li>
<li class="has-line-data" data-line-start="7" data-line-end="8">Setting up kubernetes as single node</li>
<li class="has-line-data" data-line-start="8" data-line-end="10">Deployment of kubernetes nginx service</li>
</ul>
<p class="has-line-data" data-line-start="10" data-line-end="11">It is recommended to use an Ubuntu Server VM(Virtual Machine) for the installation and deployment of kubernetes as it will remove all unnecessary complications and give you better control and learning.</p>
<h3 class="code-line" data-line-start=12 data-line-end=13 ><a id="Installation_of_Docker_12"></a>Installation of Docker</h3>
<p class="has-line-data" data-line-start="14" data-line-end="15">Before setting up docker, we need to figure out whick version of docker is compatible with kubernetes. Usually kubernetes run smoothly with the latest stable version of docker but it is recommended to use the version of docker that is verified by kubernetes. Any incompatible version might trigger hard crashing of pods.</p>
<p class="has-line-data" data-line-start="16" data-line-end="17">To find out verified versions of docker for specific version of kubernetes, goto the release notes of kubernetes for that version.</p>
<pre><code>https://kubernetes.io/docs/setup/release/notes/ 
</code></pre>
<p class="has-line-data" data-line-start="20" data-line-end="21">Press Ctrl+F to and find “list of validated docker versions”, beneath are the version that are successfully tested.</p>
<p class="has-line-data" data-line-start="22" data-line-end="23">Now to install that version of docker, follow the below steps:</p>
<p class="has-line-data" data-line-start="24" data-line-end="25">-&gt; Note that the installation guide is taken from the official documentation.</p>
<p class="has-line-data" data-line-start="26" data-line-end="27">First remove any old version of docker installed</p>
<pre><code>sudo apt-get remove docker docker-engine docker.io containerd runc 
</code></pre>
<p class="has-line-data" data-line-start="30" data-line-end="31">Then update the apt package index</p>
<pre><code>sudo apt-get update
</code></pre>
<p class="has-line-data" data-line-start="36" data-line-end="37">Install packages to allow apt to use a repository over HTTPS:</p>
<pre><code class="has-line-data" data-line-start="39" data-line-end="46" class="language-sh">$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
</code></pre>
<p class="has-line-data" data-line-start="47" data-line-end="48">Add Docker’s official GPG key:</p>
<pre><code>$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
</code></pre>
<p class="has-line-data" data-line-start="51" data-line-end="52">Use the following command to add the stable repository:</p>
<pre><code class="has-line-data" data-line-start="54" data-line-end="59" class="language-sh">sudo add-apt-repository \
   <span class="hljs-string">"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   <span class="hljs-variable">$(lsb_release -cs)</span> \
   stable"</span>
</code></pre>
<p class="has-line-data" data-line-start="60" data-line-end="61">Once again update the apt package index to contain the repository:</p>
<pre><code>sudo apt-get update
</code></pre>
<p class="has-line-data" data-line-start="64" data-line-end="65">Now run the following command to list the available stable versions:</p>
<pre><code>apt-cache madison docker-ce
</code></pre>
<p class="has-line-data" data-line-start="68" data-line-end="69">Note down the version from kubernetes release notes and match it with the available version from apt-cache, the plug into the version string:</p>
<pre><code>sudo apt-get install docker-ce=&lt;VERSION_STRING&gt; docker-ce-cli=&lt;VERSION_STRING&gt; containerd.io
</code></pre>
<p class="has-line-data" data-line-start="72" data-line-end="73">This will install the compatible version of docker on your system, run the following command to check the installation:</p>
<pre><code>sudo docker run hello-world
</code></pre>
<p class="has-line-data" data-line-start="76" data-line-end="77">If everything is working correctly, move on to the next step. Otherwise raise an issue.</p>
<h3 class="code-line" data-line-start=79 data-line-end=80 ><a id="Installation_of_Kubernetes_79"></a>Installation of Kubernetes</h3>
<p class="has-line-data" data-line-start="81" data-line-end="82">Run following commands for installing kubernetes software:</p>
<pre><code class="has-line-data" data-line-start="84" data-line-end="93" class="language-sh">sudo apt-get update &amp;&amp; sudo apt-get install -y apt-transport-https curl
curl <span class="hljs-operator">-s</span> https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat &lt;&lt;EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
</code></pre>
<p class="has-line-data" data-line-start="94" data-line-end="95">This will install all the utilities required to run kubernetes cluster. If a problem appears, refer to kubernetes trouble shooting section of the docs, or raise an issue in the repository.</p>
<h3 class="code-line" data-line-start=97 data-line-end=98 ><a id="Setting_up_kubernetes_as_single_node_97"></a>Setting up kubernetes as single node</h3>
<p class="has-line-data" data-line-start="99" data-line-end="100">To initialize kubernetes cluster, run the following command.</p>
<pre><code>sudo kubeadm init --pod-network-cidr=192.168.0.0/16
</code></pre>
<p class="has-line-data" data-line-start="103" data-line-end="104">If this command returns an error message like ports not available, run kubeadm reset and then again run kubeadm init.</p>
<p class="has-line-data" data-line-start="105" data-line-end="106">Pod network cidr should be the above address for the cluster to work. The reason why is mentioned below.</p>
<p class="has-line-data" data-line-start="107" data-line-end="108">A successful instance of kubeadm init will output a message, run following commands to be able to use kubectl(kubernetes command line tool).</p>
<pre><code class="has-line-data" data-line-start="110" data-line-end="114" class="language-sh">mkdir -p <span class="hljs-variable">$HOME</span>/.kube
sudo cp -i /etc/kubernetes/admin.conf <span class="hljs-variable">$HOME</span>/.kube/config
sudo chown $(id -u):$(id -g) <span class="hljs-variable">$HOME</span>/.kube/config
</code></pre>
<p class="has-line-data" data-line-start="115" data-line-end="116">Now we have to install a pod network plugin. There are wide variety of plugins available but we will make use of “calico” plugin as it is popular and bug free. The above pod network cidr is compatible with calico that is why the address was use.</p>
<p class="has-line-data" data-line-start="117" data-line-end="118">Run the following to apply the calico plugin to the pods.</p>
<pre><code>kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/calico.yaml
</code></pre>
<p class="has-line-data" data-line-start="121" data-line-end="122">-&gt;Note that the url is subject to change on new release. Kindly refer to this url to get the updated version of pod network</p>
<pre><code>https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
</code></pre>
<p class="has-line-data" data-line-start="125" data-line-end="126">Now to configure the cluster as a single node, we have to untaint the node, run the following command.</p>
<pre><code>sudo kubectl describe nodes
</code></pre>
<p class="has-line-data" data-line-start="129" data-line-end="130">On running logs, goto section called taints and note them down. After that run the following.</p>
<pre><code>kubectl taint nodes --all &lt;name of taint you noted down&gt;-
</code></pre>
<p class="has-line-data" data-line-start="133" data-line-end="134">Dont forget to write the minus sign in the end to successfully remove the taint.</p>
<p class="has-line-data" data-line-start="135" data-line-end="136">After this step, run “kubectl get nodes” and you will see the node is in ready state. Now run command “kubectl get pods -n --all-namespaces kube-system -o wide”, this will list down all the pods and will be in running state.</p>
<p class="has-line-data" data-line-start="137" data-line-end="138">This completes the setup of kubernetes cluster.</p>
<h2 class="code-line" data-line-start=141 data-line-end=143 ><a id="License_141"></a>License</h2>
<p class="has-line-data" data-line-start="144" data-line-end="145">MIT</p>

</body></html>