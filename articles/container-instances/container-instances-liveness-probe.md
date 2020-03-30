---
title: Configurare il probe di liveness nell'istanza del contenitoreSet up liveness probe on container instance
description: Informazioni su come configurare probe di attività per riavviare i contenitori non integri in Istanze di Azure Container
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934171"
---
# <a name="configure-liveness-probes"></a>Configurare probe di attività

Le applicazioni containerizzate possono essere eseguite per lunghi periodi di tempo, causando stati interrotti che potrebbero dover essere riparati riavviando il contenitore. Le istanze del contenitore di Azure supportano i probe di liveness in modo che sia possibile configurare i contenitori all'interno del gruppo di contenitori per il riavvio se la funzionalità critica non funziona. La sonda di liveness si comporta come una [sonda di liveness Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

Questo articolo illustra come distribuire un gruppo di contenitori che include un probe di attività, per dimostrare il riavvio automatico di un contenitore non integro simulato.

Le istanze del contenitore di Azure supportano anche i probe di [conformità,](container-instances-readiness-probe.md)che è possibile configurare per garantire che il traffico raggiunga un contenitore solo quando è pronto.

> [!NOTE]
> Attualmente non è possibile usare un probe di liveness in un gruppo di contenitori distribuito in una rete virtuale.

## <a name="yaml-deployment"></a>Distribuzione con file YAML

Creare un file `liveness-probe.yaml` con il frammento seguente. Questo file definisce un gruppo di contenitori costituito da un contenitore NGNIX che diventa non integro.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Eseguire il comando seguente per distribuire questo gruppo di contenitori con la configurazione YAML sopra riportata:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Comando di avvio

La distribuzione `command` include una proprietà che definisce un comando iniziale che viene eseguito al primo avvio dell'esecuzione del contenitore. Questa proprietà accetta una matrice di stringhe. Questo comando simula il contenitore che entra in uno stato non integro.

In primo luogo, avvia una sessione `healthy` bash `/tmp` e crea un file chiamato all'interno della directory. Quindi viene in stato di sospensione per 30 secondi prima di eliminare il file, quindi entra in una sospensione di 10 minuti:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Comando di verifica dell'attività (liveness)

Questa distribuzione definisce un `livenessProbe` `exec` che supporta un comando di liveness che funge da controllo di liveness. Se questo comando termina con un valore diverso da zero, il `healthy` contenitore viene interrotto e riavviato, segnalando il file non è stato trovato. Se questo comando viene chiuso correttamente con il codice di uscita 0, non viene eseguita alcuna azione.

La proprietà `periodSeconds` stabilisce che il comando di verifica dell'attività deve essere eseguito ogni 5 secondi.

## <a name="verify-liveness-output"></a>Verificare l'output di attività

Entro i primi 30 secondi deve essere confermata l'esistenza del file `healthy` creato dal comando di avvio. Quando il comando di `healthy` liveness controlla l'esistenza del file, il codice di stato restituisce 0, segnalando esito positivo, pertanto non viene eseguito alcun riavvio.

Dopo 30 secondi, il `cat /tmp/healthy` comando inizia a non riuscire, causando eventi non integri e di eliminazione.

Questi eventi possono essere visualizzati dal portale di Azure o dall'interfaccia della riga di comando di Azure.

![Evento di non integrità nel portale][portal-unhealthy]

Visualizzando gli eventi nel portale di `Unhealthy` Azure, gli eventi di tipo vengono attivati in caso di errore del comando di liveness. L'evento successivo `Killing`è di tipo , che indica l'eliminazione di un contenitore in modo che possa iniziare un riavvio. Il conteggio dei riavvii per il contenitore viene incrementato ogni volta che si verifica questo evento.

I riavvii vengono completati sul posto in modo da mantenere le risorse come gli indirizzi IP pubblici e il contenuto specifico del nodo.

![Contatore di riavvio nel portale][portal-restart]

Se il probe di liveness ha esito negativo continuamente e attiva troppi riavvii, il contenitore immette un ritardo di back-off esponenziale.

## <a name="liveness-probes-and-restart-policies"></a>Probe di attività e criteri di riavvio

I criteri di riavvio sostituiscono il comportamento di riavvio attivato dai probe di attività. Ad esempio, se `restartPolicy = Never` si imposta un *probe di* livellato e un probe di liveness, il gruppo di contenitori non verrà riavviato a causa di un controllo di liveness non riuscito. Il gruppo di contenitori è invece conforme `Never`ai criteri di riavvio del gruppo di contenitori di .

## <a name="next-steps"></a>Passaggi successivi

Per gli scenari basati su attività potrebbe essere necessario che un probe di attività abiliti i riavvii automatici se una funzione prerequisito non funziona correttamente. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [Eseguire attività in contenitori in Istanze di Azure Container](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
