# **Setup a Minikube local K8s cluster running Argo Workflows orchestrator with Ansible**


This file installs minikube, kubectl, helm and k9s 
creates a stateless minikube cluster and adds a minio s3-like storage as a standalone container.  

To reach Kubernetes Dashboard and Argo Workflows UIs, port-forwards must be executed on separate terminal sessions after the install:
- Kubernetes Dashboaard: `minikube dashboard`
- Argo Workflows UI: `kubectl port-forward service/my-release-argo-workflows-server 2746:2746`


---
**Contents**
- [Setup playbook](#setup-playbook)
- [Destroy playbook](#destroy-playbook)
---

## **Setup playbook**

```yaml
---
- name: Setup Minikube, Argo Workflows, and MinIO
  hosts: localhost
  tasks:
    - name: Ensure Minikube is installed
      command: minikube version
      register: minikube_installed
      ignore_errors: yes

    - name: Install Minikube
      shell: curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
      when: minikube_installed.rc != 0

    - name: Start Minikube
      command: minikube start

    - name: Ensure kubectl is installed
      command: kubectl version --client
      register: kubectl_installed
      ignore_errors: yes

    - name: Install kubectl
      shell: curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl" && chmod +x kubectl && sudo mv kubectl /usr/local/bin/
      when: kubectl_installed.rc != 0

    - name: Ensure Helm is installed
      command: helm version
      register: helm_installed
      ignore_errors: yes

    - name: Install Helm
      shell: curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 && chmod 700 get_helm.sh && ./get_helm.sh
      when: helm_installed.rc != 0

    - name: Add Argo Helm repository
      command: helm repo add argo https://argoproj.github.io/argo-helm

    - name: Update Helm repositories
      command: helm repo update

    - name: Install Argo Workflows
      command: helm install my-release argo/argo-workflows --set server.authMode=""

    - name: Wait for Argo Workflows server to be ready
      command: kubectl wait --for=condition=available --timeout=600s deployment/my-release-argo-workflows-server

    - name: Create Argo namespace
      kubernetes.core.k8s:
        name: argo
        api_version: v1
        kind: Namespace
        state: present

    - name: Deploy example Argo Workflow
      kubernetes.core.k8s:
        state: present
        definition:
          apiVersion: argoproj.io/v1alpha1
          kind: Workflow
          metadata:
            generateName: example-workflow-
            namespace: argo
          spec:
            entrypoint: whalesay
            templates:
            - name: whalesay
              container:
                image: docker/whalesay:latest
                command: [cowsay]
                args: ["hello world"]

    - name: Forward Argo Workflows server port
      command: kubectl port-forward service/my-release-argo-workflows-server 2746:2746
      async: 60
      poll: 0

    - name: Ensure Docker is installed
      command: docker --version
      register: docker_installed
      ignore_errors: yes

    - name: Install Docker
      apt:
        name: docker.io
        state: present
      when: docker_installed.rc != 0

    - name: Create MinIO data directory
      file:
        path: "./share/minio/data"
        state: directory
        mode: '0755'

    - name: Create MinIO entrypoint script
      copy:
        dest: "./share/minio/minio-entrypoint.sh"
        content: |
          #!/bin/sh
          minio server /data --console-address ':9001' &
          sleep 3
          mc alias set myminio http://localhost:9000 ${MINIO_ROOT_USER} ${MINIO_ROOT_PASSWORD}
          mc mb myminio/${MINIO_BUCKET}
          mc admin user add myminio ${AWS_ACCESS_KEY_ID} ${AWS_SECRET_ACCESS_KEY}
          mc admin policy add myminio readwrite /etc/minio/policies/readwrite.json
          mc admin policy attach myminio readwrite --user ${AWS_ACCESS_KEY_ID}
          tail -f /dev/null
        mode: '0755'

    - name: Run MinIO container
      community.docker.docker_container:
        name: minio
        image: minio/minio
        state: started
        ports:
          - "9000:9000"
          - "8008:9001"
        volumes:
          - "./share/minio/data:/data"
          - "./share/minio/minio-entrypoint.sh:/minio_entrypoint.sh"
        env:
          MINIO_ROOT_USER: "minioadmin"
          MINIO_ROOT_PASSWORD: "minioadmin"
          MINIO_BUCKET: "mybucket"
          AWS_ACCESS_KEY_ID: "minioaccesskey"
          AWS_SECRET_ACCESS_KEY: "miniosecretkey"
        entrypoint: /minio_entrypoint.sh

    - name: Create Kubernetes secret for MinIO credentials
      kubernetes.core.k8s:
        state: present
        definition:
          apiVersion: v1
          kind: Secret
          metadata:
            name: minio-secrets
            namespace: default
          data:
            accesskey: "{{ 'minioaccesskey' | b64encode }}"
            secretkey: "{{ 'miniosecretkey' | b64encode }}"

    - name: Deploy example Argo Workflow
      kubernetes.core.k8s:
        state: present
        definition:
          apiVersion: argoproj.io/v1alpha1
          kind: Workflow
          metadata:
            generateName: example-workflow-
            namespace: argo
          spec:
            entrypoint: whalesay
            templates:
            - name: whalesay
              container:
                image: docker/whalesay:latest
                command: [cowsay]
                args: ["hello world"]


    - name: Install k9s
      shell: curl -sS https://webinstall.dev/k9s | bash
      when: ansible_os_family == "Linux"

    # - name: Install k9s on macOS
    #   homebrew:
    #     name: k9s
    #     state: present
    #   when: ansible_os_family == "Darwin"

    # - name: Install k9s on Windows
    #   win_chocolatey:
    #     name: k9s
    #     state: present
    #   when: ansible_os_family == "Windows"

    # - name: Start Minikube Dashboard
    #   command: minikube dashboard --url
    #   register: dashboard_url
    #   async: 60
    #   poll: 0


    # - name: Print Minikube Dashboard URL
    #   debug:
    #     msg: "Minikube Dashboard URL: {{ dashboard_url.stdout }}"
    #   when: dashboard_url is defined

    # - name: Forward Argo Workflows UI port
    #   command: kubectl port-forward service/my-release-argo-workflows-server 2746:2746
    #   async: 60
    #   poll: 0

```




## **Destroy playbook**

Uncomment the last part of the playbook for a full minikube / kubectl uninstall

```yaml
---
- name: Destroy Minikube, Argo Workflows, and MinIO
  hosts: localhost
  tasks:
    - name: Check if Argo Workflows is installed
      command: helm status my-release
      register: argo_status
      ignore_errors: yes

    - name: Uninstall Argo Workflows
      command: helm uninstall my-release
      when: argo_status.rc == 0

    - name: Stop Minikube
      command: minikube stop
      ignore_errors: yes


    - name: Delete Minikube
      command: minikube delete

    - name: Remove MinIO data directory
      file:
        path: /opt/minio
        state: absent
      become: yes

    - name: Remove MinIO container
      community.docker.docker_container:
        name: minio
        state: absent


    # - name: Remove Minikube
    #   file:
    #     path: /usr/local/bin/minikube
    #     state: absent
    #   become: yes

    # - name: Remove kubectl
    #   file:
    #     path: /usr/local/bin/kubectl
    #     state: absent
    #   become: yes

    # - name: Remove Helm
    #   file:
    #     path: /usr/local/bin/helm
    #     state: absent
    #   become: yes

```