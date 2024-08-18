# Vagrantfile for Jenkins UI, Jenkins Agent, SonarQube, Kubernetes, and ArgoCD
Vagrant.configure("2") do |config|
  # Configuration for Jenkins UI VM
  config.vm.define "jenkins-ui" do |jenkins_ui|
    jenkins_ui.vm.box = "ubuntu/focal64"
    jenkins_ui.vm.network "private_network", ip: "192.168.33.10"
    jenkins_ui.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
  end

  # Configuration for Jenkins Agent VM
  config.vm.define "jenkins-agent" do |jenkins_agent|
    jenkins_agent.vm.box = "ubuntu/focal64"
    jenkins_agent.vm.network "private_network", ip: "192.168.33.11"
    jenkins_agent.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
    end
  end

  # Configuration for SonarQube VM
  config.vm.define "son" do |son|
    son.vm.box = "ubuntu/focal64"
    son.vm.network "private_network", ip: "192.168.33.16"
    son.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
  end  

  # Configuration for Kubernetes VM
  config.vm.define "k8s" do |k8s|
    k8s.vm.box = "ubuntu/focal64"
    k8s.vm.network "private_network", ip: "192.168.33.13"
    k8s.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
  end

  # Configuration for ArgoCD VM
  config.vm.define "arg" do |arg|
    arg.vm.box = "ubuntu/focal64"
    arg.vm.network "private_network", ip: "192.168.33.15"
    arg.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
  end
end
