---
title: Contenitori Docker-LUIS
titleSuffix: Azure Cognitive Services
description: Il contenitore LUIS carica un'app sottoposta a training o pubblicata in un contenitore Docker e fornisce l'accesso alle stime di query dagli endpoint dell'API del contenitore.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: 7de529c9478b78ff9dd042df90a061f0a96f5427
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316408"
---
# <a name="install-and-run-luis-docker-containers"></a>Installare ed eseguire i contenitori docker LUIS
 
Il contenitore Language Understanding (LUIS) carica il modello di Language Understanding sottoposto a training o pubblicato. Come [app Luis](https://www.luis.ai), il contenitore Docker fornisce l'accesso alle stime delle query dagli endpoint API del contenitore. È possibile raccogliere i log di query dal contenitore e caricarli nuovamente nell'app Language Understanding per migliorare l'accuratezza della stima dell'app.

Il video seguente illustra l'uso di questo contenitore.

[![Dimostrazione relativa ai contenitori per Servizi cognitivi](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire il contenitore LUIS, tenere presente i prerequisiti seguenti:

|Richiesto|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa `Cognitive Services` di Azure e file di [app in pacchetto](luis-how-to-start-new-app.md#export-app-for-containers) Luis |Per usare il contenitore, è necessario disporre di:<br><br>* Una risorsa di Azure _Servizi cognitivi_ e la chiave di fatturazione associata l'URI dell'endpoint di fatturazione. Entrambi i valori sono disponibili nelle pagine Panoramica e chiavi per la risorsa e sono necessari per avviare il contenitore. <br>* Un'app pubblicata o sottoposta a training inserita in un pacchetto come input montato per il contenitore con il relativo ID app associato. È possibile ottenere il file in pacchetto dal portale LUIS o dalle API di creazione. Se si sta ricevendo un'app in pacchetto LUIS dalle [API di creazione](#authoring-apis-for-package-file), sarà necessaria anche la _chiave di creazione_.<br><br>Questi requisiti vengono usati per passare gli argomenti della riga di comando per le variabili seguenti:<br><br>**{AUTHORING_KEY}** : questa chiave viene usata per ottenere il pacchetto dell'app dal servizio LUIS nel cloud e caricare i log di query nel cloud. Il formato è `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : questo ID viene usato per selezionare l'app. Il formato è `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}** : Questa chiave viene usata per avviare il contenitore. È possibile trovare la chiave dell'endpoint in due posizioni. Il primo è la portale di Azure all'interno dell'elenco chiavi della risorsa _Servizi cognitivi_ . La chiave dell'endpoint è anche disponibile nel portale di LUIS nella pagina relativa a impostazioni dell'endpoint e chiavi. Non usare la chiave di avvio.<br><br>**{ENDPOINT_URI}** : Endpoint fornito nella pagina Overview.<br><br>La [chiave di creazione e la chiave dell'endpoint](luis-boundaries.md#key-limits) hanno scopi diversi. Non usarle in modo intercambiabile. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Creazione di API per il file di pacchetto

API di creazione per le app in pacchetto:

* [API del pacchetto pubblicato](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [API pacchetto non pubblicato e con training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

Il contenitore supporta i valori minimi e consigliati seguenti per le impostazioni:

|Contenitore| Minima | Consigliato | TPS<br>(Minimo, massimo)|
|-----------|---------|-------------|--|
|LUIS|1 core, 2 GB di memoria|1 core, 4 GB di memoria|20, 40|

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.
* TPS - transazioni al secondo

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Usare il comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal repository `mcr.microsoft.com/azure-cognitive-services/luis`:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Usare il comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore.

Per una descrizione completa dei tag disponibili, ad esempio `latest` usato nel comando precedente, vedere [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) nel sito Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

![Procedura per l'uso del contenitore Language Understanding (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Esportare il pacchetto](#export-packaged-app-from-luis) per il contenitore dal portale di LUIS o tramite le API LUIS.
1. Spostare il file del pacchetto nella directory di **input** richiesta nel [computer host](#the-host-computer). Non rinominare, modificare, sovrascrivere o decomprimere il file del pacchetto LUIS.
1. [Eseguire il contenitore](##run-the-container-with-docker-run), con il _punto di montaggio di input_ e le impostazioni di fatturazione richiesti. Sono disponibili altri [esempi](luis-container-configuration.md#example-docker-run-commands) del comando `docker run`. 
1. [Eseguire query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 
1. Dopo aver completato le operazioni con il contenitore, [importare i log dell'endpoint](#import-the-endpoint-logs-for-active-learning) dal punto di montaggio di output nel portale di LUIS e [arrestare](#stop-the-container) il contenitore.
1. Usare la funzionalità di [apprendimento attivo](luis-how-to-review-endpoint-utterances.md) nella pagina **Review endpoint utterances** (Esamina espressioni endpoint) del portale di LUIS per migliorare l'app.

Non è possibile modificare l'app in esecuzione nel contenitore. Per modificare l'app nel contenitore, è necessario modificarla nel servizio LUIS usando il portale di [LUIS](https://www.luis.ai) oppure usando le [API di creazione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) LUIS. Eseguire quindi il training e/o la pubblicazione, scaricare un nuovo pacchetto ed eseguire nuovamente il contenitore.

L'app LUIS all'interno del contenitore non può essere esportata di nuovo nel servizio LUIS. È possibile caricare solo i log di query. 

## <a name="export-packaged-app-from-luis"></a>Esportare il pacchetto dell'app da LUIS

Il contenitore LUIS richiede un'app LUIS sottoposta a training o pubblicata per rispondere alle query di stima delle espressioni utente. Per ottenere l'app LUIS, usare l'API del pacchetto pubblicato o sottoposto a training. 

La posizione predefinita è la sottodirectory `input` in relazione alla posizione in cui viene eseguito il comando `docker run`.  

Posizionare il file del pacchetto in una directory e fare riferimento alla directory come punto di montaggio di input quando si esegue il contenitore Docker. 

### <a name="package-types"></a>Tipi di pacchetto

La directory di montaggio di input può contenere contemporaneamente le versioni di **produzione**, di **staging** e **sottoposta a training** dell'app. Tutti i pacchetti vengono montati. 

|Tipo di pacchetto|API endpoint di query|Disponibilità query|Formato nome file pacchetto|
|--|--|--|--|
|Con training|GET, POST|Solo contenitore|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Gestione temporanea|GET, POST|Azure e contenitore|`{APPLICATION_ID}_STAGING.gz`|
|Produzione|GET, POST|Azure e contenitore|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Non rinominare, modificare, sovrascrivere o decomprimere i file del pacchetto LUIS.

### <a name="packaging-prerequisites"></a>Prerequisiti per la creazione di pacchetti

Prima di creare un pacchetto di un'applicazione LUIS, è necessario quanto segue:

|Requisiti di creazione di pacchetti|Dettagli|
|--|--|
|Istanza di risorsa _Servizi cognitivi_ di Azure|Le aree supportate includono<br><br>Stati Uniti occidentali (`westus`)<br>Europa occidentale (`westeurope`)<br>Australia orientale (`australiaeast`)|
|App LUIS sottoposta a training o pubblicata|Senza [dipendenze non supportate](#unsupported-dependencies). |
|Accesso al file system del [computer host](#the-host-computer) |Il computer host deve consentire un [punto di montaggio di input](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Esportare il pacchetto dell'app dal portale di LUIS

Il [portale](https://www.luis.ai) di LUIS consente di esportare il pacchetto dell'app sottoposta a training o pubblicata. 

### <a name="export-published-apps-package-from-luis-portal"></a>Esportare il pacchetto dell'app pubblicata dal portale di LUIS

Il pacchetto dell'app pubblicata è disponibile nella pagina di elenco **My Apps** (App personali). 

1. Accedere al portale di [LUIS](https://www.luis.ai).
1. Selezionare la casella di controllo a sinistra del nome dell'app nell'elenco. 
1. Scegliere la voce **Export** (Esporta) sulla barra degli strumenti contestuale sopra l'elenco.
1. Selezionare **Export for container (GZIP)** (Esporta per contenitore - GZIP).
1. Selezionare **Production slot** (Slot di produzione) o **Staging slot** (Slot di staging) come ambiente.
1. Il pacchetto viene scaricato dal browser.

![Esportare il pacchetto pubblicato per il contenitore dal menu di esportazione della pagina dell'app](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Esportare il pacchetto dell'app sottoposta a training dal portale di LUIS

Il pacchetto dell'app sottoposta a training è disponibile nella pagina di elenco **Versions** (Versioni). 

1. Accedere al portale di [LUIS](https://www.luis.ai).
1. Selezionare l'app nell'elenco. 
1. Selezionare **Manage** (Gestisci) sulla barra di spostamento dell'app.
1. Selezionare **Versions** (Versioni) sulla barra di spostamento a sinistra.
1. Selezionare la casella di controllo a sinistra del nome della versione nell'elenco.
1. Scegliere la voce **Export** (Esporta) sulla barra degli strumenti contestuale sopra l'elenco.
1. Selezionare **Export for container (GZIP)** (Esporta per contenitore - GZIP).
1. Il pacchetto viene scaricato dal browser.

![Esportare il pacchetto sottoposto a training per il contenitore dal menu di esportazione della pagina delle versioni](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Esportare il pacchetto dell'app pubblicata dall'API

Usare il metodo dell'API REST seguente per creare il pacchetto di un'app LUIS già [pubblicata](luis-how-to-publish-app.md). Sostituire i segnaposto con i valori appropriati nella chiamata API, facendo riferimento alla tabella riportata sotto la specifica HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Segnaposto | Value |
|-------------|-------|
| **{APPLICATION_ID}** | ID applicazione dell'app LUIS pubblicata. |
| **{APPLICATION_ENVIRONMENT}** | Ambiente dell'app LUIS pubblicata. Usare uno dei valori seguenti:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Chiave di creazione dell'account LUIS per l'app LUIS pubblicata.<br/>È possibile ottenere la chiave di creazione nella pagina **User Settings** (Impostazioni utente) nel portale di LUIS. |
| **{AZURE_REGION}** | Area di Azure appropriata:<br/><br/>`westus` - Stati Uniti occidentali<br/>`westeurope` - Europa occidentale<br/>`australiaeast` - Australia orientale |

Per scaricare il pacchetto pubblicato, vedere la [documentazione dell'API qui][download-published-package]. Se il download è stato completato correttamente, la risposta è un file di pacchetto LUIS. Salvare il file nella posizione di archiviazione specificata per il punto di montaggio di input del contenitore. 

### <a name="export-trained-apps-package-from-api"></a>Esportare il pacchetto dell'app sottoposta a training dall'API

Usare il metodo dell'API REST seguente per creare il pacchetto di un'applicazione LUIS già [sottoposta a training](luis-how-to-train.md). Sostituire i segnaposto con i valori appropriati nella chiamata API, facendo riferimento alla tabella riportata sotto la specifica HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Segnaposto | Value |
|-------------|-------|
| **{APPLICATION_ID}** | ID applicazione dell'app LUIS sottoposta a training. |
| **{APPLICATION_VERSION}** | Versione dell'applicazione dell'app LUIS sottoposta a training. |
| **{AUTHORING_KEY}** | Chiave di creazione dell'account LUIS per l'app LUIS pubblicata.<br/>È possibile ottenere la chiave di creazione nella pagina **User Settings** (Impostazioni utente) nel portale di LUIS. |
| **{AZURE_REGION}** | Area di Azure appropriata:<br/><br/>`westus` - Stati Uniti occidentali<br/>`westeurope` - Europa occidentale<br/>`australiaeast` - Australia orientale |

Per scaricare il pacchetto sottoposto a training, fare riferimento alla [documentazione dell'API qui][download-trained-package]. Se il download è stato completato correttamente, la risposta è un file di pacchetto LUIS. Salvare il file nella posizione di archiviazione specificata per il punto di montaggio di input del contenitore. 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Per informazioni dettagliate su come ottenere i `{ENDPOINT_URI}` valori e `{API_KEY}` , vedere raccolta dei [parametri obbligatori](#gathering-required-parameters) .

Sono disponibili [esempi](luis-container-configuration.md#example-docker-run-commands) di comando `docker run`.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Questo esempio usa la directory `C:` dall'unità per evitare eventuali conflitti di autorizzazione in Windows. Se è necessario usare una directory specifica come directory di input, potrebbe essere necessario concedere l'autorizzazione per il servizio Docker. 
* Non modificare l'ordine degli argomenti a meno che non si abbia familiarità con i contenitori docker.
* Se si utilizza un sistema operativo diverso, utilizzare la console/terminale, la sintassi di cartella per i montaggi e il carattere di continuazione di riga corretti per il sistema. In questi esempi si presuppone una console di Windows con un `^`carattere di continuazione di riga. Poiché il contenitore è un sistema operativo Linux, il montaggio di destinazione usa una sintassi di cartella di tipo Linux.

Questo comando:

* Esegue un contenitore dall'immagine del contenitore LUIS
* Carica l'app LUIS dal montaggio di input in *C:\input*, che si trova nell'host contenitore
* Alloca due core CPU e 4 gigabyte (GB) di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Salva i log del contenitore e LUIS nel montaggio di output in *C:\output*, che si trova nell'host contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

Sono disponibili altri [esempi](luis-container-configuration.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).
> Il valore APIKEY è la **chiave** della pagina **delle risorse di Azure** nel portale Luis ed è disponibile anche nella pagina chiavi `Cognitive Services` di risorsa di Azure.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>API endpoint supportate dal contenitore

Con il contenitore sono disponibili entrambe le versioni V2 e [v3 (anteprima)](luis-migration-api-v3.md) dell'API. 

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. Gli endpoint per le app pubblicate (staging o produzione) hanno una route _diversa_ rispetto agli endpoint per le app sottoposte a training. 

Usare l'host, `http://localhost:5000`, per le API del contenitore. 

|Tipo di pacchetto|Metodo|Route|Parametri di query|
|--|--|--|--|
|Pubblicato|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>&staging<br>[&timezoneOffset]<br>[&verbose]<br>[&log]<br>|
|Con training|GET, POST|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]|

I parametri di query specificano la modalità e i contenuti restituiti nella risposta della query:

|Parametro di query|Type|Scopo|
|--|--|--|
|`q`|string|Espressione dell'utente.|
|`timezoneOffset`|number|Il parametro timezoneOffset consente di [modificare il fuso orario](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) usato dall'entità predefinita datetimeV2.|
|`verbose`|boolean|Se l'impostazione è true, restituisce tutte le finalità e i relativi punteggi. Il valore predefinito è false, che restituisce solo la finalità principale.|
|`staging`|boolean|Se l'impostazione è true, restituisce i risultati della query dall'ambiente di gestione temporanea. |
|`log`|boolean|Registra le query, che successivamente possono essere usate per l'[apprendimento attivo](luis-how-to-review-endpoint-utterances.md). Il valore predefinito è true.|

### <a name="query-published-app"></a>Eseguire query sull'app pubblicata

Ecco un esempio di comando CURL per eseguire query sul contenitore per un'app pubblicata:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Per eseguire query sull'**ambiente di gestione temporanea**, modificare il valore del parametro della stringa di query **staging** impostandolo su true: 

`staging=true`

### <a name="query-trained-app"></a>Eseguire query sull'app sottoposta a training

Ecco un esempio di comando CURL per eseguire query sul contenitore per un'app sottoposta a training: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Il nome della versione può essere composto da un massimo di 10 caratteri e deve contenere solo caratteri consentiti in un URL. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importare i log dell'endpoint per l'apprendimento attivo

Se viene specificato un montaggio di output per il contenitore Luis, i file di log delle query dell'app vengono salvati nella `{INSTANCE_ID}` directory di output, dove è l'ID del contenitore. Il log di query dell'app contiene la query, la risposta e i timestamp per ogni query di stima inviata al contenitore LUIS. 

Il percorso seguente indica la struttura di directory nidificata per i file di log del contenitore.
```
/output/luis/{INSTANCE_ID}/
```
 
Dal portale di LUIS selezionare l'app, quindi selezionare **Import endpoint logs** (Importa log endpoint) per caricare i log. 

![Importare i file di log del contenitore per l'apprendimento attivo](./media/luis-container-how-to/upload-endpoint-log-files.png)

Dopo il caricamento del log, [esaminare le espressioni dell'endpoint](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) nel portale di LUIS.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

Per arrestare il contenitore, nell'ambiente della riga di comando in cui è in esecuzione il contenitore premere **CTRL+C**.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](luis-container-configuration.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

Il contenitore LUIS Invia le informazioni di fatturazione ad Azure, usando una risorsa _Servizi cognitivi_ nell'account Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](luis-container-configuration.md).

## <a name="supported-dependencies-for-latest-container"></a>Dipendenze supportate `latest` per il contenitore

Il contenitore più recente, rilasciato alle 2019 Build, supporterà:

* Controllo ortografico Bing: richieste all'endpoint di stima della query con `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` i parametri della stringa di query. Per ulteriori informazioni, utilizzare l' [esercitazione controllo ortografico Bing V7](luis-tutorial-bing-spellcheck.md) . Se si usa questa funzionalità, il contenitore invia l'espressione alla risorsa Controllo ortografico Bing V7.
* [Nuovi domini predefiniti](luis-reference-prebuilt-domains.md): questi domini aziendali includono entità, espressioni di esempio e modelli. Estendere questi domini per uso personale. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>Dipendenze non supportate per `latest` il contenitore

Per [esportare per il contenitore](#export-packaged-app-from-luis), è necessario rimuovere le dipendenze non supportate dall'app Luis. Quando si tenta di esportare per il contenitore, il portale LUIS segnala le funzionalità non supportate che è necessario rimuovere.

È possibile usare un'applicazione LUIS se **non include** le dipendenze seguenti:

Configurazioni dell'app non supportate|Dettagli|
|--|--|
|Impostazioni cultura del contenitore non supportate| Olandese (nl-NL)<br>Giapponese (ja-JP)<br>Il tedesco è supportato solo con la [Tokenizer 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Entità non supportate per tutte le impostazioni cultura|Entità [KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) predefinita per tutte le impostazioni cultura|
|Entità non supportate per le impostazioni cultura Inglese (en-US)|Entità [GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) predefinite|
|Priming del riconoscimento vocale|Le dipendenze esterne non sono supportate nel contenitore.|
|Analisi del sentiment|Le dipendenze esterne non sono supportate nel contenitore.|

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire contenitori Language Understanding (LUIS). In sintesi:

* LUIS (Language Understanding) offre un contenitore Linux per Docker per le stime di query degli endpoint per le espressioni.
* Le immagini dei contenitori vengono scaricate da Registro Container Microsoft.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST per eseguire query sugli endpoint dei contenitori specificando l'URI dell'host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](luis-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* Per risolvere i problemi correlati alle funzionalità di LUIS, vedere [Risoluzione dei problemi](troubleshooting.md).
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip