---
title: Risoluzione dei problemi relativi a Istanze di contenitore di Azure
description: Informazioni su come risolvere i problemi relativi a Istanze di contenitore di Azure
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 062308622e3170a4eb8f75a96300f04f683a90e7
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820359"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Risolvere i problemi comuni in Istanze di contenitore di Azure

Questo articolo mostra come risolvere i problemi comuni per la gestione o la distribuzione di contenitori in Istanze di contenitore di Azure.

## <a name="naming-conventions"></a>Convenzioni di denominazione

Quando si definisce la specifica del contenitore, determinati parametri devono essere conformi a limitazioni di denominazione. Nella tabella seguente sono disponibili i requisiti specifici per le proprietà dei gruppi di contenitori. Per altre informazioni sulle convenzioni di denominazione di Azure, vedere [Regole di denominazione e restrizioni][azure-name-restrictions] nel Centro architettura di Azure.

| Scope | Length | Maiuscole/minuscole | Caratteri validi | Schema consigliato | Esempio |
| --- | --- | --- | --- | --- | --- | --- |
| Nome del gruppo di contenitori | 1-64 |Non fa distinzione tra maiuscole e minuscole |Carattere alfanumerico e trattino in un punto qualsiasi, tranne il primo o l'ultimo carattere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nome contenitore | 1-64 |Non fa distinzione tra maiuscole e minuscole |Carattere alfanumerico e trattino in un punto qualsiasi, tranne il primo o l'ultimo carattere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porte del contenitore | Tra 1 e 65535 |Integer |Numero intero compreso tra 1 e 65535 |`<port-number>` |`443` |
| Etichetta del nome DNS | 5-63 |Non fa distinzione tra maiuscole e minuscole |Carattere alfanumerico e trattino in un punto qualsiasi, tranne il primo o l'ultimo carattere |`<name>` |`frontend-site1` |
| Variabile di ambiente | 1-63 |Non fa distinzione tra maiuscole e minuscole |Carattere alfanumerico e carattere di sottolineatura '_' in un punto qualsiasi, tranne il primo o l'ultimo carattere |`<name>` |`MY_VARIABLE` |
| Nome del volume | 5-63 |Non fa distinzione tra maiuscole e minuscole |Lettere minuscole, numeri e trattini in un punto qualsiasi, tranne il primo o l'ultimo carattere. Non può contenere due trattini consecutivi. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Versione del sistema operativo dell'immagine non supportata

Se si specifica un'immagine non supportata da Istanze di contenitore di Azure, viene restituito un errore `OsVersionNotSupported`. L'errore è simile al seguente, dove `{0}` è il nome dell'immagine che si è tentato di distribuire:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Questo errore viene spesso rilevato durante la distribuzione di immagini Windows basate su una versione Canale semestrale di Windows. Ad esempio, le versioni Windows 1709 e 1803 sono versioni Canale semestrale di Windows e generano questo errore al momento della distribuzione.

Istanze di contenitore di Azure supporta le immagini di Windows che si basano solo sulle versioni Long-Term Servicing Channel (LTSC). Per attenuare questo problema durante la distribuzione di contenitori Windows, distribuire sempre immagini basate sul Long-Term Servicing Channel.

Per informazioni dettagliate sulle versioni Long-Term Servicing Channel e Canale semestrale di Windows, vedere [Panoramica del Canale semestrale di Windows Server][windows-sac-overview].

## <a name="unable-to-pull-image"></a>Non è possibile eseguire il pull dell'immagine

Se non è inizialmente in grado di eseguire il pull dell'immagine, Istanze di contenitore di Azure ripete il tentativo per un periodo di tempo. Se l'operazione di pull dell'immagine continua a non riuscire, Istanze di contenitore di Azure non esegue correttamente la distribuzione e potrebbe essere visualizzato un errore `Failed to pull image`.

Per risolvere questo problema, eliminare l'istanza di contenitore e ripetere la distribuzione. Verificare che l'immagine esista nel registro e che il nome dell'immagine sia stato digitato correttamente.

