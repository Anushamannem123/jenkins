# jenkins

---
- hosts: webserver
  gather_facts: false
  become: true
  tasks:
  - name: install wget
    yum:
      name: wget
      state: present

  - name: install openjdk
    yum:
      name: java-1.8.0-openjdk
      state: present

  - name: download jenkins.repo
    get_url:
      url: http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
      dest: /etc/yum.repos.d/jenkins.repo

  - name: import jenkins key
    rpm_key:
      state: present
      key: https://jenkins-ci.org/redhat/jenkins-ci.org.key

  - name: install jenkins
    yum:
      name: jenkins
      state: present

  - name: start jenkins
    systemd:
      name: jenkins
      state: started

  - name: enable jenkins
    systemd:
      name: jenkins
      enabled: true

  - name: sleep for 30 seconds and continue with play
    wait_for: timeout=30
    delegate_to: 3.90.64.234

  - name: init password jenkin
    shell: cat /var/lib/jenkins/secrets/initialAdminPassword
    changed_when: false
    register: result
