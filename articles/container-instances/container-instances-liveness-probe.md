---
title: Configurare il probe di liveity nell'istanza del contenitore
description: Informazioni su come configurare probe di attività per riavviare i contenitori non integri in Istanze di Azure Container
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76934171"
---
# <a name="configure-liveness-probes"></a>Configurare probe di attività

Le applicazioni incluse in contenitori possono essere eseguite per periodi prolungati di tempo, causando stati interrotti che potrebbero dover essere ripristinati riavviando il contenitore. Istanze di contenitore di Azure supporta i probe di liveity per poter configurare i contenitori all'interno del gruppo di contenitori per il riavvio se le funzionalità critiche non funzionano. Il probe di liveity si comporta come un [Probe di Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

Questo articolo illustra come distribuire un gruppo di contenitori che include un probe di attività, per dimostrare il riavvio automatico di un contenitore non integro simulato.

Istanze di contenitore di Azure supporta anche [Probe di conformità](container-instances-readiness-probe.md), che è possibile configurare per garantire che il traffico raggiunga un contenitore solo quando è pronto per l'it.

> [!NOTE]
> Attualmente non è possibile usare un probe di liveity in un gruppo di contenitori distribuito in una rete virtuale.

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

### <a name="start-command"></a>Comando Avvia

La distribuzione include una `command` proprietà che definisce un comando di avvio che viene eseguito all'avvio dell'esecuzione del contenitore. Questa proprietà accetta una matrice di stringhe. Questo comando simula il contenitore che entra in uno stato non integro.

Viene innanzitutto avviata una sessione bash e viene creato un file `healthy` denominato nella `/tmp` directory. Quindi dorme per 30 secondi prima di eliminare il file, quindi entra in uno stato di sospensione di 10 minuti:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Comando di verifica dell'attività (liveness)

Questa distribuzione definisce un `livenessProbe` che supporta un `exec` comando di liveity che funge da controllo della durata. Se questo comando viene terminato con un valore diverso da zero, il contenitore viene terminato e riavviato, segnalando che `healthy` il file non è stato trovato. Se il comando termina correttamente con il codice di uscita 0, non viene eseguita alcuna azione.

La proprietà `periodSeconds` stabilisce che il comando di verifica dell'attività deve essere eseguito ogni 5 secondi.

## <a name="verify-liveness-output"></a>Verificare l'output di attività

Entro i primi 30 secondi deve essere confermata l'esistenza del file `healthy` creato dal comando di avvio. Quando il comando di liveity controlla l' `healthy` esistenza del file, il codice di stato restituisce 0, segnalando l'esito positivo, quindi non viene eseguito il riavvio.

Dopo 30 secondi, il `cat /tmp/healthy` comando inizia ad avere esito negativo, causando la generazione di eventi non integri e l'eliminazione.

Questi eventi possono essere visualizzati dal portale di Azure o dall'interfaccia della riga di comando di Azure.

![Evento di non integrità nel portale][portal-unhealthy]

Visualizzando gli eventi nel portale di Azure, gli eventi di tipo `Unhealthy` vengono attivati in caso di errore del comando di Livezza. L'evento successivo è di tipo `Killing`, che indica l'eliminazione di un contenitore, quindi è possibile iniziare un riavvio. Il numero di riavvio per il contenitore viene incrementato ogni volta che si verifica questo evento.

I riavvii vengono completati sul posto, quindi le risorse come gli indirizzi IP pubblici e il contenuto specifico del nodo vengono mantenute.

![Contatore di riavvio nel portale][portal-restart]

Se il probe di vita continua a non riuscire e attiva un numero eccessivo di riavvii, il contenitore immette un ritardo esponenziale.

## <a name="liveness-probes-and-restart-policies"></a>Probe di attività e criteri di riavvio

I criteri di riavvio sostituiscono il comportamento di riavvio attivato dai probe di attività. Se, ad esempio, si impostano un oggetto `restartPolicy = Never` *e* un probe di Livezza, il gruppo di contenitori non viene riavviato a causa di un controllo di stato non riuscito. Il gruppo di contenitori rispetta invece i criteri di riavvio del gruppo di contenitori `Never`di.

## <a name="next-steps"></a>Passaggi successivi

Per gli scenari basati su attività potrebbe essere necessario che un probe di attività abiliti i riavvii automatici se una funzione prerequisito non funziona correttamente. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [Eseguire attività in contenitori in Istanze di Azure Container](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
