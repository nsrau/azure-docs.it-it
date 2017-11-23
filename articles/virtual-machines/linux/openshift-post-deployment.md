---
title: "OpenShift in attività di post-distribuzione di Azure | Microsoft Docs"
description: "Attività aggiuntive dopo che è stato distribuito un cluster OpenShift."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 77c4719b5cee7f5736d73ee10cf6abf12229ea11
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="post-deployment-tasks"></a>Attività di post-distribuzione

Dopo aver distribuito un cluster OpenShift, è possibile configurare elementi aggiuntivi. Questo articolo illustra le attività seguenti:

- Come configurare l'accesso Single Sign-On usando Azure Active Directory (Azure AD)
- Come configurare Operations Management Suite per monitorare OpenShift
- Come configurare le metriche e la registrazione

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurare Single Sign-On usando Azure Active Directory

Per usare Azure Active Directory per l'autenticazione, è necessario creare prima di tutto la registrazione di un'app Azure AD. Questo processo include due passaggi: la creazione della registrazione dell'app e la configurazione delle autorizzazioni.

### <a name="create-an-app-registration"></a>Creare la registrazione di un'app

Si userà l'interfaccia della riga di comando di Azure per creare la registrazione dell'app e l'interfaccia utente grafica (portale) per impostare le autorizzazioni. Per creare la registrazione dell'app, saranno necessarie le cinque informazioni seguenti:

- Nome visualizzato: nome di registrazione dell'app, ad esempio OCPAzureAD
- Home page: URL della console OpenShift, ad esempio https://masterdns343khhde.westus.cloudapp.azure.com:8443/console
- URI identificatore: URL della console OpenShift, ad esempio https://masterdns343khhde.westus.cloudapp.azure.com:8443/console
- URL di risposta: URL pubblico master e nome di registrazione dell'app, ad esempio https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD
- Password: password sicura. Usare una password complessa

L'esempio seguente crea una registrazione dell'app usando le informazioni indicate sopra:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Se il comando riesce, viene restituito un output JSON simile al seguente:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

Prendere nota della proprietà appId restituita dal comando per un passaggio successivo.

Nel portale di Azure:

1.  Passare ad **Azure Active Directory** > **Registrazioni per l'app**.
2.  Cercare la registrazione dell'app, ad esempio OCPAzureAD.
3.  Nei risultati fare clic sulla registrazione dell'app.
4.  Nel pannello **Impostazioni** selezionare **Autorizzazioni necessarie**.
5.  In **Autorizzazioni necessarie** selezionare **Aggiungi**.

  ![Registrazione delle app](media/openshift-post-deployment/app-registration.png)

6.  Fare clic su Passaggio 1: Seleziona API, su **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Fare clic su **Seleziona** nella parte inferiore.

  ![Registrazione dell'app, Seleziona API](media/openshift-post-deployment/app-registration-select-api.png)

7.  In Passaggio 2: Selezionare le autorizzazioni selezionare **Accedi e leggi il profilo di un altro utente** in **Autorizzazioni delegate** e quindi fare clic su **Seleziona**.

  ![Accesso alla registrazione dell'app](media/openshift-post-deployment/app-registration-access.png)

8.  Selezionare **Operazione completata**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurare OpenShift per l'autenticazione di Azure AD

Per configurare OpenShift per l'uso di Azure AD come provider di autenticazione, il file /etc/origin/master/master-config.yaml deve essere modificato in tutti i nodi master.

È possibile trovare l'ID tenant usando il comando dell'interfaccia della riga di comando seguente:

```azurecli
az account show
```

Nel file YAML individuare le righe seguenti:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Inserire le righe seguenti immediatamente dopo le righe indicate sopra:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

È possibile trovare l'ID tenant usando il comando dell'interfaccia della riga di comando seguente: ```az account show```

Riavviare i servizi master OpenShift in tutti i nodi master:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift Container Platform (OCP) con più master**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform con singolo master**

```bash
sudo systemctl restart atomic-openshift-master
```

Nella console OpenShift verranno visualizzate due opzioni per l'autenticazione, htpasswd_auth e [Registrazione app].

## <a name="monitor-openshift-with-operations-management-suite"></a>Monitorare OpenShift con Operations Management Suite

Il monitoraggio di OpenShift con Operations Management Suite può essere eseguito usando due opzioni diverse: l'installazione dell'agente OMS nell'host della macchina virtuale o il contenitore OMS. Questo articolo contiene le istruzioni per la distribuzione del contenitore OMS.

## <a name="create-an-openshift-project-for-operations-management-suite-and-set-user-access"></a>Creare un progetto OpenShift per Operations Management Suite e impostare l'accesso utente

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Creare un file YAML dell'oggetto DaemonSet

Creare un file denominato ocp-omsagent.yml:

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-a-secret-yaml-file"></a>Creare un file YAML del segreto

Per creare il file YAML del segreto, sono necessarie due informazioni: l'ID area di lavoro OMS e la chiave condivisa dell'area di lavoro OMS. 

File ocp-secret.yml di esempio: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Sostituire wsid_data with con l'ID area di lavoro OMS con codifica Base64. Quindi sostituire key_data con la chiave condivisa dell'area di lavoro OMS con codifica Base64.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Creare il segreto e l'oggetto DaemonSet

Distribuire il file del segreto:

```bash
oc create -f ocp-secret.yml
```

Distribuire l'oggetto DaemonSet dell'agente OMS:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Configurare le metriche e la registrazione

Il modello di Azure Resource Manager per OpenShift Container Platform fornisce i parametri di input per l'abilitazione delle metriche e della registrazione, mentre l'offerta OpenShift Container Platform del Marketplace e il modello OpenShift Origin di Resource Manager no.

Se è stato usato il modello OCP di Resource Manager e le metriche e la registrazione non sono state abilitate al momento dell'installazione oppure è stata usata l'offerta OCP del Marketplace, le metriche e la registrazione possono essere abilitate facilmente al termine dell'attività. Se si usa il modello OpenShift Origin di Resource Manager, sono necessarie alcune operazioni preliminari.

### <a name="openshift-origin-template-pre-work"></a>Operazioni preliminari per il modello OpenShift Origin

1. SSH nel primo nodo Master usando la porta 2200.

   Esempio:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Modificare il file /etc/ansible/hosts file e aggiungere le righe seguenti dopo la sezione del provider di identità (# Enable HTPasswdPasswordIdentityProvider):

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

3. Sostituire $ROUTING con la stringa usata per l'opzione openshift_master_default_subdomain nello stesso file /etc/ansible/hosts.

### <a name="azure-cloud-provider-in-use"></a>Provider di servizi cloud di Azure in uso

Nel primo nodo master (origine) o nodo Bastion (OCP) eseguire SSH usando le credenziali specificate durante la distribuzione. Eseguire il comando seguente:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Provider di servizi cloud di Azure non in uso

Nel primo nodo master (origine) o nodo Bastion (OCP) eseguire SSH usando le credenziali specificate durante la distribuzione. Eseguire il comando seguente:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Passaggi successivi

- [Getting started with OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html) (Introduzione a OpenShift Container Platform)
- [Introduzione a OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
