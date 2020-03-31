---
title: Riferimento YAML per il gruppo di contenitoriYAML reference for container group
description: Riferimento per il file YAML supportato dalle istanze del contenitore di Azure per configurare un gruppo di contenitoriReference for the YAML file supported by Azure Container Instances to configure a container group
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896560"
---
# <a name="yaml-reference-azure-container-instances"></a>Riferimento YAML: Istanze del contenitore di AzureYAML reference: Azure Container Instances

In questo articolo vengono illustrati la sintassi e le proprietà per il file YAML supportato dalle istanze del contenitore di Azure per configurare un gruppo di [contenitori.](container-instances-container-groups.md) Usare un file YAML per immettere la configurazione del gruppo nel comando az container create nell'interfaccia della riga di comando di Azure.Use a YAML file to input the group configuration to the [az container create][az-container-create] command in the Azure CLI. 

Un file YAML è un modo pratico per configurare un gruppo di contenitori per le distribuzioni riproducibili. È un'alternativa concisa all'uso di un modello di [Resource Manager](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) o degli SDK delle istanze del contenitore di Azure per creare o aggiornare un gruppo di contenitori.

> [!NOTE]
> Questo riferimento si applica ai file YAML per la versione dell'API REST delle istanze del contenitore di `2018-10-01`Azure.

## <a name="schema"></a>SCHEMA 

