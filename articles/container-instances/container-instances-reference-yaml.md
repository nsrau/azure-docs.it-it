---
title: Riferimento YAML per il gruppo di contenitori
description: Informazioni di riferimento per il file YAML supportato da istanze di contenitore di Azure per configurare un gruppo di contenitori
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896560"
---
# <a name="yaml-reference-azure-container-instances"></a>Guida di riferimento a YAML: istanze di contenitore di Azure

Questo articolo illustra la sintassi e le proprietà del file YAML supportato dalle istanze di contenitore di Azure per configurare un [gruppo di contenitori](container-instances-container-groups.md). Usare un file YAML per inserire la configurazione di gruppo nel comando [AZ container create][az-container-create] nell'interfaccia della riga di comando di Azure. 

Un file YAML è un modo pratico per configurare un gruppo di contenitori per le distribuzioni riproducibili. Si tratta di un'alternativa concisa all'uso di un [modello di gestione risorse](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) o degli SDK di istanze di contenitore di Azure per creare o aggiornare un gruppo di contenitori.

> [!NOTE]
> Questo riferimento si applica ai file YAML per la versione dell'API REST di istanze di contenitore di Azure `2018-10-01`.

## <a name="schema"></a>SCHEMA 

Lo schema per il file YAML segue, inclusi i commenti per evidenziare le proprietà chiave. Per una descrizione delle proprietà di questo schema, vedere la sezione relativa ai [valori delle proprietà](#property-values) .

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

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Oggetto Microsoft. ContainerInstance/containerGroups

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | SÌ | Nome del gruppo di contenitori. |
|  apiVersion | enum | SÌ | 2018-10-01 |
|  location | string | No | Percorso della risorsa. |
|  tags | object | No | Tag delle risorse. |
|  identità | object | No | Identità del gruppo di contenitori, se configurata. - [oggetto ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | object | SÌ | [Oggetto ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Oggetto ContainerGroupIdentity

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  type | enum | No | Tipo di identità utilizzata per il gruppo di contenitori. Il tipo ' SystemAssigned, UserAssigned ' include un'identità creata in modo implicito e un set di identità assegnate dall'utente. Il tipo ' none ' rimuoverà tutte le identità dal gruppo di contenitori. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | No | Elenco di identità utente associate al gruppo di contenitori. I riferimenti alle chiavi del dizionario identità utente verranno Azure Resource Manager ID risorsa nel formato '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Oggetto ContainerGroupProperties

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  containers | array | SÌ | Contenitori all'interno del gruppo di contenitori.[oggetto contenitore](#Container)  -  |
|  Credenziali | array | No | Credenziali del registro di sistema dell'immagine da cui viene creato il gruppo di contenitori. - [oggetto ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | enum | No | Criteri di riavvio per tutti i contenitori all'interno del gruppo di contenitori. - `Always` sempre il riavvio `OnFailure` riavvio in caso di errore-`Never` non riavviare mai. -Always, OnFailure, mai |
|  IpAddress | object | No | Tipo di indirizzo IP del gruppo di contenitori. - [oggetto IPAddress](#IpAddress) |
|  osType | enum | SÌ | Tipo di sistema operativo necessario per i contenitori nel gruppo di contenitori. -Windows o Linux |
|  volumes | array | No | Elenco di volumi che possono essere montati da contenitori in questo gruppo di contenitori. - [oggetto volume](#Volume) |
|  diagnostica | object | No | Informazioni di diagnostica per un gruppo di contenitori. - [oggetto ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  NetworkProfile | object | No | Informazioni sul profilo di rete per un gruppo di contenitori. - [oggetto ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | No | Informazioni di configurazione DNS per un gruppo di contenitori. - [oggetto DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Oggetto contenitore

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | SÌ | Nome dell'istanza del contenitore fornito dall'utente. |
|  properties | object | SÌ | Proprietà dell'istanza di contenitore. - [oggetto ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Oggetto ImageRegistryCredential

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  server | string | SÌ | Il server del registro immagini Docker senza protocollo come "http" e "https". |
|  Nome utente | string | SÌ | Nome utente per il registro privato. |
|  password | string | No | Password per il registro di sistema privato. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Oggetto IpAddress

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | SÌ | Elenco di porte esposte nel gruppo di contenitori.[oggetto porta](#Port)  -  |
|  type | enum | SÌ | Specifica se l'indirizzo IP è esposto alla rete Internet pubblica o alla VNET privata. -Pubblico o privato |
|  IP | string | No | IP esposto alla rete Internet pubblica. |
|  dnsNameLabel | string | No | Etichetta del nome DNS per l'indirizzo IP. |


<a id="Volume" />

### <a name="volume-object"></a>Oggetto volume

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | SÌ | Il nome del volume. |
|  azureFile | object | No | Il volume di file di Azure. - [oggetto AzureFileVolume](#AzureFileVolume) |
|  emptyDir | object | No | Volume di directory vuoto. |
|  secret | object | No | Volume Secret. |
|  gitRepo | object | No | Volume del repository git. - [oggetto GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Oggetto ContainerGroupDiagnostics

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | No | Informazioni su log Analytics del gruppo di contenitori. - [oggetto LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Oggetto ContainerGroupNetworkProfile

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  id | string | SÌ | Identificatore di un profilo di rete. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Oggetto DnsConfiguration

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  Nameserver | array | SÌ | Server DNS per il gruppo di contenitori. -stringa |
|  searchDomains | string | No | Domini di ricerca DNS per la ricerca del nome host nel gruppo di contenitori. |
|  options | string | No | Opzioni DNS per il gruppo di contenitori. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Oggetto ContainerProperties

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  image | string | SÌ | Nome dell'immagine utilizzata per creare l'istanza di contenitore. |
|  command | array | No | Comandi da eseguire all'interno dell'istanza del contenitore in formato EXEC. -stringa |
|  ports | array | No | Porte esposte nell'istanza del contenitore. - [oggetto PORTACONTENITORE](#ContainerPort) |
|  environmentVariables | array | No | Variabili di ambiente da impostare nell'istanza del contenitore. - [oggetto Metodo EnvironmentVariable](#EnvironmentVariable) |
|  resources | object | SÌ | Requisiti delle risorse dell'istanza di contenitore. - [oggetto ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | array | No | Il volume viene montato in modo da essere disponibile per l'istanza del contenitore. - [oggetto VolumeMount](#VolumeMount) |
|  livenessProbe | object | No | Probe di vita. - [oggetto ContainerProbe](#ContainerProbe) |
|  readinessProbe | object | No | Probe di conformità. - [oggetto ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Oggetto Port

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | Protocollo associato alla porta. -TCP o UDP |
|  port | integer | SÌ | Numero di porta. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Oggetto AzureFileVolume

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | string | SÌ | Nome della condivisione file di Azure da montare come volume. |
|  readOnly | boolean | No | Flag che indica se il file di Azure condiviso montato come volume è di sola lettura. |
|  storageAccountName | string | SÌ | Nome dell'account di archiviazione che contiene la condivisione file di Azure. |
|  storageAccountKey | string | No | Chiave di accesso dell'account di archiviazione usata per accedere alla condivisione file di Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Oggetto GitRepoVolume

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  Directory | string | No | Nome della directory di destinazione. Non devono contenere o iniziare con '. .'.  Se viene specificato ' .', la directory del volume sarà il repository git.  In caso contrario, se specificato, il volume conterrà il repository git nella sottodirectory con il nome specificato. |
|  repository | string | SÌ | URL del repository |
|  revision | string | No | Hash di commit per la revisione specificata. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Oggetto LogAnalytics

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | SÌ | ID dell'area di lavoro per log Analytics |
|  workspaceKey | string | SÌ | Chiave dell'area di lavoro per log Analytics |
|  logType | enum | No | Tipo di log da utilizzare. -ContainerInsights o ContainerInstanceLogs |
|  metadata | object | No | Metadati per log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Oggetto PORTACONTENITORE

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | Protocollo associato alla porta. -TCP o UDP |
|  port | integer | SÌ | Numero di porta esposto all'interno del gruppo di contenitori. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Oggetto Metodo EnvironmentVariable

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | SÌ | Nome della variabile di ambiente. |
|  value | string | No | Valore della variabile di ambiente. |
|  Parametro securevalue | string | No | Valore della variabile di ambiente protetta. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Oggetto ResourceRequirements

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  requests | object | SÌ | Richieste di risorse dell'istanza di contenitore. - [oggetto ResourceRequests](#ResourceRequests) |
|  limiti | object | No | Limiti delle risorse di questa istanza di contenitore. - [oggetto ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Oggetto VolumeMount

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | SÌ | Nome del montaggio del volume. |
|  mountPath | string | SÌ | Il percorso all'interno del contenitore in cui deve essere montato il volume. Non devono contenere due punti (:). |
|  readOnly | boolean | No | Flag che indica se il montaggio del volume è di sola lettura. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Oggetto ContainerProbe

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  exec | object | No | Comando di esecuzione per l'oggetto Probe- [ContainerExec](#ContainerExec) |
|  httpGet | object | No | Impostazioni HTTP Get per l'oggetto Probe- [ContainerHttpGet](#ContainerHttpGet) |
|  initialDelaySeconds | integer | No | Secondi di ritardo iniziali. |
|  periodSeconds | integer | No | Secondi del periodo. |
|  failureThreshold | integer | No | Soglia di errore. |
|  successThreshold | integer | No | Soglia di esito positivo. |
|  timeoutSeconds | integer | No | Secondi di timeout. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Oggetto ResourceRequests

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | d'acquisto | SÌ | Richiesta di memoria in GB dell'istanza di contenitore. |
|  cpu | d'acquisto | SÌ | Richiesta di CPU dell'istanza di contenitore. |
|  gpu | object | No | Richiesta GPU di questa istanza di contenitore. - [oggetto GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Oggetto ResourceLimits

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | d'acquisto | No | Limite di memoria in GB dell'istanza di contenitore. |
|  cpu | d'acquisto | No | Limite di CPU dell'istanza di contenitore. |
|  gpu | object | No | Limite GPU di questa istanza di contenitore. - [oggetto GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Oggetto ContainerExec

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  command | array | No | Comandi da eseguire all'interno del contenitore. -stringa |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Oggetto ContainerHttpGet

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  path | string | No | Percorso del probe. |
|  port | integer | SÌ | Numero di porta su cui eseguire il probe. |
|  scheme | enum | No | Schema. -http o HTTPS |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Oggetto GpuResource

|  name | Type | Obbligatoria | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | SÌ | Conteggio della risorsa GPU. |
|  sku | enum | SÌ | SKU della risorsa GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione [distribuire un gruppo multicontenitore usando un file YAML](container-instances-multi-container-yaml.md).

Vedere esempi di uso di un file YAML per distribuire gruppi di contenitori in una [rete virtuale](container-instances-vnet.md) o che [montano un volume esterno](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

