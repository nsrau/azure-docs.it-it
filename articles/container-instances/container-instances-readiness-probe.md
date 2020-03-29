---
title: Configurare il probe di conformità nell'istanza del contenitoreSet up readiness probe on container instance
description: Informazioni su come configurare un probe per garantire che i contenitori nelle istanze del contenitore di Azure ricevano le richieste solo quando sono prontiLearn how to configure a probe to ensure containers in Azure Container Instances receive requests when they are ready
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935688"
---
# <a name="configure-readiness-probes"></a>Configurare probe di idoneità

Per le applicazioni in contenitori che gestiscono il traffico, è possibile verificare che il contenitore sia pronto per gestire le richieste in ingresso. Le istanze del contenitore di Azure supportano probe di conformità per includere configurazioni in modo che non sia possibile accedere al contenitore in determinate condizioni. La sonda di prontezza si comporta come una [sonda di prontezza Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Ad esempio, un'app contenitore potrebbe dover caricare un set di dati di grandi dimensioni durante l'avvio e non si vuole che riceva richieste durante questo periodo.

In questo articolo viene illustrato come distribuire un gruppo di contenitori che include un probe di conformità, in modo che un contenitore riceva il traffico solo quando il probe ha esito positivo.

Le istanze del contenitore di Azure supportano anche i probe di [liveness,](container-instances-liveness-probe.md)che è possibile configurare per il riavvio automatico di un contenitore non integro.

> [!NOTE]
> Attualmente non è possibile usare un probe di conformità in un gruppo di contenitori distribuito in una rete virtuale.

## <a name="yaml-configuration"></a>Configurazione YAML

Ad esempio, creare `readiness-probe.yaml` un file con il frammento di codice seguente che include un probe di conformità. Questo file definisce un gruppo di contenitori costituito da un contenitore che esegue un'app Web di piccole dimensioni. L'app viene distribuita `mcr.microsoft.com/azuredocs/aci-helloworld` dall'immagine pubblica. Questa app in contenitori viene illustrata anche in [Distribuire un'istanza del contenitore in Azure usando l'interfaccia della](container-instances-quickstart.md) riga di comando di Azure e altre guide introduttive.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Comando di avvio

La distribuzione `command` include una proprietà che definisce un comando iniziale che viene eseguito al primo avvio dell'esecuzione del contenitore. Questa proprietà accetta una matrice di stringhe. Questo comando simula un'ora di esecuzione dell'app Web ma il contenitore non è pronto. 

In primo luogo, avvia una `node` sessione della shell ed esegue un comando per avviare l'app web. Avvia anche un comando da sommettere per 240 `ready` secondi, `/tmp` dopo di che crea un file chiamato all'interno della directory:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Comando di preparazione

Questo file YAML `readinessProbe` definisce un `exec` che supporta un comando di conformità che funge da controllo di conformità. In questo esempio viene verificata l'esistenza del comando di conformità per l'esistenza del `ready` file nella `/tmp` directory.

Quando `ready` il file non esiste, il comando readiness viene chiuso con un valore diverso da zero. il contenitore continua a essere eseguito, ma non è possibile accedervi. Quando il comando viene chiuso correttamente con il codice di uscita 0, il contenitore è pronto per l'accesso. 

La `periodSeconds` proprietà indica che il comando di conformità deve essere eseguito ogni 5 secondi. La sonda di conformità viene eseguita per tutta la durata del gruppo di contenitori.

## <a name="example-deployment"></a>Distribuzione di esempio

Eseguire il comando seguente per distribuire un gruppo di contenitori con la configurazione YAML precedente:Run the following command to deploy a container group with the preceding YAML configuration:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Visualizzare i controlli di conformità

In questo esempio, durante i primi 240 secondi, il `ready` comando readiness ha esito negativo quando viene eseguita la verifica dell'esistenza del file. Il codice di stato ha restituito segnali che il contenitore non è pronto.

Questi eventi possono essere visualizzati dal portale di Azure o dall'interfaccia della riga di comando di Azure. Ad esempio, il portale `Unhealthy` mostra che gli eventi di tipo vengono attivati in caso di errore del comando di conformità. 

![Evento di non integrità nel portale][portal-unhealthy]

## <a name="verify-container-readiness"></a>Verificare la disponibilità del contenitoreVerify container readiness

Dopo aver avviato il contenitore, è possibile verificare che inizialmente non sia accessibile. Dopo il provisioning, ottenere l'indirizzo IP del gruppo di contenitori:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Provare ad accedere al sito mentre il probe di conformità ha esito negativo:

```bash
wget <ipAddress>
```

L'output mostra che il sito inizialmente non è accessibile:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Dopo 240 secondi, il comando di conformità ha esito positivo, segnalando che il contenitore è pronto. Ora, quando si `wget` esegue il comando, ha esito positivo:Now, when you run the command, it succeeds:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Quando il contenitore è pronto, è anche possibile accedere all'app Web accedendo all'indirizzo IP tramite un Web browser.

> [!NOTE]
> Il probe di conformità continua a essere eseguito per tutta la durata del gruppo di contenitori. Se il comando readiness ha esito negativo in un secondo momento, il contenitore diventa nuovamente inaccessibile. 
> 

## <a name="next-steps"></a>Passaggi successivi

Un probe di conformità potrebbe essere utile in scenari che coinvolgono gruppi multi-contenitore costituiti da contenitori dipendenti. Per altre informazioni sugli scenari multi-contenitore, vedere Gruppi di [contenitori nelle istanze dei contenitori](container-instances-container-groups.md)di Azure.For more information about multi-container scenarios, see Container groups in Azure Container Instances.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