Di seguito è riportato lo schema per il file YAML, inclusi i commenti per evidenziare le proprietà chiave. Per una descrizione delle proprietà in questo schema, vedere la sezione [Valori delle proprietà.](#property-values)

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Valori delle proprietà

Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups oggetto

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Nome del gruppo di contenitori. |
|  apiVersion | enum | Sì | 2018-10-01 |
|  posizione | string | No | Percorso della risorsa. |
|  tags | object | No | Tag di risorsa. |
|  identity | object | No | Identità del gruppo di contenitori, se configurato. - [Oggetto ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | object | Sì | [Oggetto ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Oggetto ContainerGroupIdentity

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  type | enum | No | Tipo di identità utilizzata per il gruppo di contenitori. Il tipo 'SystemAssigned, UserAssigned' include sia un'identità creata in modo implicito che un set di identità assegnate dall'utente. Il tipo 'None' rimuoverà tutte le identità dal gruppo di contenitori. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | No | Elenco di identità utente associate al gruppo di contenitori. I riferimenti alla chiave del dizionario dell'identità utente saranno ID di risorsa di Azure Resource Manager nel formato: '/subscriptions/'subscriptionId'/resourceGroups/'resourceGroupName'/providers/Microsoft.ManagedIdentity/userAssignedIdentities/''identityName''. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Oggetto ContainerGroupProperties

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  containers | array | Sì | Contenitori all'interno del gruppo di contenitori. - [Oggetto Container](#Container) |
|  imageRegistryCredentials | array | No | Credenziali del Registro di sistema dell'immagine da cui viene creato il gruppo di contenitori. - [Oggetto ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy (criterio di riavvio) | enum | No | Criteri di riavvio per tutti i contenitori all'interno del gruppo di contenitori. - `Always`Riavvia sempre: `OnFailure` riavvia `Never` in caso di errore- Non riavviare mai. - Sempre, OnFailure, Mai |
|  IpAddress | object | No | Tipo di indirizzo IP del gruppo di contenitori. - [Oggetto IpAddress](#IpAddress) |
|  osType | enum | Sì | Tipo di sistema operativo richiesto dai contenitori nel gruppo di contenitori. - Windows o Linux |
|  volumes | array | No | Elenco di volumi che possono essere montati da contenitori in questo gruppo di contenitori. - [Oggetto Volume](#Volume) |
|  diagnostica | object | No | Informazioni di diagnostica per un gruppo di contenitori. - [Oggetto ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | object | No | Informazioni sul profilo di rete per un gruppo di contenitori. - [Oggetto ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | No | Informazioni di configurazione DNS per un gruppo di contenitori. - [Oggetto DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Oggetto Container

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Nome fornito dall'utente dell'istanza del contenitore. |
|  properties | object | Sì | Proprietà dell'istanza del contenitore. - [Oggetto ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Oggetto ImageRegistryCredential

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  server | string | Sì | Il server del Registro di sistema dell'immagine Docker senza un protocollo, ad esempio "http" e "https". |
|  username | string | Sì | Nome utente per il registro privato. |
|  password | string | No | Password per il registro privato. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Oggetto IpAddress

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  ports | array | Sì | Elenco delle porte esposte nel gruppo di contenitori. - [Oggetto Port](#Port) |
|  type | enum | Sì | Specifica se l'IP è esposto a Internet pubblico o a una rete virtuale privata. - Pubblico o privato |
|  Ip | string | No | L'IP esposto all'internet pubblico. |
|  dnsNameLabel (etichettanome) | string | No | Etichetta nome Dns per l'IP. |


<a id="Volume" />

### <a name="volume-object"></a>Oggetto Volume

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Il nome del volume. |
|  azureFile | object | No | Volume di File di Azure.The Azure File volume. - [Oggetto AzureFileVolume](#AzureFileVolume) |
|  emptyDir | object | No | Volume di directory vuoto. |
|  secret | object | No | Il volume segreto. |
|  gitRepo | object | No | Volume del repository git. - [Oggetto GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Oggetto ContainerGroupDiagnostics

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  logAnalytics (informazioni in netto modulo) | object | No | Informazioni di analisi del log del gruppo di contenitori. - [Oggetto LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Oggetto ContainerGroupNetworkProfile

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  id | string | Sì | Identificatore per un profilo di rete. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Oggetto DnsConfiguration

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  Nameserver | array | Sì | Server DNS per il gruppo di contenitori. - stringa |
|  ricercaDomini | string | No | Domini di ricerca DNS per la ricerca del nome host nel gruppo di contenitori. |
|  opzioni | string | No | Opzioni DNS per il gruppo di contenitori. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Oggetto ContainerProperties

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  image | string | Sì | Nome dell'immagine utilizzata per creare l'istanza del contenitore. |
|  command | array | No | Comandi da eseguire all'interno dell'istanza del contenitore in formato exec. - stringa |
|  ports | array | No | Porte esposte nell'istanza del contenitore. - [Oggetto ContainerPort](#ContainerPort) |
|  environmentVariables | array | No | Variabili di ambiente da impostare nell'istanza del contenitore. - [Oggetto EnvironmentVariable](#EnvironmentVariable) |
|  resources | object | Sì | Requisiti delle risorse dell'istanza del contenitore. - [Oggetto ResourceRequirements](#ResourceRequirements) |
|  volumeMontaggii | array | No | Il volume viene montato all'istanza del contenitore. - [Oggetto VolumeMount](#VolumeMount) |
|  livenessProbe | object | No | La sonda di liveness. - [Oggetto ContainerProbe](#ContainerProbe) |
|  readinessProbe | object | No | La sonda di prontezza. - [Oggetto ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Oggetto Port

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | Protocollo associato alla porta. - TCP o UDP |
|  port | integer | Sì | Il numero della porta. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Oggetto AzureFileVolume

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  Nomecondivisione | string | Sì | Nome della condivisione file di Azure da montare come volume. |
|  readOnly | boolean | No | Flag che indica se il file di Azure condiviso montato come volume è di sola lettura. |
|  storageAccountName | string | Sì | Nome dell'account di archiviazione che contiene la condivisione File di Azure.The name of the storage account that contains the Azure File share. |
|  storageAccountKey | string | No | Chiave di accesso all'account di archiviazione usata per accedere alla condivisione file di Azure.The storage account access key used to access the Azure File share. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Oggetto GitRepoVolume

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  directory | string | No | Nome della directory di destinazione. Non deve contenere o iniziare con '..'.  Se viene fornito '.', la directory del volume sarà il repository git.  In caso contrario, se specificato, il volume conterrà il repository git nella sottodirectory con il nome specificato. |
|  repository | string | Sì | Repository URL |
|  revision | string | No | Hash di commit per la revisione specificata. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Oggetto LogAnalytics

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Sì | ID dell'area di lavoro per l'analisi dei log |
|  workspaceKey | string | Sì | Chiave dell'area di lavoro per l'analisi dei logThe workspace key for log analytics |
|  logType (tipo log) | enum | No | Tipo di log da utilizzare. - ContainerInsights o ContainerInstanceLogs |
|  metadata | object | No | Metadati per l'analisi dei log. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Oggetto ContainerPort

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | Protocollo associato alla porta. - TCP o UDP |
|  port | integer | Sì | Numero di porta esposto all'interno del gruppo di contenitori. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Oggetto EnvironmentVariable

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Nome della variabile di ambiente. |
|  value | string | No | Valore della variabile di ambiente. |
|  secureValue (valore sicuro) | string | No | Valore della variabile di ambiente protetta. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Oggetto ResourceRequirements

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  requests | object | Sì | Richieste di risorse di questa istanza del contenitore. - [Oggetto ResourceRequests](#ResourceRequests) |
|  limiti | object | No | Limiti delle risorse di questa istanza del contenitore. - [Oggetto ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Oggetto VolumeMount

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  name | string | Sì | Nome del montaggio del volume. |
|  mountPath (percorso di montaggio) | string | Sì | Percorso all'interno del contenitore in cui deve essere montato il volume. Non devono contenere i due punti (:). |
|  readOnly | boolean | No | Flag che indica se il montaggio del volume è di sola lettura. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Oggetto ContainerProbe

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  exec | object | No | Il comando di esecuzione da sondare - [oggetto ContainerExecThe](#ContainerExec) execution command to probe - ContainerExec object |
|  httpGet (informazioni in base al sito | object | No | Le impostazioni Http Get da sondare - [Oggetto ContainerHttpGetThe](#ContainerHttpGet) Http Get settings to probe - ContainerHttpGet object |
|  initialDelaySeconds | integer | No | Secondi di ritardo iniziali. |
|  periodSecondi | integer | No | Periodo secondi. |
|  failureThreshold | integer | No | Soglia di errore. |
|  successoSoglia | integer | No | La soglia di successo. |
|  timeoutSecondi | integer | No | Secondi di timeout. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Oggetto ResourceRequests

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  memoryInGB (memoriaInGB) | d'acquisto | Sì | Richiesta di memoria in GB di questa istanza del contenitore. |
|  cpu | d'acquisto | Sì | Richiesta della CPU di questa istanza del contenitore. |
|  Gpu | object | No | Richiesta GPU di questa istanza del contenitore. - [Oggetto GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Oggetto ResourceLimits

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  memoryInGB (memoriaInGB) | d'acquisto | No | Limite di memoria in GB di questa istanza del contenitore. |
|  cpu | d'acquisto | No | Limite della CPU di questa istanza del contenitore. |
|  Gpu | object | No | Limite GPU di questa istanza del contenitore. - [Oggetto GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Oggetto ContainerExec

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  command | array | No | Comandi da eseguire all'interno del contenitore. - stringa |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Oggetto ContainerHttpGet

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  path | string | No | Percorso da sondare. |
|  port | integer | Sì | Numero di porta da sondare. |
|  scheme | enum | No | Schema. - http o https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Oggetto GpuResource

|  Nome | Type | Obbligatoria | valore |
|  ---- | ---- | ---- | ---- |
|  count | integer | Sì | Conteggio della risorsa GPU. |
|  sku | enum | Sì | SKU della risorsa GPU. - K80, P100, V100 |


## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione [Distribuire un gruppo con più contenitori utilizzando un file YAML](container-instances-multi-container-yaml.md).

Vedere esempi di utilizzo di un file YAML per distribuire gruppi di contenitori in una [rete virtuale](container-instances-vnet.md) o che [montano un volume esterno.](container-instances-volume-azure-files.md)

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

