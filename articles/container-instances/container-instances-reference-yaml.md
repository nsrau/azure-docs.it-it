---
title: Riferimento YAML per istanze di contenitore di Azure
description: Informazioni di riferimento per il file YAML supportato da istanze di contenitore di Azure per configurare un gruppo di contenitori
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/12/2019
ms.author: danlep
ms.openlocfilehash: 2e6be18371cf3ff96d1ce91d4dde26ff1f14021b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179904"
---
# <a name="yaml-reference-azure-container-instances"></a>Riferimento YAML: Istanze di Azure Container

Questo articolo illustra la sintassi e le proprietà del file YAML supportato dalle istanze di contenitore di Azure per configurare un [gruppo di contenitori](container-instances-container-groups.md). Usare un file YAML per inserire la configurazione di gruppo nel comando [AZ container create][az-container-create] nell'interfaccia della riga di comando di Azure. 

Un file YAML è un modo pratico per configurare un gruppo di contenitori per le distribuzioni riproducibili. Si tratta di un'alternativa concisa all'uso di un [modello di gestione risorse](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) o degli SDK di istanze di contenitore di Azure per creare o aggiornare un gruppo di contenitori.

> [!NOTE]
> Questo riferimento si applica ai file YAML per la versione `2018-10-01`dell'API REST di istanze di contenitore di Azure.

