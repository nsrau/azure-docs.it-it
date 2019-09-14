---
title: Configurare un servizio QnA Maker-QnA Maker
titleSuffix: Azure Cognitive Services
description: Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a2b467eed010edbb842d536bd8f6e3f4107fcea8
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984370"
---
# <a name="manage-qna-maker-resources"></a>Gestisci risorse QnA Maker

Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.

## <a name="types-of-keys-in-qna-maker"></a>Tipi di chiavi in QnA Maker

Il servizio QnA Maker gestisce due tipi di chiavi: chiavi di **sottoscrizione** e **chiavi dell'endpoint**.

![Gestione della chiave](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Location|Scopo|
|--|--|--|
|Chiave sottoscrizione|[Portale di Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|queste chiavi vengono usate per accedere alle [API del servizio di gestione di QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Queste API consentono di modificare le domande e le risposte nella Knowledge base e di pubblicare la Knowledge base. Queste chiavi vengono create quando si crea un nuovo servizio QnA Maker.<br><br>Trovare queste chiavi nella risorsa **Servizi cognitivi** nella pagina **chiavi** .|
|Chiave endpoint|[Portale di QnA Maker](http://www.qnamaker.ai)|Queste chiavi vengono utilizzate per accedere all'endpoint della Knowledge base pubblicata per ottenere una risposta per una domanda utente. Questo endpoint viene in genere usato in chat bot o nel codice dell'applicazione client che si connette al servizio QnA Maker. Queste chiavi vengono create quando si pubblica la QnA Maker Knowledge base.<br><br>Trovare queste chiavi nella pagina **Impostazioni servizio** . Trovare questa pagina dal menu dell'utente nella parte superiore destra della pagina nel menu a discesa.|

## <a name="create-a-new-qna-maker-service"></a>Creare un nuovo servizio QnA Maker

Questa procedura consente di creare le risorse di Azure necessarie per gestire il contenuto della Knowledge base. Dopo aver completato questi passaggi, le chiavi della _sottoscrizione_ sono disponibili nella pagina **chiavi** della risorsa nel portale di Azure.

1. Accedere al portale di Azure e [creare una risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selezionare **Crea** dopo aver letto i termini e le condizioni:

    ![Creare un nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. In **QnA Maker**selezionare i livelli e le aree appropriati:

    ![Creare un nuovo servizio QnA Maker - Piano tariffario e aree](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Nel campo **nome** immettere un nome univoco per identificare questo servizio QnA Maker. Questo nome identifica anche l'endpoint QnA Maker a cui verranno associate le Knowledge base.
    * Scegliere la **sottoscrizione** in cui verrà distribuita la risorsa QnA Maker.
    * Selezionare il piano **tariffario** per i servizi di gestione QnA Maker (portale e API di gestione). Vedere [altri dettagli sui prezzi degli SKU](https://aka.ms/qnamaker-pricing).
    * Creare un nuovo **gruppo di risorse** (scelta consigliata) o utilizzarne uno esistente in cui distribuire questa risorsa QnA Maker. QnA Maker crea diverse risorse di Azure. Quando si crea un gruppo di risorse che contiene queste risorse, è possibile trovare, gestire ed eliminare facilmente queste risorse in base al nome del gruppo di risorse.
    * Selezionare una **località del gruppo di risorse**.
    * Scegliere il **piano tariffario** per il servizio Ricerca di Azure. Se l'opzione del livello gratuito non è disponibile (visualizzata in grigio), significa che è già stato distribuito un livello di ricerca di Azure gratuito tramite la sottoscrizione. In tal caso, è necessario iniziare con il livello di ricerca di base di Azure. Vedere [i dettagli prezzi di ricerca di Azure](https://azure.microsoft.com/pricing/details/search/).
    * Scegliere il **percorso di ricerca** in cui si desidera distribuire i dati di ricerca di Azure. Per determinare il percorso scelto per ricerca di Azure, è necessario che le restrizioni relative ai dati dei clienti vengano archiviate.
    * Nel campo **nome app** immettere un nome per l'istanza del servizio app Azure.
    * Per impostazione predefinita, il servizio app viene impostato sul livello standard (S1). È possibile modificare il piano dopo la creazione. Altre informazioni sui [prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/).
    * Scegliere il **percorso del sito Web** in cui verrà distribuito il servizio app.

        > [!NOTE]
        > Il **percorso di ricerca** può essere diverso dal **percorso del sito Web**.

    * Specificare se si desidera abilitare o meno **Application Insights**. Se **Application Insights** è abilitato, QnA Maker raccoglierà dati di telemetria su traffico, log delle chat ed errori.
    * Scegliere il **percorso di App Insights** in cui verrà distribuita la risorsa Application Insights.
    * Per le misure di risparmio sui costi, è possibile [condividere](#share-existing-services-with-qna-maker) alcune risorse di Azure create per QnA Maker. 

1. Dopo la convalida di tutti i campi, selezionare **Crea**. Il completamento del processo può richiedere alcuni minuti.

1. Al termine della distribuzione, verranno visualizzate le risorse seguenti create nella sottoscrizione:

   ![Risorsa creata - Nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    La risorsa con il tipo di _Servizi cognitivi_ ha le chiavi di _sottoscrizione_ .

## <a name="find-subscription-keys-in-the-azure-portal"></a>Trovare le chiavi di sottoscrizione nel portale di Azure

È possibile visualizzare e reimpostare le chiavi della sottoscrizione dalla portale di Azure, in cui è stata creata la risorsa QnA Maker.

1. Passare alla risorsa QnA Maker nella portale di Azure e selezionare la risorsa con il tipo di _Servizi cognitivi_ :

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vai alle **chiavi**:

    ![Chiave sottoscrizione](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Trovare le chiavi degli endpoint nel portale di QnA Maker

L'endpoint si trova nella stessa area della risorsa perché le chiavi dell'endpoint vengono utilizzate per effettuare una chiamata alla Knowledge base.

Le chiavi endpoint possono essere gestite dal [portale di QnA Maker](https://qnamaker.ai).

1. Accedere al portale di [QnA Maker](https://qnamaker.ai), passare al profilo e quindi selezionare **impostazioni del servizio**:

    ![Chiave endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visualizza o Reimposta le chiavi:

    ![Gestione chiavi endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Aggiornare le chiavi se si ritiene che siano state compromesse. Questa operazione può richiedere modifiche corrispondenti al codice del bot o dell'applicazione client.

## <a name="share-existing-services-with-qna-maker"></a>Condividere i servizi esistenti con QnA Maker

QnA Maker crea diverse risorse di Azure. Per ridurre la gestione e trarre vantaggio dalla condivisione dei costi, usare la tabella seguente per comprendere cosa è possibile e non è possibile condividere:

|Service|Condividi|`Reason`|
|--|--|--|
|Servizi cognitivi|X|Non possibile dalla progettazione|
|Piano di servizio app|✔|Spazio su disco fisso allocato per un piano di servizio app. Se altre app che condividono lo stesso piano di servizio app usano spazio su disco significativo, si verificheranno problemi nell'istanza del servizio app QnAMaker.|
|Servizio app|x|Non possibile dalla progettazione|
|Application Insights|✔|Può essere condivisa|
|Servizio di ricerca|✔|1. `testkb` è un nome riservato per il servizio QnAMaker, non può essere usato da altri.<br>2. Il mapping dei sinonimi `synonym-map` in base al nome è riservato per il servizio QnAMaker.<br>3. Il numero di Knowledge base pubblicate è limitato dal livello di servizio di ricerca. Se sono disponibili indici gratuiti, possono essere usati da altri servizi.|

Altre informazioni sul servizio [app](../../../app-service/index.yml) e il [servizio di ricerca](../../../search/index.yml).

### <a name="using-a-single-search-service"></a>Uso di un singolo servizio di ricerca

Se si crea un servizio QnA e le relative dipendenze (ad esempio la ricerca) tramite il portale, viene creato un servizio di ricerca e collegato al servizio QnA Maker. Dopo aver creato queste risorse, è possibile aggiornare l'impostazione del servizio app per usare un servizio di ricerca precedentemente esistente e rimuovere quello appena creato.

Se si crea un servizio QnA tramite modelli di Azure Resource Manager, è possibile creare tutte le risorse e controllare la creazione del servizio app per l'uso di un servizio di ricerca esistente.

## <a name="upgrade-qna-maker"></a>QnA Maker di aggiornamento

|Aggiornamento|`Reason`|
|--|--|
|[Aggiornamento](#upgrade-qna-maker-sku) di SKU di gestione QnA Maker|Si desiderano ulteriori domande e risposte nella Knowledge base.|
|[Aggiornamento](#upgrade-app-service) di SKU del servizio app|La Knowledge base deve soddisfare un numero maggiore di richieste provenienti dall'app client, ad esempio un bot di chat.|
|[Aggiornamento](#upgrade-the-azure-search-service) di Servizio ricerca di Azure|Si prevede di avere molte Knowledge base.|


### <a name="upgrade-qna-maker-sku"></a>Aggiornare QnA Maker SKU

Per ulteriori domande e risposte nella Knowledge base, oltre al livello corrente, aggiornare il piano tariffario del servizio QnA Maker.

Per aggiornare lo SKU di gestione di QnA Maker:

1. Passare alla risorsa QnA Maker nel portale di Azure e selezionare **Piano tariffario**.

    ![Risorsa QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Scegliere lo SKU appropriato e fare clic su **Seleziona**.

    ![Prezzi di QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Aggiorna servizio app

 Quando la Knowledge base deve servire più richieste dall'app client, aggiornare il piano tariffario del servizio app.

È possibile [aumentare](https://docs.microsoft.com/azure/app-service/manage-scale-up) o ridurre il servizio app.

Passare alla risorsa del servizio app nel portale di Azure e selezionare l'opzione di **scalabilità verticale** o **orizzontale** secondo le esigenze.

![Scalabilità del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-search-service"></a>Aggiornare il servizio ricerca di Azure

Se si prevede di avere molte Knowledge base, aggiornare il piano tariffario del servizio ricerca di Azure.

Attualmente, non è possibile eseguire un aggiornamento sul posto dello SKU di ricerca di Azure. È tuttavia possibile creare una nuova risorsa di Ricerca di Azure con lo SKU desiderato, ripristinare i dati nella nuova risorsa e quindi collegarla allo stack di QnA Maker. A tale scopo, effettuare le operazioni seguenti:

1. Creare una nuova risorsa di ricerca di Azure nella portale di Azure e selezionare lo SKU desiderato.

    ![Risorsa di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Ripristinare gli indici dalla risorsa di Ricerca di Azure originale alla nuova risorsa. Vedere il [codice di esempio](https://github.com/pchoudhari/QnAMakerBackupRestore)per il ripristino del backup.

1. Dopo il ripristino dei dati, passare alla nuova risorsa ricerca di Azure, selezionare **chiavi**e annotare il **nome** e la **chiave amministratore**:

    ![Chiavi di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Per collegare la nuova risorsa di ricerca di Azure allo stack di QnA Maker, passare all'istanza del servizio app QnA Maker.

    ![QnA Maker istanza del servizio app](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selezionare **Impostazioni applicazione** e modificare le impostazioni nei campi **AzureSearchName** e **AzureSearchAdminKey** del passaggio 3.

    ![Impostazione del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Riavviare l'istanza del servizio app.

    ![Riavvio dell'istanza del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Ottenere gli aggiornamenti più recenti del runtime

Il runtime di QnAMaker fa parte dell'istanza del servizio app Azure distribuita quando si [Crea un servizio QnAMaker](./set-up-qnamaker-service-azure.md) nel portale di Azure. Vengono applicati aggiornamenti periodici al runtime. L'istanza del servizio app QnA Maker è in modalità di aggiornamento automatico dopo la versione dell'estensione del sito aprile 2019 (versione 5 +). Questo aggiornamento è stato progettato per gestire il tempo di inattività durante gli aggiornamenti.

È possibile controllare la versione corrente in https://www.qnamaker.ai/UserSettings. Se la versione è precedente alla versione 5. x, è necessario riavviare il servizio app per applicare gli aggiornamenti più recenti:

1. Passare al servizio QnAMaker (gruppo di risorse) nell' [portale di Azure](https://portal.azure.com).

    ![Gruppo di risorse di Azure per QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selezionare l'istanza del servizio app e aprire la sezione **Panoramica** .

    ![Istanza del servizio app QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Riavviare il servizio app. Il processo di aggiornamento dovrebbe terminare tra qualche secondo. Eventuali applicazioni dipendenti o bot che usano questo servizio QnAMaker non saranno disponibili per gli utenti finali durante questo periodo di riavvio.

    ![Riavvio dell'istanza del servizio app QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Area del servizio di gestione

Il servizio di gestione di QnA Maker viene usato solo per il portale di QnA Maker e per l'elaborazione iniziale dei dati. Questo servizio è disponibile solo nell'area Stati Uniti occidentali. Nessun dato cliente è archiviato in questo servizio Stati Uniti occidentali.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul servizio [app](../../../app-service/index.yml) e il [servizio di ricerca](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Creare e pubblicare una Knowledge Base](../Quickstarts/create-publish-knowledge-base.md)
