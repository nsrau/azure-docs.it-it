---
title: Azure resources - QnA Maker
description: QnA Maker usa diverse origini di Azure, ognuna con uno scopo diverso. Comprendere come vengono utilizzati singolarmente consente di pianificare e selezionare il piano tariffario corretto o di sapere quando modificare il piano tariffario. Comprendere come vengono utilizzati in combinazione consente di trovare e risolvere i problemi quando si verificano.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 1bd491ecbd878cb7bb05a7eaa5712c75653f2cba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804300"
---
# <a name="azure-resources-for-qna-maker"></a>Azure resources for QnA Maker

QnA Maker usa diverse origini di Azure, ognuna con uno scopo diverso. Comprendere come vengono utilizzati singolarmente consente di pianificare e selezionare il piano tariffario corretto o di sapere quando modificare il piano tariffario. Comprendere come vengono utilizzati _in combinazione_ consente di trovare e risolvere i problemi quando si verificano.

## <a name="resource-planning"></a>Pianificazione delle risorse

Quando si sviluppa per la prima volta una knowledge base Di QnA Maker, nella fase di prototipo, è comune avere una singola risorsa QnA Maker sia per il test che per la produzione.

Quando si passa alla fase di sviluppo del progetto, è necessario considerare:

* quante lingue il sistema di Knowledge Base conterrà
* quante regioni hai bisogno che la tua knowledge base sia disponibile da/verso
* quanti documenti in ogni dominio il sistema conterrà

Pianificare la disponibilità di una singola risorsa QnA Maker che contenga tutte le Knowledge Base con la stessa lingua, la stessa area geografica e la stessa combinazione di dominio soggetto.

## <a name="pricing-tier-considerations"></a>Considerazioni sul livello di determinazione dei prezzi

Ci sono generalmente tre parametri da considerare:

