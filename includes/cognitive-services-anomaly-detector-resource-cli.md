---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554706"
---
Iniziare a usare il servizio Rilevamento anomalie creando una delle risorse di Azure seguenti.

* Una [risorsa di valutazione](https://azure.microsoft.com/try/cognitive-services/#decision) (non richiede una sottoscrizione di Azure): 
    * è gratuita e valida sette giorni. Dopo aver eseguito l'iscrizione, una chiave di valutazione e un endpoint saranno disponibili sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Si tratta di un'ottima soluzione se si vuole provare Rilevamento anomalie, ma non si ha una sottoscrizione di Azure.

* [Risorsa Rilevamento anomalie](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector):
    * disponibile tramite il [portale di Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) fino a quando la risorsa non viene eliminata.
    * Usare il piano tariffario gratuito per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

### <a name="create-an-environment-variable"></a>Creare una variabile di ambiente

>[!NOTE]
> Gli endpoint per le risorse non di valutazione create dopo il 1° luglio 2019 usano il formato di sottodominio personalizzato riportato di seguito. Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usando la chiave e l'endpoint della risorsa creata, creare due variabili di ambiente per l'autenticazione:

* `ANOMALY_DETECTOR_KEY`: la chiave della risorsa per l'autenticazione delle richieste.
* `ANOMALY_DETECTOR_ENDPOINT`: l'endpoint della risorsa per l'invio delle richieste API. L'aspetto sarà simile al seguente: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Usare le istruzioni per il sistema operativo in uso.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

Dopo aver aggiunto la variabile di ambiente, riavviare la finestra della console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modificare `.bash_profile` e aggiungere la variabile di ambiente:

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.

***