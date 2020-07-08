---
title: Configurare il probe di conformità sull'istanza del contenitore
description: Informazioni su come configurare un probe per garantire che i contenitori nelle istanze di contenitore di Azure ricevano richieste solo quando sono pronte
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76935688"
---
# <a name="configure-readiness-probes"></a>Configurare probe di idoneità

Per le applicazioni incluse in contenitori che gestiscono il traffico, potrebbe essere necessario verificare che il contenitore sia pronto per la gestione delle richieste in ingresso. Istanze di contenitore di Azure supporta i probe di conformità per includere le configurazioni, in modo che non sia possibile accedere al contenitore in determinate condizioni. Il probe di conformità si comporta come un [Probe di conformità di Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Ad esempio, un'app contenitore potrebbe dover caricare un set di dati di grandi dimensioni durante l'avvio e non si vuole che riceva richieste durante questo periodo di tempo.

Questo articolo illustra come distribuire un gruppo di contenitori che include un probe di conformità, in modo che un contenitore riceva il traffico solo quando il probe ha esito positivo.

Istanze di contenitore di Azure supporta anche i [Probe di liveity](container-instances-liveness-probe.md), che è possibile configurare per provocare il riavvio automatico di un contenitore non integro.

> [!NOTE]
> Attualmente non è possibile usare un probe di conformità in un gruppo di contenitori distribuito in una rete virtuale.

## <a name="yaml-configuration"></a>Configurazione YAML

Ad esempio, creare un `readiness-probe.yaml` file con il frammento di codice seguente che include un probe di conformità. Questo file definisce un gruppo di contenitori costituito da un contenitore che esegue un'app Web di piccole dimensioni. L'app viene distribuita dall' `mcr.microsoft.com/azuredocs/aci-helloworld` immagine pubblica. Questa app in contenitori è illustrata anche in [distribuire un'istanza di contenitore in Azure usando l'interfaccia della riga di](container-instances-quickstart.md) comando di Azure e altre guide introduttive.

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

### <a name="start-command"></a>Comando Avvia

La distribuzione include una `command` proprietà che definisce un comando di avvio che viene eseguito all'avvio dell'esecuzione del contenitore. Questa proprietà accetta una matrice di stringhe. Questo comando Simula un'ora in cui viene eseguita l'app Web, ma il contenitore non è pronto. 

Innanzitutto, avvia una sessione della shell ed esegue un `node` comando per avviare l'app Web. Avvia anche un comando per la sospensione di 240 secondi, dopo il quale viene creato un file denominato `ready` nella `/tmp` Directory:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Comando di conformità

Questo file YAML definisce un `readinessProbe` che supporta un `exec` comando di conformità che funge da controllo di conformità. Questo comando di conformità di esempio verifica l'esistenza del `ready` file nella `/tmp` Directory.

Quando il `ready` file non esiste, il comando di conformità viene chiuso con un valore diverso da zero. il contenitore continua l'esecuzione ma non è possibile accedervi. Quando il comando termina correttamente con il codice di uscita 0, il contenitore è pronto per l'accesso. 

La `periodSeconds` proprietà indica che il comando di conformità deve essere eseguito ogni 5 secondi. Il probe di conformità viene eseguito per la durata del gruppo di contenitori.

## <a name="example-deployment"></a>Distribuzione di esempio

Eseguire il comando seguente per distribuire un gruppo di contenitori con la configurazione YAML precedente:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Visualizza controlli di conformità

In questo esempio, durante i primi 240 secondi, il comando di conformità ha esito negativo quando controlla l' `ready` esistenza del file. Il codice di stato ha restituito segnali che il contenitore non è pronto.

Questi eventi possono essere visualizzati dal portale di Azure o dall'interfaccia della riga di comando di Azure. Ad esempio, il portale Mostra gli eventi di tipo che `Unhealthy` vengono attivati in caso di errore del comando di conformità. 

![Evento di non integrità nel portale][portal-unhealthy]

## <a name="verify-container-readiness"></a>Verificare la conformità del contenitore

Dopo l'avvio del contenitore, è possibile verificare che non sia inizialmente accessibile. Dopo il provisioning, ottenere l'indirizzo IP del gruppo di contenitori:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Provare ad accedere al sito mentre il probe di conformità ha esito negativo:

```bash
wget <ipAddress>
```

L'output mostra che il sito non è accessibile inizialmente:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Dopo 240 secondi, il comando di conformità ha esito positivo, segnalando che il contenitore è pronto. A questo punto, quando si esegue il `wget` comando, l'operazione ha esito positivo:

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

Quando il contenitore è pronto, è anche possibile accedere all'app Web passando all'indirizzo IP usando un Web browser.

> [!NOTE]
> Il probe di conformità continua a essere eseguito per la durata del gruppo di contenitori. Se il comando di conformità ha esito negativo in un secondo momento, il contenitore diventa inaccessibile. 
> 

## <a name="next-steps"></a>Passaggi successivi

Un probe di conformità può essere utile in scenari che coinvolgono gruppi multicontenitore che sono costituiti da contenitori dipendenti. Per altre informazioni sugli scenari con più contenitori, vedere [gruppi di contenitori in istanze di contenitore di Azure](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