* **La velocità effettiva che il servizio deve fornire**:
    * scegliere il [piano app](https://azure.microsoft.com/pricing/details/app-service/plans/) appropriato per il servizio app in base alle proprie esigenze. È possibile [aumentare](https://docs.microsoft.com/azure/app-service/manage-scale-up) o ridurre le prestazioni dell'app.
    * Questo dovrebbe influenzare anche la selezione SKU **di Ricerca cognitiva** di Azure, vedere altri dettagli [qui](https://docs.microsoft.com/azure/search/search-sku-tier). Inoltre, potrebbe essere necessario regolare la [capacità](../../../search/search-capacity-planning.md) di ricerca cognitiva con le repliche.

* **Dimensioni e numero di Knowledge Base**: scegliere lo [SKU di Ricerca di Azure](https://azure.microsoft.com/pricing/details/search/) appropriato per lo scenario. In genere, si decide il numero di knowledge base necessarie in base al numero di domini oggetto diversi. Una volta che il dominio dell'oggetto (per una singola lingua) deve essere in una Knowledge Base.

    È possibile pubblicare N-1 Knowledge Base in un particolare livello, dove N è il numero massimo di indici consentiti nel livello. Verificare anche le dimensioni massime e il numero di documenti consentiti per ogni livello.

    Ad esempio, se il livello include 15 indici consentiti, è possibile pubblicare 14 articoli della knowledge base (1 indice per ogni articolo della knowledge base pubblicato). Il quindicesimo indice viene usato per tutti gli articoli della knowledge base per la creazione e il testing.

* **Numero di documenti come origini**: lo SKU gratuito del servizio di gestione di QnA Maker limita a 3 (1 MB ciascuno) il numero di documenti gestibili tramite il portale e le API. Lo SKU Standard non pone limiti al numero di documenti gestibili. Per informazioni dettagliate, vedere [qui](https://aka.ms/qnamaker-pricing).

La tabella seguente indica alcune linee guida generali.

|                        | Gestione di QnA Maker | Servizio app | Ricerca cognitiva di Azure | Limitazioni                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Sperimentazione        | SKU gratuito             | Livello gratuito   | Livello gratuito    | Pubblicazione di massimo 2 Knowledge Base, dimensioni 50 MB  |
| Ambiente di sviluppo/test   | SKU Standard         | Condiviso      | Basic        | Pubblicazione di massimo 14 Knowledge Base, dimensioni 2 GB    |
| Ambiente di produzione | SKU Standard         | Basic       | Standard     | Pubblicazione di massimo 49 Knowledge Base, dimensioni 25 GB |

## <a name="when-to-change-a-pricing-tier"></a>Quando modificare un piano tariffarioWhen to change a pricing tier

|Aggiornamento|Motivo|
|--|--|
|[Aggiornamento](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) SKU di gestione di QnA Maker|Si desidera avere più coppie QnA o origini documento nella Knowledge Base.|
|[Aggiornamento](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) SKU del servizio app e controllare il livello Ricerca cognitiva e [creare repliche di Ricerca cognitiva](../../../search/search-capacity-planning.md)|La Knowledge Base deve soddisfare più richieste dall'app client, ad esempio un bot di chat.|
|[Aggiornamento](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Servizio Ricerca cognitiva di AzureAzure Cognitive Search service|Si prevede di avere molte basi di conoscenza.|

Ottenere gli aggiornamenti di runtime più recenti [aggiornando il servizio app nel portale](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)di Azure.

## <a name="resource-naming-considerations"></a>Considerazioni sulla denominazione delle risorse

Il nome della risorsa QnA Maker, ad `qna-westus-f0-b`esempio , viene utilizzato anche per denominare le altre risorse.

La finestra di creazione del portale di Azure consente di creare una risorsa QnA Maker e selezionare i piani tariffari per le altre risorse.

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure per la creazione di risorse QnA Maker](../media/concept-azure-resource/create-blade.png)

Dopo la creazione, le risorse hanno lo stesso nome, ad eccezione della risorsa facoltativa di Application Insights, che invia i caratteri al nome.

> [!div class="mx-imgBorder"]
> ![Screenshot dell'elenco delle risorse del portale di Azure](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Creare un nuovo gruppo di risorse quando si crea una risorsa QnA Maker.Create a new resource group when you create a QnA Maker resource. Ciò consente di visualizzare tutte le risorse associate alla risorsa QnA Maker durante la ricerca in base al gruppo di risorse.

> [!TIP]
> Usare una convenzione di denominazione per indicare i livelli dei prezzi all'interno del nome della risorsa o del gruppo di risorse. Quando si ricevono errori dalla creazione di una nuova Knowledge Base o dall'aggiunta di nuovi documenti, il limite del livello dei prezzi ricerca cognitiva è un problema comune.

## <a name="resource-purposes"></a>Scopi delle risorse

Ogni risorsa di Azure creata con QnA Maker ha uno scopo specifico:Each Azure resource created with QnA Maker has a specific purpose:

* Risorsa QnA Maker
* Risorsa Ricerca cognitiva
* Servizio app
* Servizio piano app
* Servizio Application Insights


### <a name="cognitive-search-resource"></a>Risorsa Ricerca cognitiva

La risorsa [Ricerca cognitiva](../../../search/index.yml) viene utilizzata per:

* Memorizzare le coppie QnA
* Fornire la classificazione iniziale (#1 più bile) delle coppie QnA in fase di esecuzioneProvide the initial ranking (ranker #1) of the QnA pairs at runtime

#### <a name="index-usage"></a>Utilizzo dell'indice

La risorsa mantiene un indice che funga da indice di test e gli indici rimanenti sono correlati a una Knowledge Base pubblicata ciascuno.

Una risorsa con un prezzo di 15 indici, conterrà 14 knowledge base pubblicate e un indice viene utilizzato per testare tutte le knowledge base. Questo indice di test viene partizionato in base alla Knowledge Base in modo che una query che utilizza il riquadro di test interattivo utilizzi l'indice di test ma restituisca solo i risultati della partizione specifica associata alla Knowledge Base specifica.

#### <a name="language-usage"></a>Utilizzo della lingua

La prima Knowledge Base creata nella risorsa QnA Maker viene utilizzata per determinare la _singola_ lingua impostata per la risorsa Ricerca cognitiva e tutti i relativi indici. È possibile _impostare una_ sola lingua per un servizio QnA Maker.

### <a name="qna-maker-resource"></a>Risorsa QnA Maker

La risorsa QnA Maker fornisce l'accesso alle API di creazione e pubblicazione, nonché al secondo livello di classificazione basato su NLP (Natural Language Processing) in base al livello di classificazione (#2) delle coppie QnA in fase di esecuzione.

La seconda classificazione applica filtri intelligenti che possono includere metadati e prompt di completamento.

#### <a name="qna-maker-resource-configuration-settings"></a>Impostazioni di configurazione delle risorse QnA Maker

Quando si crea una nuova Knowledge Base nel [portale QnA Maker](https://qnamaker.ai), l'impostazione **Lingua** è l'unica impostazione applicata a livello di risorsa. Selezionare la lingua quando si crea la prima Knowledge Base per la risorsa.

### <a name="app-service-and-app-service-plan"></a>Servizio app e piano di servizio app

Il [servizio app](../../../app-service/index.yml) viene usato dall'applicazione client per accedere alle Knowledge Base pubblicate tramite l'endpoint di runtime.

Per eseguire una query sulla Knowledge Base pubblicata, tutte le Knowledge Base pubblicate utilizzano lo stesso endpoint URL, ma specificano **l'ID della Knowledge Base** all'interno della route.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) viene usato per raccogliere log di chat e dati di telemetria. Esaminare le [query Kusto](../how-to/get-analytics-knowledge-base.md) comuni per informazioni sul servizio.

## <a name="share-services-with-qna-maker"></a>Condividere i servizi con QnA Maker

QnA Maker crea diverse risorse di Azure.QnA Maker creates several Azure resources. Per ridurre la gestione e trarre vantaggio dalla condivisione dei costi, utilizzare la tabella seguente per comprendere cosa è possibile o non è possibile condividere:

|Service|Condividi|Motivo|
|--|--|--|
|Servizi cognitivi|X|Non possibile per progettazione|
|Piano di servizio app|✔|Spazio su disco fisso allocato per un piano di servizio app. Se altre app che condividono lo stesso piano di servizio app utilizzano uno spazio su disco significativo, l'istanza del servizio app QnAMaker incontrerà problemi.|
|Servizio app|X|Non possibile per progettazione|
|Application Insights|✔|Può essere condivisa|
|Servizio di ricerca|✔|1. `testkb` è un nome riservato per il servizio QnAMaker; non può essere utilizzato da altri.<br>2. La mappa dei sinonimi con il nome `synonym-map` è riservata al servizio QnAMaker.<br>3. Il numero di Knowledge Base pubblicate è limitato dal livello del servizio di ricerca. Se sono disponibili indici gratuiti, altri servizi possono utilizzarli.|

### <a name="using-a-single-cognitive-search-service"></a>Utilizzo di un singolo servizio di ricerca cognitivaUsing a single Cognitive Search service

Se si crea un servizio QnA e le relative dipendenze (ad esempio ricerca) tramite il portale, viene creato automaticamente un servizio di ricerca e collegato al servizio QnA Maker. Dopo aver creato queste risorse, è possibile aggiornare l'impostazione del servizio app per usare un servizio di ricerca esistente in precedenza e rimuovere quella appena creata.

Informazioni su [come configurare](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker per l'utilizzo di una risorsa del servizio cognitivo diversa da quella creata nell'ambito del processo di creazione delle risorse QnA Maker.

## <a name="management-service-region"></a>Area del servizio di gestione

Il servizio di gestione di QnA Maker viene utilizzato solo per il portale QnA Maker e per l'elaborazione iniziale dei dati. Questo servizio è disponibile solo nella regione **degli Stati Uniti occidentali.** Nessun dato dei clienti è memorizzato in questo servizio west US.

## <a name="keys-in-qna-maker"></a>Tasti in QnA Maker

Il servizio QnA Maker gestisce due tipi di chiavi: **le chiavi** di creazione e le **chiavi dell'endpoint di query** usate con il runtime ospitato nel servizio app.

Se si sta cercando la chiave di **sottoscrizione,** [la terminologia è cambiata.](#subscription-keys)

Usare queste chiavi quando si effettuano richieste al servizio tramite le API.

![Gestione delle chiavi](../media/qnamaker-how-to-key-management/key-management.png)

|Nome|Location|Scopo|
|--|--|--|
|Chiave di creazione|[Portale di Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|queste chiavi vengono usate per accedere alle [API del servizio di gestione di QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Queste API consentono di modificare le domande e le risposte nella Knowledge Base e di pubblicare la Knowledge Base. Queste chiavi vengono create quando si crea un nuovo servizio QnA Maker.These keys are created when you create a new QnA Maker service.<br><br>Trovare queste chiavi nella risorsa **Servizi cognitivi** nella pagina **Chiavi.Find** these keys on the Cognitive Services resource on the Keys page.|
|Chiave dell'endpoint di query|[Portale di QnA Maker](https://www.qnamaker.ai)|Queste chiavi vengono usate per eseguire una query sull'endpoint della Knowledge Base pubblicato per ottenere una risposta per una domanda dell'utente. Questo endpoint di query viene in genere usato nel bot di chat o nel codice dell'applicazione client che si connette al servizio QnA Maker. Queste chiavi vengono create quando si pubblica la Knowledge Base di QnA Maker.<br><br>Trova queste chiavi nella pagina **Impostazioni del servizio.** Trova questa pagina dal menu dell'utente in alto a destra della pagina nel menu a discesa.|

### <a name="subscription-keys"></a>Chiavi di sottoscrizione

I termini di creazione e chiave dell'endpoint di query sono termini correttivi. Il termine precedente era **chiave di sottoscrizione**. Se viene visualizzata altra documentazione che fa riferimento alle chiavi di sottoscrizione, queste sono equivalenti alla creazione e alla query delle chiavi dell'endpoint (usate nel runtime).

È necessario sapere qual è la chiave di accesso, gestione della Knowledge Base o query della Knowledge Base, per sapere quale chiave è necessario trovare.

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulla [Knowledge Base](knowledge-base.md) di QnA Maker
* Comprendere un [ciclo di vita della knowledge base](development-lifecycle-knowledge-base.md)
* Esaminare i [limiti](../limits.md) del servizio e della Knowledge BaseReview service and knowledge base limits