Se il pull dell'immagine non può essere eseguito, vengono visualizzati eventi simili al seguente nell'output di [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Il contenitore viene continuamente chiuso e riavviato (nessun processo a esecuzione prolungata)

I gruppi di contenitori vengono impostati automaticamente sul [criterio di riavvio](container-instances-restart-policy.md) **Always**, in modo che i contenitori nel gruppo di contenitori eseguano sempre il riavvio dopo il completamento dell'esecuzione. Potrebbe essere necessario impostare questa opzione su **OnFailure** oppure **Never** se si prevede di eseguire i contenitori basati su attività. Se si specifica **OnFailure** e si riscontra una situazione di riavvio continuo, potrebbe essere presente un problema con l'applicazione o lo script eseguito nel contenitore.

Durante l'esecuzione di gruppi di contenitori senza processi a esecuzione prolungata è probabile che si verifichino ripetute uscite e riavvii con le immagini, ad esempio Ubuntu o Alpine. La connessione tramite [EXEC](container-instances-exec.md) non funzionerà in quanto il contenitore non dispone di alcun processo che lo mantiene attivo. Per risolvere questo problema, includere un comando simile al seguente con la distribuzione del gruppo di contenitori per mantenere il contenitore in esecuzione.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image microsoft/windowsservercore:ltsc2016
 --command-line "ping -t localhost"
```

L'API Istanze di contenitore e il portale di Azure includono una proprietà `restartCount`. Per controllare il numero di riavvii di un contenitore, è possibile usare il comando [az container show][az-container-show] nell'interfaccia della riga di comando di Azure. Nell'output di esempio seguente (troncato per brevità) la proprietà `restartCount` è visualizzata alla fine dell'output.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> La maggior parte delle immagini di contenitore per le distribuzioni di Linux imposta una shell, ad esempio bash, come comando predefinito. Poiché una shell non è di per sé un servizio a esecuzione prolungata, questi contenitori vengono chiusi immediatamente e sono soggetti a un riavvio ciclico quando sono configurati in base al criterio di riavvio predefinito **Always**.

## <a name="container-takes-a-long-time-to-start"></a>L'avvio di un contenitore richiede molto tempo

I due principali fattori che contribuiscono al tempo di avvio di un contenitore in Istanze di contenitore di Azure sono:

* [Dimensioni dell'immagine](#image-size)
* [Posizione dell'immagine](#image-location)

Per le immagini Windows sono necessarie [altre considerazioni](#cached-windows-images).

### <a name="image-size"></a>Dimensioni dell'immagine

Se per avviare il contenitore è necessario molto tempo, ma alla fine l'operazione ha esito positivo, esaminare innanzitutto la dimensione dell'immagine del contenitore. Poiché Istanze di contenitore di Azure esegue il pull dell'immagine del contenitore su richiesta, il tempo di avvio indicato è direttamente correlato alla dimensione dell'immagine.

Per visualizzare la dimensione dell'immagine del contenitore, è possibile usare il comando `docker images` nell'interfaccia della riga di comando di Docker:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Il modo migliore per limitare le dimensioni delle immagini è quello di evitare che l'immagine finale contenga dati che non sono necessari in fase di esecuzione. A tale scopo è possibile eseguire [compilazioni in più fasi][docker-multi-stage-builds]. Le compilazioni di questo tipo consentono di assicurarsi che l'immagine finale contenga solo gli elementi necessari per l'applicazione, escludendo altro contenuto richiesto in fase di compilazione.

### <a name="image-location"></a>Posizione dell'immagine

Un altro modo per ridurre l'impatto del pull dell'immagine sul tempo di avvio del contenitore è quello di ospitare l'immagine del contenitore nel [Registro contenitori di Azure](/azure/container-registry/) nella stessa area in cui si intende distribuire le istanze del contenitore. Ciò consente di ridurre il percorso dell'immagine del contenitore attraverso la rete e quindi di limitare notevolmente il tempo di download.

### <a name="cached-windows-images"></a>Immagini di Windows memorizzate nella cache

Istanze di contenitore di Azure usa un meccanismo di memorizzazione nella cache per velocizzare il tempo di avvio dei contenitori per le immagini basate su determinate immagini Windows.

Per garantire il tempo di avvio dei contenitori Windows più veloce, usare una delle **tre più recenti** versioni delle **due immagini** seguenti come immagine di base:

* [Windows Server 2016][docker-hub-windows-core] (solo LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>La rete diventa disponibile lentamente per i contenitori Windows

Al momento della creazione iniziale, è possibile che per i contenitori Windows non sia presente la connettività in ingresso o in uscita per un intervallo massimo di 30 secondi (o più lungo in alcuni rari casi). Se per l'applicazione contenitore è necessaria una connessione Internet, aggiungere un ritardo e ripetere la logica per consentire di stabilire la connettività Internet in un intervallo di 30 secondi. Dopo l'installazione iniziale, le funzionalità di rete per i contenitori dovrebbero riprendere in modo appropriato.

## <a name="resource-not-available-error"></a>Errore di risorsa non disponibile

A causa del carico variabile delle risorse delle aree in Azure, quando si cerca di distribuire un'istanza di contenitore, potrebbe verificarsi l'errore seguente:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Questo errore indica che a causa di un carico elevato nell'area in cui si sta cercando di eseguire la distribuzione, le risorse specificate per il contenitore non possono essere al momento allocate. Usare uno o più dei passaggi seguenti per mitigare il problema.

* Verificare che le impostazioni di distribuzione del contenitore rientrino nei parametri definiti in [Quote e aree disponibili per Istanze di contenitore di Azure](container-instances-quotas.md#region-availability)
* Specificare impostazioni di memoria e CPU inferiori per il contenitore
* Eseguire la distribuzione in un'area di Azure diversa
* Eseguire la distribuzione in un secondo momento

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Non è possibile connettersi all'API Docker sottostante o eseguire contenitori con privilegi

Istanze di contenitore di Azure non espone l'accesso diretto all'infrastruttura sottostante che ospita i gruppi di contenitori. È incluso l'accesso all'API Docker in esecuzione nell'host del contenitore e che esegue contenitori con privilegi. Se è necessaria l'interazione con Docker, vedere la [documentazione di riferimento su REST](https://aka.ms/aci/rest) per vedere cosa è supportato dall'API di Istanze di contenitore di Azure. Se mancano informazioni, inviare una richiesta al [forum di commenti e suggerimenti per Istanze di contenitore di Azure](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>Gli indirizzi IP potrebbero non essere accessibili a causa di porte non corrispondenti
Istanze di Azure Container non supporta attualmente il mapping delle porte, come con la configurazione docker normale, tuttavia questa correzione è prevista nella roadmap. Se gli indirizzi IP risultano non accessibili quando si ritiene che dovrebbero esserlo, assicurarsi di aver configurato l'immagine del contenitore per l'ascolto sulle stesse porte esposte nel gruppo di contenitori con la proprietà `ports`.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [recuperare log ed eventi dei contenitori](container-instances-get-logs.md) per facilitare il debug dei contenitori.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
