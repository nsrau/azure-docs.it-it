---
title: Configurare il probe di liveity nell'istanza del contenitore
description: Informazioni su come configurare probe di attività per riavviare i contenitori non integri in Istanze di Azure Container
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: 96d98d18a3f0ac666fb2c057216f7844b176d177
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481688"
---
# <a name="configure-liveness-probes"></a>Configurare probe di attività

Le applicazioni incluse in contenitori possono essere eseguite per periodi prolungati di tempo, causando stati interrotti che potrebbero dover essere ripristinati riavviando il contenitore. Istanze di contenitore di Azure supporta i probe di liveity per poter configurare i contenitori all'interno del gruppo di contenitori per il riavvio se le funzionalità critiche non funzionano. Il probe di liveity si comporta come un [Probe di Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

Questo articolo illustra come distribuire un gruppo di contenitori che include un probe di attività, per dimostrare il riavvio automatico di un contenitore non integro simulato.

Istanze di contenitore di Azure supporta anche [Probe di conformità](container-instances-readiness-probe.md), che è possibile configurare per garantire che il traffico raggiunga un contenitore solo quando è pronto per l'it.

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

La distribuzione definisce un comando iniziale da eseguire quando viene avviata per la prima volta l'esecuzione del contenitore, definito dalla proprietà `command`, che accetta una matrice di stringhe. In questo esempio, verrà avviata una sessione bash e verrà creato un file denominato `healthy` all'interno della directory `/tmp` passando questo comando:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Quindi verrà sospeso per 30 secondi prima di eliminare il file e quindi entrerà in una sospensione di 10 minuti.

### <a name="liveness-command"></a>Comando di verifica dell'attività (liveness)

Questa distribuzione definisce un `livenessProbe` che supporta un comando `exec` liveity che funge da controllo della durata. Se questo comando termina con un valore diverso da zero, il contenitore verrà terminato e riavviato, a indicare che non è stato trovato il file `healthy`. Se questo comando termina correttamente con il codice di uscita 0, non verrà eseguita alcuna azione.

La proprietà `periodSeconds` stabilisce che il comando di verifica dell'attività deve essere eseguito ogni 5 secondi.

## <a name="verify-liveness-output"></a>Verificare l'output di attività

Entro i primi 30 secondi deve essere confermata l'esistenza del file `healthy` creato dal comando di avvio. Quando il comando di verifica dell'attività controlla l'esistenza del file `healthy`, il codice di stato restituisce zero, a indicare l'esito positivo, quindi non si verifica alcun riavvio.

Dopo 30 secondi, inizieranno gli errori per `cat /tmp/healthy`, con conseguente generazione degli eventi per lo stato non integro e la terminazione.

Questi eventi possono essere visualizzati dal portale di Azure o dall'interfaccia della riga di comando di Azure.

![Evento di non integrità nel portale][portal-unhealthy]

Quando si visualizzano gli eventi nel portale di Azure, gli eventi di tipo `Unhealthy` verranno attivati in caso di errore del comando liveness. L'evento successivo sarà di tipo `Killing`, che significa l'eliminazione di un contenitore, quindi può iniziare un riavvio. Il numero di riavvio per il contenitore viene incrementato ogni volta che si verifica questo evento.

I riavvi vengono completati sul posto, in modo da mantenere risorse come gli indirizzi IP pubblici e il contenuto specifico del nodo.

![Contatore di riavvio nel portale][portal-restart]

Se il probe di attività ha continuamente esito negativo e genera troppi riavvii, per il contenitore subentrerà un ritardo di backoff esponenziale.

## <a name="liveness-probes-and-restart-policies"></a>Probe di attività e criteri di riavvio

I criteri di riavvio sostituiscono il comportamento di riavvio attivato dai probe di attività. Se, ad esempio, si impostano un `restartPolicy = Never` *e* un probe di Livezza, il gruppo di contenitori non viene riavviato a causa di un controllo del tempo di insuccesso. Il gruppo di contenitori dovrà invece rispettare i criteri di riavvio `Never` del gruppo di contenitori.

## <a name="next-steps"></a>Passaggi successivi

Per gli scenari basati su attività potrebbe essere necessario che un probe di attività abiliti i riavvii automatici se una funzione prerequisito non funziona correttamente. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [Eseguire attività in contenitori in Istanze di Azure Container](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
