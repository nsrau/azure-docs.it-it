---
title: Risorse di Azure-QnA Maker
description: QnA Maker usa diverse origini di Azure, ognuna con uno scopo diverso. Per comprendere il modo in cui vengono usati singolarmente, è possibile pianificare e selezionare il piano tariffario corretto o sapere quando modificare il piano tariffario. La comprensione del modo in cui vengono usate in combinazione consente di individuare e risolvere i problemi quando si verificano.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 581029d2372f7a2ef704dcf02f266b66440aa246
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80873906"
---
# <a name="azure-resources-for-qna-maker"></a>Risorse di Azure per QnA Maker

QnA Maker usa diverse origini di Azure, ognuna con uno scopo diverso. Per comprendere il modo in cui vengono usati singolarmente, è possibile pianificare e selezionare il piano tariffario corretto o sapere quando modificare il piano tariffario. La comprensione del modo in cui vengono usate _in combinazione_ consente di individuare e risolvere i problemi quando si verificano.

## <a name="resource-planning"></a>Pianificazione delle risorse

Quando si sviluppa una QnA Maker Knowledge base nella fase di prototipo, è comune disporre di una singola risorsa QnA Maker per i test e la produzione.

Quando si passa alla fase di sviluppo del progetto, è consigliabile prendere in considerazione quanto segue:

* il numero di linguaggi che il sistema Knowledge base terrà in attesa
* il numero di aree in cui è necessario che la Knowledge base sia disponibile in/da
* il numero di documenti in ogni dominio che il sistema conterrà

Pianificare un'unica risorsa QnA Maker contenere tutte le Knowledge base con lo stesso linguaggio, la stessa area e la stessa combinazione di dominio soggetto.

## <a name="pricing-tier-considerations"></a>Considerazioni sui piani tariffari

Ci sono generalmente tre parametri da considerare:

* **La velocità effettiva che il servizio deve fornire**:
    * scegliere il [piano app](https://azure.microsoft.com/pricing/details/app-service/plans/) appropriato per il servizio app in base alle proprie esigenze. È possibile [aumentare](https://docs.microsoft.com/azure/app-service/manage-scale-up) o ridurre le prestazioni dell'app.
    * Questo dovrebbe influire anche sulla selezione dello SKU **ricerca cognitiva** di Azure. per altre informazioni, vedere [qui](https://docs.microsoft.com/azure/search/search-sku-tier). Inoltre, potrebbe essere necessario regolare ricerca cognitiva [capacità](../../../search/search-capacity-planning.md) con le repliche.

* **Dimensioni e numero di Knowledge Base**: scegliere lo [SKU di Ricerca di Azure](https://azure.microsoft.com/pricing/details/search/) appropriato per lo scenario. In genere, si decide il numero di Knowledge base necessarie in base al numero di domini soggetto diversi. Una volta che il dominio dell'oggetto (per un singolo linguaggio) deve trovarsi in una Knowledge base.

    È possibile pubblicare N-1 Knowledge Base in un particolare livello, dove N è il numero massimo di indici consentiti nel livello. Verificare anche le dimensioni massime e il numero di documenti consentiti per ogni livello.

    Ad esempio, se il livello include 15 indici consentiti, è possibile pubblicare 14 articoli della knowledge base (1 indice per ogni articolo della knowledge base pubblicato). Il quindicesimo indice viene usato per tutti gli articoli della knowledge base per la creazione e il testing.

* **Numero di documenti come origini**: lo SKU gratuito del servizio di gestione di QnA Maker limita a 3 (1 MB ciascuno) il numero di documenti gestibili tramite il portale e le API. Lo SKU Standard non pone limiti al numero di documenti gestibili. Per informazioni dettagliate, vedere [qui](https://aka.ms/qnamaker-pricing).

La tabella seguente indica alcune linee guida generali.

|                        | Gestione di QnA Maker | Servizio app | Ricerca cognitiva di Azure | Limitazioni                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Sperimentazione        | SKU gratuito             | Livello gratuito   | Livello gratuito    | Pubblicazione di massimo 2 Knowledge Base, dimensioni 50 MB  |
| Ambiente di sviluppo/test   | SKU Standard         | Shared      | Basic        | Pubblicazione di massimo 14 Knowledge Base, dimensioni 2 GB    |
| Ambiente di produzione | SKU Standard         | Basic       | Standard     | Pubblicazione di massimo 49 Knowledge Base, dimensioni 25 GB |

## <a name="recommended-settings"></a>Impostazioni consigliate

|QUERY al secondo di destinazione | Servizio app | Ricerca cognitiva di Azure |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 istanza   | S1, 1 istanza    |
| 50         | S3, 10 istanze       | S1, 12 istanze         |
| 80         | S3, 10 istanze      |  S3, 12 istanze  |
| 100         | P3V2, 10 istanze  | S3, 12 istanze, 3 partizioni   |
| da 200 a 250         | P3V2, 20 istanze | S3, 12 istanze, 3 partizioni    |

## <a name="when-to-change-a-pricing-tier"></a>Quando modificare un piano tariffario

|Aggiornamento|Motivo|
|--|--|
|[Aggiornamento](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) di SKU di gestione QnA Maker|Si desidera avere più coppie di QnA o origini documenti nella Knowledge base.|
|[Aggiornamento](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) di SKU del servizio app e controllare ricerca cognitiva livello e [creare repliche ricerca cognitiva](../../../search/search-capacity-planning.md)|La Knowledge base deve soddisfare un numero maggiore di richieste provenienti dall'app client, ad esempio un bot di chat.|
|[Aggiornamento](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) di Servizio ricerca cognitiva di Azure|Si prevede di avere molte Knowledge base.|

Per ottenere gli aggiornamenti più recenti del runtime, [aggiornare il servizio app nel portale di Azure](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Considerazioni sulla denominazione delle risorse

Il nome della risorsa per la risorsa QnA Maker, ad `qna-westus-f0-b`esempio, viene usato anche per assegnare un nome alle altre risorse.

La finestra portale di Azure crea consente di creare una risorsa QnA Maker e selezionare i piani tariffari per le altre risorse.

> [!div class="mx-imgBorder"]
> ![Screenshot di portale di Azure per la creazione di risorse QnA Maker](../media/concept-azure-resource/create-blade.png)

Una volta create, le risorse hanno lo stesso nome, ad eccezione della risorsa Application Insights facoltativa, che antepone i caratteri al nome.

> [!div class="mx-imgBorder"]
> ![Screenshot dell'elenco di portale di Azure risorse](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Creare un nuovo gruppo di risorse quando si crea una risorsa QnA Maker. Che consente di visualizzare tutte le risorse associate alla risorsa QnA Maker durante la ricerca in base al gruppo di risorse.

> [!TIP]
> Usare una convenzione di denominazione per indicare i piani tariffari nel nome della risorsa o del gruppo di risorse. Quando si ricevono errori dalla creazione di una nuova Knowledge base o dall'aggiunta di nuovi documenti, il ricerca cognitiva limite del piano tariffario è un problema comune.

## <a name="resource-purposes"></a>Finalità delle risorse

Ogni risorsa di Azure creata con QnA Maker ha uno scopo specifico:

* Risorsa QnA Maker
* Risorsa ricerca cognitiva
* Servizio app
* Servizio del piano app
* Servizio Application Insights


### <a name="cognitive-search-resource"></a>Risorsa ricerca cognitiva

La risorsa [ricerca cognitiva](../../../search/index.yml) viene utilizzata per:

* Archiviare le coppie QnA
* Fornire la classificazione iniziale (Ranker #1) delle coppie QnA in fase di esecuzione

#### <a name="index-usage"></a>Utilizzo indici

La risorsa mantiene un indice che funga da indice di test e gli indici rimanenti sono correlati a una Knowledge base pubblicata ciascuna.

Una risorsa con prezzi per la presenza di 15 indici, conterrà 14 Knowledge base pubblicate e un indice viene utilizzato per il test di tutte le Knowledge base. Questo indice di test viene partizionato in base alla Knowledge base, in modo che una query che utilizza il riquadro test interattivo utilizzerà l'indice di test, ma restituirà solo i risultati della partizione specifica associata alla Knowledge base specifica.

#### <a name="language-usage"></a>Utilizzo della lingua

La prima Knowledge base creata nella risorsa QnA Maker viene utilizzata per determinare il _singolo_ set di lingue per la risorsa ricerca cognitiva e tutti i relativi indici. È possibile _impostare una sola lingua_ per un servizio QnA Maker.

### <a name="qna-maker-resource"></a>Risorsa QnA Maker

La risorsa QnA Maker fornisce l'accesso alle API di creazione e pubblicazione, nonché al secondo livello di classificazione (PNL) basato sul linguaggio naturale di elaborazione del linguaggio naturale (Ranker #2) delle coppie di QnA in fase di esecuzione.

Il secondo rango applica filtri intelligenti che possono includere metadati e richieste di completamento.

#### <a name="qna-maker-resource-configuration-settings"></a>Impostazioni di configurazione della risorsa QnA Maker

Quando si crea una nuova Knowledge base nel [portale di QnA Maker](https://qnamaker.ai), l'impostazione della **lingua** è l'unica che viene applicata a livello di risorsa. Selezionare la lingua quando si crea la prima Knowledge base per la risorsa.

### <a name="app-service-and-app-service-plan"></a>Servizio app e piano di servizio app

Il [servizio app](../../../app-service/index.yml) viene usato dall'applicazione client per accedere alle Knowledge base pubblicate tramite l'endpoint di Runtime.

Per eseguire una query sulla Knowledge base pubblicata, tutte le Knowledge base pubblicate utilizzano lo stesso endpoint URL, ma specificano l' **ID Knowledge base** nella route.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) viene usato per raccogliere i log di chat e i dati di telemetria. Esaminare le [query kusto](../how-to/get-analytics-knowledge-base.md) comuni per informazioni sul servizio.

## <a name="share-services-with-qna-maker"></a>Condividere i servizi con QnA Maker

QnA Maker crea diverse risorse di Azure. Per ridurre la gestione e trarre vantaggio dalla condivisione dei costi, usare la tabella seguente per comprendere cosa è possibile e non è possibile condividere:

|Servizio|Condividi|Motivo|
|--|--|--|
|Servizi cognitivi|X|Non possibile dalla progettazione|
|Piano di servizio app|✔|Spazio su disco fisso allocato per un piano di servizio app. Se altre app che condividono lo stesso piano di servizio app usano spazio su disco significativo, si verificheranno problemi nell'istanza del servizio app QnAMaker.|
|Servizio app|X|Non possibile dalla progettazione|
|Application Insights|✔|Può essere condivisa|
|Servizio di ricerca|✔|1. `testkb` è un nome riservato per il servizio QnAMaker. non può essere usato da altri.<br>2. il mapping dei sinonimi `synonym-map` con il nome è riservato per il servizio QnAMaker.<br>3. il numero di Knowledge base pubblicate è limitato dal livello di servizio di ricerca. Se sono disponibili indici gratuiti, possono essere usati da altri servizi.|

### <a name="using-a-single-cognitive-search-service"></a>Uso di un singolo servizio ricerca cognitiva

Se si crea un servizio QnA e le relative dipendenze (ad esempio la ricerca) tramite il portale, viene creato un servizio di ricerca e collegato al servizio QnA Maker. Dopo aver creato queste risorse, è possibile aggiornare l'impostazione del servizio app per usare un servizio di ricerca precedentemente esistente e rimuovere quello appena creato.

Informazioni [su come configurare](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker per l'uso di una risorsa di servizi cognitivi diversa da quella creata come parte del processo di creazione delle risorse QnA Maker.

## <a name="management-service-region"></a>Area del servizio di gestione

Il servizio di gestione di QnA Maker viene usato solo per il portale di QnA Maker e per l'elaborazione iniziale dei dati. Questo servizio è disponibile solo nell'area **Stati Uniti occidentali** . Nessun dato cliente è archiviato in questo servizio Stati Uniti occidentali.

## <a name="keys-in-qna-maker"></a>Chiavi in QnA Maker

Il servizio QnA Maker gestisce due tipi di chiavi: la **creazione** di chiavi e le **chiavi dell'endpoint di query** usate con il runtime ospitato nel servizio app.

Se si sta cercando la **chiave della sottoscrizione**, [la terminologia è cambiata](#subscription-keys).

Usare queste chiavi quando si effettuano richieste al servizio tramite le API.

![Gestione delle chiavi](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Location|Scopo|
|--|--|--|
|Chiave di creazione|[Azure portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|queste chiavi vengono usate per accedere alle [API del servizio di gestione di QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Queste API consentono di modificare le domande e le risposte nella Knowledge base e di pubblicare la Knowledge base. Queste chiavi vengono create quando si crea un nuovo servizio QnA Maker.<br><br>Trovare queste chiavi nella risorsa **Servizi cognitivi** nella pagina **chiavi** .|
|Chiave endpoint query|[Portale di QnA Maker](https://www.qnamaker.ai)|Queste chiavi vengono utilizzate per eseguire una query sull'endpoint della Knowledge base pubblicata per ottenere una risposta per una domanda utente. Questo endpoint di query viene in genere usato in chat bot o nel codice dell'applicazione client che si connette al servizio QnA Maker. Queste chiavi vengono create quando si pubblica la QnA Maker Knowledge base.<br><br>Trovare queste chiavi nella pagina **Impostazioni servizio** . Trovare questa pagina dal menu dell'utente nella parte superiore destra della pagina nel menu a discesa.|

### <a name="subscription-keys"></a>Chiavi di sottoscrizione

La chiave di endpoint per la creazione e la query sono termini correttivi. Il termine precedente è una **chiave di sottoscrizione**. Se viene visualizzata un'altra documentazione che fa riferimento alle chiavi di sottoscrizione, queste sono equivalenti alle chiavi dell'endpoint di creazione ed esecuzione di query (usate nel Runtime).

Per conoscere la chiave che è necessario trovare, è necessario conoscere il tipo di accesso alla chiave, la gestione della Knowledge base o l'esecuzione di query sulla Knowledge base.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla QnA Maker [Knowledge base](knowledge-base.md)
* Informazioni sul [ciclo di vita della Knowledge base](development-lifecycle-knowledge-base.md)
* Esaminare i [limiti](../limits.md) del servizio e della Knowledge base

