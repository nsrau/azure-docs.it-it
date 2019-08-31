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
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec154a7e437621f377c503642b186cf166016cc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195311"
---
# <a name="manage-qna-maker-resources"></a>Gestisci risorse QnA Maker

Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.

## <a name="types-of-keys-in-qna-maker"></a>Tipi di chiavi in QnA Maker

Il servizio QnA Maker gestisce due tipi di chiavi, le **chiavi di sottoscrizione** e le **chiavi endpoint**.

![gestione delle chiavi](../media/qnamaker-how-to-key-management/key-management.png)

|NOME|Location|Scopo|
|--|--|--|
|Chiave sottoscrizione|[Portale di Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|queste chiavi vengono usate per accedere alle [API del servizio di gestione di QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Queste API consentono di modificare le domande e le risposte nella Knowledge base e di pubblicare la Knowledge base. Queste chiavi vengono create quando si crea un nuovo servizio QnA Maker.<br><br>Trovare queste chiavi nella risorsa **Servizi cognitivi** nella pagina **chiavi** .|
|Chiave endpoint|[Portale di QnA Maker](http://www.qnamaker.ai)|Queste chiavi vengono utilizzate per accedere all'endpoint della Knowledge base pubblicata per ottenere una risposta per una domanda utente. Questo endpoint viene in genere usato in chat bot o nel codice dell'applicazione client che si connette al servizio QnA Maker. Queste chiavi vengono create quando si pubblica la QnA Maker Knowledge base.<br><br>Trovare queste chiavi nella pagina **Impostazioni servizio** . Trovare questa pagina dal menu in alto e a destra dell'utente come scelta nell'elenco a discesa.|

## <a name="create-a-new-qna-maker-service"></a>Creare un nuovo servizio QnA Maker

Questa procedura consente di creare le risorse di Azure necessarie per gestire il contenuto della Knowledge base. Quando si completano questi passaggi, le chiavi della _sottoscrizione_ sono disponibili nella pagina **chiavi** della risorsa nel portale di Azure.

1. Accedere al portale di Azure e [creare una risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selezionare **Crea** dopo aver letto i termini e le condizioni.

    ![Creare un nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. In **QnA Maker** selezionare i livelli e le aree appropriati.

    ![Creare un nuovo servizio QnA Maker - Piano tariffario e aree](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Specificare un **nome** univoco per identificare il servizio QnA Maker. Questo nome identifica anche l'endpoint QnA Maker a cui saranno associate le Knowledge Base.
    * Scegliere la **sottoscrizione** in cui verrà distribuita la risorsa QnA Maker.
    * Selezionare il piano tariffario per i servizi di gestione QnA Maker (portale e API di gestione). Vedere [qui](https://aka.ms/qnamaker-pricing) per informazioni dettagliate sui prezzi degli SKU.
    * Creare un nuovo **gruppo di risorse** (scelta consigliata) o usare un gruppo di risorse esistente in cui distribuire questa risorsa QnA Maker. QnA Maker crea diverse risorse di Azure; Quando si crea un gruppo di risorse che contiene queste risorse, è possibile trovare, gestire ed eliminare facilmente queste risorse in base al nome del gruppo di risorse.
    * Selezionare una **località del gruppo di risorse**.
    * Scegliere il **piano tariffario** per il servizio Ricerca di Azure. Se l'opzione Livello gratuito è disabilitata, nella sottoscrizione è già stato distribuito un livello gratuito per Ricerca di Azure. In tal caso è necessario iniziare con il livello Basic di Ricerca di Azure. Vedere [qui](https://azure.microsoft.com/pricing/details/search/) i dettagli dei prezzi di Ricerca di Azure.
    * Scegliere il **percorso di ricerca** per la distribuzione dei dati di Ricerca di Azure. Le limitazioni relative ai percorsi in cui devono essere archiviati i dati dei clienti determinano il percorso scelto per Ricerca di Azure.
    * Assegnare un nome al servizio app in **Nome app**.
    * Per impostazione predefinita, il servizio app è impostato sul livello Standard (S1). È possibile modificare il piano dopo la creazione. Vedere [qui](https://azure.microsoft.com/pricing/details/app-service/) per altri dettagli sui prezzi del servizio app.
    * Scegliere il percorso del **sito Web** in cui verrà distribuito il servizio app.

        > [!NOTE]
        > Il percorso di ricerca sarà diverso dal percorso del sito Web.

    * Scegliere se abilitare o meno **Application Insights**. Se **Application Insights** è abilitato, QnA Maker raccoglierà dati di telemetria su traffico, log delle chat ed errori.
    * Scegliere il **percorso** in cui verrà distribuita la risorsa Application Insights.
    * Per le misure di risparmio sui costi, è possibile [condividere](#share-existing-services-with-qna-maker) alcune risorse di Azure create per QnA Maker. 

1. Una volta convalidati tutti i campi, selezionare **Crea**. Il completamento dell'operazione può richiedere alcuni minuti.

1. Dopo aver completato la distribuzione, nella sottoscrizione verranno visualizzate le seguenti risorse create.

    ![Risorsa creata - Nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    La risorsa con il tipo _Servizi cognitivi_ dispone delle chiavi di _sottoscrizione_ .

## <a name="find-subscription-keys-in-azure-portal"></a>Trovare le chiavi di sottoscrizione in portale di Azure

È possibile visualizzare e reimpostare le chiavi della sottoscrizione, che modificano dal portale di Azure in cui è stata creata la risorsa QnA Maker. 

1. Passare alla risorsa QnA Maker nella portale di Azure e selezionare la risorsa con il tipo _Servizi cognitivi_.

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Passare a **Chiavi**.

    ![chiave di sottoscrizione](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-qna-maker-portal"></a>Trovare le chiavi degli endpoint nel portale QnA Maker

Le chiavi endpoint possono essere gestite dal [portale di QnA Maker](https://qnamaker.ai).

1. Accedere al [portale di QnA Maker](https://qnamaker.ai), passare al proprio profilo e fare clic su **Impostazioni servizio**.

    ![chiave endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visualizzare o reimpostare le chiavi.

    ![gestione chiavi endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Aggiornare le chiavi se si ritiene che siano state compromesse. Questa operazione può richiedere modifiche corrispondenti al codice del bot o dell'applicazione client.

## <a name="share-existing-services-with-qna-maker"></a>Condividere i servizi esistenti con QnA Maker

QnA Maker crea diverse risorse di Azure. Per ridurre la gestione e trarre vantaggio dalla condivisione dei costi, usare la tabella seguente per comprendere cosa è possibile e non è possibile condividere:

|Service|Condividi|`Reason`|
|--|--|--|
|Servizi cognitivi|x|Non possibile dalla progettazione|
|Piano di servizio app|✔|Spazio su disco fisso allocato per un piano di servizio app. Se altre app, che condividono lo stesso piano di servizio app, usano spazio su disco significativo, il servizio app QnAMaker si risolverà in problemi.|
|Servizio app|X|Non possibile dalla progettazione|
|Application Insights|✔|Può essere condivisa|
|Servizio di ricerca|✔|1. `testkb` è un nome riservato per il servizio QnAMaker, non può essere usato da altri.<br>2. Il mapping dei sinonimi `synonym-map` con il nome è riservato per il servizio QnAMaker.<br>3. Il numero di KB pubblicati è limitato dal livello di servizio di ricerca. Se sono disponibili indici gratuiti, possono essere usati da altri servizi.|

### <a name="using-a-single-search-service"></a>Uso di un singolo servizio di ricerca

Se si crea un servizio QnA e le relative dipendenze (ad esempio la ricerca) tramite il portale, viene creato un servizio di ricerca e associato al servizio QnA Maker. Dopo aver creato queste risorse, è possibile aggiornare l'impostazione del servizio app per usare un servizio di ricerca precedentemente esistente e rimuovere il servizio di ricerca appena creato.

Se si crea un servizio QnA tramite Azure Resource Manager modelli, è possibile creare tutte le risorse e controllare la creazione del servizio app per l'uso di un servizio di ricerca esistente. 

## <a name="upgrade-qna-maker"></a>QnA Maker di aggiornamento

|Aggiornamento|`Reason`|
|--|--|
|[Aggiornamento](#upgrade-qna-maker-sku) di SKU di gestione QnA Maker|È necessario disporre di ulteriori domande e risposte nella Knowledge base.|
|[Aggiornamento](#upgrade-app-service) di SKU del servizio app|La Knowledge base deve gestire più richieste dall'app client, ad esempio un bot di chat.|
|[Aggiornamento](#upgrade-azure-search-service) di Servizio ricerca di Azure|Si prevede di avere molte Knowledge base.|


### <a name="upgrade-qna-maker-sku"></a>Aggiornare QnA Maker SKU

Quando è necessario disporre di altre domande e risposte nella knowledge base, oltre il livello corrente, aggiornare il piano tariffario del servizio QnA Maker. 

Per aggiornare lo SKU di gestione di QnA Maker:

1. Passare alla risorsa QnA Maker nel portale di Azure e selezionare **Piano tariffario**.

    ![Risorsa QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Scegliere lo SKU appropriato e fare clic su **Seleziona**.

    ![Prezzi di QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Aggiornare il servizio app

 Quando la knowledge base deve servire più richieste dall'applicazione client, aggiornare il piano tariffario del servizio app.

È possibile [aumentare](https://docs.microsoft.com/azure/app-service/manage-scale-up) o ridurre le prestazioni del servizio app.

Passare alla risorsa del servizio app nel portale di Azure e selezionare le opzioni per **aumentare** o **ridurre** le prestazioni secondo le esigenze.

![Ridimensionamento del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-azure-search-service"></a>Aggiornare il servizio Ricerca di Azure

Quando si prevede di avere molte knowledge base, aggiornare il piano tariffario del servizio di Ricerca di Azure. 

Al momento non è possibile eseguire un aggiornamento sul posto dello SKU di Ricerca di Azure. È tuttavia possibile creare una nuova risorsa di Ricerca di Azure con lo SKU desiderato, ripristinare i dati nella nuova risorsa e quindi collegarla allo stack di QnA Maker.

1. Creare una nuova risorsa di Ricerca di Azure nel portale di Azure e scegliere lo SKU desiderato.

    ![Risorsa di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Ripristinare gli indici dalla risorsa di Ricerca di Azure originale alla nuova risorsa. Vedere il codice di esempio di ripristino del backup [qui](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Dopo aver ripristinato i dati, passare alla nuova risorsa di Ricerca di Azure, selezionare **Chiavi**, quindi annotare **Nome** e **Chiave amministratore**.

    ![Chiavi di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Per collegare la nuova risorsa di Ricerca di Azure allo stack di QnA Maker, passare al servizio app QnA Maker.

    ![Servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selezionare **Impostazioni applicazione** e sostituire i campi **AzureSearchName** e **AzureSearchAdminKey** del passaggio 3.

    ![Impostazione del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Riavviare il servizio app.

    ![Riavvio del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-latest-runtime-updates"></a>Ottenere gli aggiornamenti più recenti del runtime

Il runtime di QnAMaker fa parte del servizio app Azure distribuito quando si [Crea un servizio QnAMaker](./set-up-qnamaker-service-azure.md) in portale di Azure. Vengono applicati aggiornamenti periodici al runtime. Il servizio app di QnA Maker si trova in modalità di aggiornamento automatico dopo la versione dell'estensione del sito apr 2019 (versione 5 +). Questa operazione è già stata progettata in modo da occuparsi del tempo di inattività durante gli aggiornamenti. 

È possibile controllare la versione corrente in https://www.qnamaker.ai/UserSettings. Se la versione è precedente alla versione 5. x, è necessario riavviare il servizio app per applicare gli aggiornamenti più recenti.

1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse di Azure per QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Fare clic sul servizio app e aprire la sezione Panoramica

     ![Servizio app QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Riavviare il servizio app. Il riavvio dovrebbe richiedere un paio di secondi. Eventuali applicazioni dipendenti o bot che usano questo servizio QnAMaker non saranno disponibili per gli utenti finali durante questo periodo di riavvio.

    ![Riavvio del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Area del servizio di gestione

Il servizio di gestione di QnA Maker, usato solo per il portale di QnA Maker & per l'elaborazione iniziale dei dati, è disponibile solo negli Stati Uniti occidentali. Nessun dato cliente è archiviato in questo servizio Stati Uniti occidentali.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare e pubblicare una Knowledge Base](../Quickstarts/create-publish-knowledge-base.md)
