---
title: Configurare probe di attività in Istanze di Azure Container
description: Informazioni su come configurare probe di attività per riavviare i contenitori non integri in Istanze di Azure Container
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: juluk
ms.openlocfilehash: 1582f0d7ec688bc72cc9d1aa6ae0ddb0a6ad3a17
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213072"
---
# <a name="configure-liveness-probes"></a>Configurare probe di attività

Le applicazioni nei contenitori possono essere eseguite per periodi prolungati di tempo e ciò può causare stati interrotti che potrebbe risultare necessario ripristinare riavviando il contenitore. Istanze di Azure Container supporta l'inclusione di configurazioni nei probe di attività, in modo che il contenitore possa essere riavviato se una funzionalità cruciale non è disponibile.

Questo articolo illustra come distribuire un gruppo di contenitori che include un probe di attività, per dimostrare il riavvio automatico di un contenitore non integro simulato.

## <a name="yaml-deployment"></a>Distribuzione con file YAML

Creare un file `liveness-probe.yaml` con il frammento seguente. Questo file definisce un gruppo di contenitori costituito da un contenitore NGNIX che diventa non integro.

```yaml
apiVersion: 2018-06-01
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

La distribuzione definisce un comando di avvio da eseguire al primo avvio dell'esecuzione del contenitore, definito dalla proprietà `command` che accetta una matrice di stringhe. In questo esempio, verrà avviata una sessione bash e verrà creato un file denominato `healthy` all'interno della directory `/tmp` passando questo comando:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 L'operazione viene quindi sospesa per 30 secondi prima di eliminare il file, poi viene attivata una sospensione di 10 minuti.

### <a name="liveness-command"></a>Comando di verifica dell'attività (liveness)

Questa distribuzione definisce un `livenessProbe` che supporta un comando di verifica dell'attività `exec` usato come controllo di attività. Se questo comando termina con un valore diverso da zero, il contenitore verrà terminato e riavviato, a indicare che non è stato trovato il file `healthy`. Se questo comando termina correttamente con il codice di uscita 0, non verrà eseguita alcuna azione.

La proprietà `periodSeconds` stabilisce che il comando di verifica dell'attività deve essere eseguito ogni 5 secondi.

## <a name="verify-liveness-output"></a>Verificare l'output di attività

Entro i primi 30 secondi deve essere confermata l'esistenza del file `healthy` creato dal comando di avvio. Quando il comando di verifica dell'attività controlla l'esistenza del file `healthy`, il codice di stato restituisce zero, a indicare l'esito positivo, quindi non si verifica alcun riavvio.

Dopo 30 secondi, inizieranno gli errori per `cat /tmp/healthy`, con conseguente generazione degli eventi per lo stato non integro e la terminazione.

Questi eventi possono essere visualizzati dal portale di Azure o dall'interfaccia della riga di comando di Azure.

![Evento di non integrità nel portale][portal-unhealthy]

Quando si visualizzano gli eventi nel portale di Azure, gli eventi di tipo `Unhealthy` verranno attivati in caso di errore del comando liveness. L'evento successivo sarà di tipo `Killing`, che significa l'eliminazione di un contenitore, quindi può iniziare un riavvio. Il conteggio di riavvio per il contenitore verrà incrementato ogni volta che si verifica.

I riavvi vengono completati sul posto, in modo da mantenere risorse come gli indirizzi IP pubblici e il contenuto specifico del nodo.

![Contatore di riavvio nel portale][portal-restart]

Se il probe di attività ha continuamente esito negativo e genera troppi riavvii, per il contenitore subentrerà un ritardo di backoff esponenziale.

## <a name="liveness-probes-and-restart-policies"></a>Probe di attività e criteri di riavvio

I criteri di riavvio sostituiscono il comportamento di riavvio attivato dai probe di attività. Ad esempio, se si impostano i criteri `restartPolicy = Never` *e* un probe di attività, il gruppo di contenitori non verrà riavviato nel caso di un controllo di attività non riuscito. Il gruppo di contenitori dovrà invece rispettare i criteri di riavvio `Never` del gruppo di contenitori.

## <a name="next-steps"></a>Passaggi successivi

Per gli scenari basati su attività potrebbe essere necessario che un probe di attività abiliti i riavvii automatici se una funzione prerequisito non funziona correttamente. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [Eseguire attività in contenitori in Istanze di Azure Container](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
