---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483045"
---
Iniziare a usare il servizio Rilevamento anomalie creando una delle risorse di Azure seguenti.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Creare una risorsa di valutazione (si apre in una nuova scheda)</a>
    * Non è necessaria una sottoscrizione di Azure: 
    * è gratuita e valida sette giorni. Dopo aver eseguito l'iscrizione, una chiave di valutazione e un endpoint saranno disponibili sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Si tratta di un'ottima soluzione se si vuole provare Rilevamento anomalie, ma non si ha una sottoscrizione di Azure.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Creare una risorsa di Rilevamento anomalie (si apre in una nuova scheda)</a>:
    * disponibile tramite il portale di Azure fino a quando la risorsa non viene eliminata.
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
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Dopo aver aggiunto la variabile di ambiente, riavviare la finestra della console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modificare `.bash_profile` e aggiungere la variabile di ambiente:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.

***