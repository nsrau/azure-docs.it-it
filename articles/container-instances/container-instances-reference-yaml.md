---
title: Riferimento YAML per il gruppo di contenitori
description: Informazioni di riferimento per il file YAML supportato da istanze di contenitore di Azure per configurare un gruppo di contenitori
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 5603f2e0f63c4f83a6d3761feb540abb8b8b7d5c
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533495"
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

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  Nome | stringa | Sì | Nome del gruppo di contenitori. |
|  apiVersion | enum | Sì | 2018-10-01 |
|  location | stringa | No | Percorso della risorsa. |
|  Tag | oggetto | No | Tag delle risorse. |
|  identity | oggetto | No | Identità del gruppo di contenitori, se configurata. - [oggetto ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | oggetto | Sì | [Oggetto ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Oggetto ContainerGroupIdentity

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  type | enum | No | Tipo di identità utilizzata per il gruppo di contenitori. Il tipo ' SystemAssigned, UserAssigned ' include un'identità creata in modo implicito e un set di identità assegnate dall'utente. Il tipo ' none ' rimuoverà tutte le identità dal gruppo di contenitori. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | oggetto | No | Elenco di identità utente associate al gruppo di contenitori. I riferimenti alle chiavi del dizionario identità utente verranno Azure Resource Manager ID risorsa nel formato '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Oggetto ContainerGroupProperties

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  containers | array | Sì | Contenitori all'interno del gruppo di contenitori.[oggetto contenitore](#Container)  -  |
|  Credenziali | array | No | Credenziali del registro di sistema dell'immagine da cui viene creato il gruppo di contenitori. - [oggetto ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | enum | No | Criteri di riavvio per tutti i contenitori all'interno del gruppo di contenitori. - `Always` sempre il riavvio `OnFailure` riavvio in caso di errore-`Never` non riavviare mai. -Always, OnFailure, mai |
|  IpAddress | oggetto | No | Tipo di indirizzo IP del gruppo di contenitori. - [oggetto IPAddress](#IpAddress) |
|  osType | enum | Sì | Tipo di sistema operativo necessario per i contenitori nel gruppo di contenitori. -Windows o Linux |
|  volumes | array | No | Elenco di volumi che possono essere montati da contenitori in questo gruppo di contenitori. - [oggetto volume](#Volume) |
|  diagnostica | oggetto | No | Informazioni di diagnostica per un gruppo di contenitori. - [oggetto ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  NetworkProfile | oggetto | No | Informazioni sul profilo di rete per un gruppo di contenitori. - [oggetto ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | oggetto | No | Informazioni di configurazione DNS per un gruppo di contenitori. - [oggetto DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Oggetto contenitore

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  Nome | stringa | Sì | Nome dell'istanza del contenitore fornito dall'utente. |
|  properties | oggetto | Sì | Proprietà dell'istanza di contenitore. - [oggetto ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Oggetto ImageRegistryCredential

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  server | stringa | Sì | Il server del registro immagini Docker senza protocollo come "http" e "https". |
|  username | stringa | Sì | Nome utente per il registro privato. |
|  Password | stringa | No | Password per il registro di sistema privato. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Oggetto IpAddress

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  ports | array | Sì | Elenco di porte esposte nel gruppo di contenitori.[oggetto porta](#Port)  -  |
|  type | enum | Sì | Specifica se l'indirizzo IP è esposto alla rete Internet pubblica o alla VNET privata. -Pubblico o privato |
|  IP | stringa | No | IP esposto alla rete Internet pubblica. |
|  dnsNameLabel | stringa | No | Etichetta del nome DNS per l'indirizzo IP. |


<a id="Volume" />

### <a name="volume-object"></a>Oggetto volume

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  Nome | stringa | Sì | Il nome del volume. |
|  azureFile | oggetto | No | Il volume di file di Azure. - [oggetto AzureFileVolume](#AzureFileVolume) |
|  emptyDir | oggetto | No | Volume di directory vuoto. |
|  secret | oggetto | No | Volume Secret. |
|  gitRepo | oggetto | No | Volume del repository git. - [oggetto GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Oggetto ContainerGroupDiagnostics

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | oggetto | No | Informazioni su log Analytics del gruppo di contenitori. - [oggetto LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Oggetto ContainerGroupNetworkProfile

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  id | stringa | Sì | Identificatore di un profilo di rete. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Oggetto DnsConfiguration

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  Nameserver | array | Sì | Server DNS per il gruppo di contenitori. -stringa |
|  searchDomains | stringa | No | Domini di ricerca DNS per la ricerca del nome host nel gruppo di contenitori. |
|  options | stringa | No | Opzioni DNS per il gruppo di contenitori. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Oggetto ContainerProperties

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  immagine | stringa | Sì | Nome dell'immagine utilizzata per creare l'istanza di contenitore. |
|  command | array | No | Comandi da eseguire all'interno dell'istanza del contenitore in formato EXEC. -stringa |
|  ports | array | No | Porte esposte nell'istanza del contenitore. - [oggetto PORTACONTENITORE](#ContainerPort) |
|  environmentVariables | array | No | Variabili di ambiente da impostare nell'istanza del contenitore. - [oggetto Metodo EnvironmentVariable](#EnvironmentVariable) |
|  resources | oggetto | Sì | Requisiti delle risorse dell'istanza di contenitore. - [oggetto ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | array | No | Il volume viene montato in modo da essere disponibile per l'istanza del contenitore. - [oggetto VolumeMount](#VolumeMount) |
|  livenessProbe | oggetto | No | Probe di vita. - [oggetto ContainerProbe](#ContainerProbe) |
|  readinessProbe | oggetto | No | Probe di conformità. - [oggetto ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Oggetto Port

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | Protocollo associato alla porta. -TCP o UDP |
|  port | numero intero | Sì | Numero di porta. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Oggetto AzureFileVolume

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  shareName | stringa | Sì | Nome della condivisione file di Azure da montare come volume. |
|  readOnly | boolean | No | Flag che indica se il file di Azure condiviso montato come volume è di sola lettura. |
|  storageAccountName | stringa | Sì | Nome dell'account di archiviazione che contiene la condivisione file di Azure. |
|  storageAccountKey | stringa | No | Chiave di accesso dell'account di archiviazione usata per accedere alla condivisione file di Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Oggetto GitRepoVolume

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  Directory | stringa | No | Nome della directory di destinazione. Non devono contenere o iniziare con '. .'.  Se viene specificato ' .', la directory del volume sarà il repository git.  In caso contrario, se specificato, il volume conterrà il repository git nella sottodirectory con il nome specificato. |
|  repository | stringa | Sì | URL del repository |
|  revision | stringa | No | Hash di commit per la revisione specificata. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Oggetto LogAnalytics

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  workspaceId | stringa | Sì | ID dell'area di lavoro per log Analytics |
|  workspaceKey | stringa | Sì | Chiave dell'area di lavoro per log Analytics |
|  logType | enum | No | Tipo di log da utilizzare. -ContainerInsights o ContainerInstanceLogs |
|  metadata | oggetto | No | Metadati per log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Oggetto PORTACONTENITORE

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | Protocollo associato alla porta. -TCP o UDP |
|  port | numero intero | Sì | Numero di porta esposto all'interno del gruppo di contenitori. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Oggetto Metodo EnvironmentVariable

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  Nome | stringa | Sì | Nome della variabile di ambiente. |
|  Valore | stringa | No | Valore della variabile di ambiente. |
|  Parametro securevalue | stringa | No | Valore della variabile di ambiente protetta. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Oggetto ResourceRequirements

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  requests | oggetto | Sì | Richieste di risorse dell'istanza di contenitore. - [oggetto ResourceRequests](#ResourceRequests) |
|  limiti | oggetto | No | Limiti delle risorse di questa istanza di contenitore. - [oggetto ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Oggetto VolumeMount

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  Nome | stringa | Sì | Nome del montaggio del volume. |
|  mountPath | stringa | Sì | Il percorso all'interno del contenitore in cui deve essere montato il volume. Non devono contenere due punti (:). |
|  readOnly | boolean | No | Flag che indica se il montaggio del volume è di sola lettura. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Oggetto ContainerProbe

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  Exec | oggetto | No | Comando di esecuzione per l'oggetto Probe- [ContainerExec](#ContainerExec) |
|  httpGet | oggetto | No | Impostazioni HTTP Get per l'oggetto Probe- [ContainerHttpGet](#ContainerHttpGet) |
|  initialDelaySeconds | numero intero | No | Secondi di ritardo iniziali. |
|  periodSeconds | numero intero | No | Secondi del periodo. |
|  failureThreshold | numero intero | No | Soglia di errore. |
|  successThreshold | numero intero | No | Soglia di esito positivo. |
|  timeoutSeconds | numero intero | No | Secondi di timeout. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Oggetto ResourceRequests

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Sì | Richiesta di memoria in GB dell'istanza di contenitore. |
|  cpu | number | Sì | Richiesta di CPU dell'istanza di contenitore. |
|  gpu | oggetto | No | Richiesta GPU di questa istanza di contenitore. - [oggetto GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Oggetto ResourceLimits

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | No | Limite di memoria in GB dell'istanza di contenitore. |
|  cpu | number | No | Limite di CPU dell'istanza di contenitore. |
|  gpu | oggetto | No | Limite GPU di questa istanza di contenitore. - [oggetto GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Oggetto ContainerExec

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  command | array | No | Comandi da eseguire all'interno del contenitore. -stringa |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Oggetto ContainerHttpGet

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  path | stringa | No | Percorso del probe. |
|  port | numero intero | Sì | Numero di porta su cui eseguire il probe. |
|  scheme | enum | No | Schema. -http o HTTPS |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Oggetto GpuResource

|  Nome | digitare | obbligatori | Valore |
|  ---- | ---- | ---- | ---- |
|  Numero | numero intero | Sì | Conteggio della risorsa GPU. |
|  sku | enum | Sì | SKU della risorsa GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione [distribuire un gruppo multicontenitore usando un file YAML](container-instances-multi-container-yaml.md).

Vedere esempi di uso di un file YAML per distribuire gruppi di contenitori in una [rete virtuale](container-instances-vnet.md) o che [montano un volume esterno](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