## <a name="schema"></a>Schema 

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
      ports: # Exposed ports on the instance
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

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Nome del gruppo di contenitori. |
|  apiVersion | enumerazione | Yes | 2018-10-01 |
|  location | string | No | Percorso della risorsa. |
|  tags | object | No | Tag delle risorse. |
|  identity | object | No | Identità del gruppo di contenitori, se configurata. - [Oggetto ContainerGroupIdentity](#ContainerGroupIdentity) |
|  proprietà | object | Yes | [Oggetto ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Oggetto ContainerGroupIdentity

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  type | enumerazione | No | Tipo di identità utilizzata per il gruppo di contenitori. Il tipo ' SystemAssigned, UserAssigned ' include un'identità creata in modo implicito e un set di identità assegnate dall'utente. Il tipo ' none ' rimuoverà tutte le identità dal gruppo di contenitori. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | No | Elenco di identità utente associate al gruppo di contenitori. I riferimenti alle chiavi del dizionario identità utente verranno Azure Resource Manager ID risorsa nel formato '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName '. }'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Oggetto ContainerGroupProperties

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  containers | matrice | Yes | Contenitori all'interno del gruppo di contenitori. - [Oggetto contenitore](#Container) |
|  Credenziali | matrice | No | Credenziali del registro di sistema dell'immagine da cui viene creato il gruppo di contenitori. - [Oggetto ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | enumerazione | No | Criteri di riavvio per tutti i contenitori all'interno del gruppo di contenitori. - `Always`Riavvia sempre- `OnFailure` riavvia in caso di `Never` errore-non riavviare mai. -Always, OnFailure, mai |
|  ipAddress | object | No | Tipo di indirizzo IP del gruppo di contenitori. - [Oggetto IpAddress](#IpAddress) |
|  osType | enumerazione | Yes | Tipo di sistema operativo necessario per i contenitori nel gruppo di contenitori. -Windows o Linux |
|  volumi | matrice | No | Elenco di volumi che possono essere montati da contenitori in questo gruppo di contenitori. - [Oggetto volume](#Volume) |
|  diagnostica | object | No | Informazioni di diagnostica per un gruppo di contenitori. - [Oggetto ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  NetworkProfile | object | No | Informazioni sul profilo di rete per un gruppo di contenitori. - [Oggetto ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | No | Informazioni di configurazione DNS per un gruppo di contenitori. - [Oggetto DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Oggetto contenitore

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Nome dell'istanza del contenitore fornito dall'utente. |
|  proprietà | object | Yes | Proprietà dell'istanza di contenitore. - [Oggetto ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Oggetto ImageRegistryCredential

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  server | string | Yes | Il server del registro immagini Docker senza protocollo come "http" e "https". |
|  userName | string | Yes | Nome utente per il registro privato. |
|  password | string | No | Password per il registro di sistema privato. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Oggetto IpAddress

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  ports | matrice | Yes | Elenco di porte esposte nel gruppo di contenitori. - [Oggetto Port](#Port) |
|  type | enumerazione | Yes | Specifica se l'indirizzo IP è esposto alla rete Internet pubblica o alla VNET privata. -Pubblico o privato |
|  ip | string | No | IP esposto alla rete Internet pubblica. |
|  dnsNameLabel | string | No | Etichetta del nome DNS per l'indirizzo IP. |


<a id="Volume" />

### <a name="volume-object"></a>Oggetto volume

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Il nome del volume. |
|  AzureFile | object | No | Il volume di file di Azure. - [Oggetto AzureFileVolume](#AzureFileVolume) |
|  emptyDir | object | No | Volume di directory vuoto. |
|  secret | object | No | Volume Secret. |
|  gitRepo | object | No | Volume del repository git. - [Oggetto GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Oggetto ContainerGroupDiagnostics

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | No | Informazioni su log Analytics del gruppo di contenitori. - [Oggetto LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Oggetto ContainerGroupNetworkProfile

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  id | string | Yes | Identificatore di un profilo di rete. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Oggetto DnsConfiguration

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  Nameserver | matrice | Yes | Server DNS per il gruppo di contenitori. -stringa |
|  searchDomains | string | No | Domini di ricerca DNS per la ricerca del nome host nel gruppo di contenitori. |
|  opzioni | string | No | Opzioni DNS per il gruppo di contenitori. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Oggetto ContainerProperties

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  image | string | Yes | Nome dell'immagine utilizzata per creare l'istanza di contenitore. |
|  command | matrice | No | Comandi da eseguire all'interno dell'istanza del contenitore in formato EXEC. -stringa |
|  ports | matrice | No | Porte esposte nell'istanza del contenitore. - [Oggetto PORTACONTENITORE](#ContainerPort) |
|  environmentVariables | matrice | No | Variabili di ambiente da impostare nell'istanza del contenitore. - [Oggetto Metodo EnvironmentVariable](#EnvironmentVariable) |
|  risorse | object | Yes | Requisiti delle risorse dell'istanza di contenitore. - [Oggetto ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | matrice | No | Il volume viene montato in modo da essere disponibile per l'istanza del contenitore. - [Oggetto VolumeMount](#VolumeMount) |
|  livenessProbe | object | No | Probe di vita. - [Oggetto ContainerProbe](#ContainerProbe) |
|  readinessProbe | object | No | Probe di conformità. - [Oggetto ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Oggetto Port

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  protocollo | enumerazione | No | Protocollo associato alla porta. -TCP o UDP |
|  porta | integer | Yes | Numero di porta. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Oggetto AzureFileVolume

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | string | Yes | Nome della condivisione file di Azure da montare come volume. |
|  readOnly | boolean | No | Flag che indica se il file di Azure condiviso montato come volume è di sola lettura. |
|  storageAccountName | string | Yes | Nome dell'account di archiviazione che contiene la condivisione file di Azure. |
|  storageAccountKey | string | No | Chiave di accesso dell'account di archiviazione usata per accedere alla condivisione file di Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Oggetto GitRepoVolume

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  directory | string | No | Nome della directory di destinazione. Non devono contenere o iniziare con '. .'.  Se viene specificato ' .', la directory del volume sarà il repository git.  In caso contrario, se specificato, il volume conterrà il repository git nella sottodirectory con il nome specificato. |
|  repository | string | Yes | URL del repository |
|  revision | string | No | Hash di commit per la revisione specificata. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Oggetto LogAnalytics

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Yes | ID dell'area di lavoro per log Analytics |
|  workspaceKey | string | Yes | Chiave dell'area di lavoro per log Analytics |
|  logType | enumerazione | No | Tipo di log da utilizzare. -ContainerInsights o ContainerInstanceLogs |
|  metadata | object | No | Metadati per log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Oggetto PORTACONTENITORE

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  protocollo | enumerazione | No | Protocollo associato alla porta. -TCP o UDP |
|  porta | integer | Yes | Numero di porta esposto all'interno del gruppo di contenitori. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Oggetto Metodo EnvironmentVariable

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Nome della variabile di ambiente. |
|  value | string | No | Valore della variabile di ambiente. |
|  Parametro securevalue | string | No | Valore della variabile di ambiente protetta. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Oggetto ResourceRequirements

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  richieste | object | Yes | Richieste di risorse dell'istanza di contenitore. - [Oggetto ResourceRequests](#ResourceRequests) |
|  limiti | object | No | Limiti delle risorse di questa istanza di contenitore. - [Oggetto ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Oggetto VolumeMount

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Nome del montaggio del volume. |
|  mountPath | string | Yes | Il percorso all'interno del contenitore in cui deve essere montato il volume. Non devono contenere due punti (:). |
|  readOnly | boolean | No | Flag che indica se il montaggio del volume è di sola lettura. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Oggetto ContainerProbe

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  Exec | object | No | Comando di esecuzione per l'oggetto Probe- [ContainerExec](#ContainerExec) |
|  httpGet | object | No | Impostazioni HTTP Get per l'oggetto Probe- [ContainerHttpGet](#ContainerHttpGet) |
|  initialDelaySeconds | integer | No | Secondi di ritardo iniziali. |
|  periodSeconds | integer | No | Secondi del periodo. |
|  failureThreshold | integer | No | Soglia di errore. |
|  successThreshold | integer | No | Soglia di esito positivo. |
|  timeoutSeconds | integer | No | Secondi di timeout. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Oggetto ResourceRequests

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Yes | Richiesta di memoria in GB dell'istanza di contenitore. |
|  cpu | number | Yes | Richiesta di CPU dell'istanza di contenitore. |
|  gpu | object | No | Richiesta GPU di questa istanza di contenitore. - [Oggetto GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Oggetto ResourceLimits

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | No | Limite di memoria in GB dell'istanza di contenitore. |
|  cpu | number | No | Limite di CPU dell'istanza di contenitore. |
|  gpu | object | No | Limite GPU di questa istanza di contenitore. - [Oggetto GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Oggetto ContainerExec

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  command | matrice | No | Comandi da eseguire all'interno del contenitore. -stringa |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Oggetto ContainerHttpGet

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  path | string | No | Percorso del probe. |
|  porta | integer | Yes | Numero di porta su cui eseguire il probe. |
|  schema | enumerazione | No | Schema. -http o HTTPS |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Oggetto GpuResource

|  Attività | Type | Richiesto | Value |
|  ---- | ---- | ---- | ---- |
|  numero | integer | Yes | Conteggio della risorsa GPU. |
|  sku | enumerazione | Yes | SKU della risorsa GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione [distribuire un gruppo multicontenitore usando un file YAML](container-instances-multi-container-yaml.md).

Vedere esempi di uso di un file YAML per distribuire gruppi di contenitori in una [rete virtuale](container-instances-vnet.md) o che [montano un volume esterno](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

