---
title: Configurare un servizio QnA Maker - QnA Maker
description: Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 563a56fdb288568e7fe667fa54658400064a560f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402979"
---
# <a name="manage-qna-maker-resources"></a>Gestire le risorse di QnA MakerManage QnA Maker resources

Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.

Prima di creare la risorsa, è utile comprendere concretamente i concetti seguenti:A solid understanding of the following concepts is helpful before creating your resource:

* [Risorse QnA Maker](../Concepts/azure-resources.md)
* [Chiavi di creazione e pubblicazione](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Creare un nuovo servizio QnA Maker

Questa procedura consente di creare le risorse di Azure necessarie per gestire il contenuto della Knowledge Base. Dopo aver completato questi passaggi, le chiavi di sottoscrizione sono disponibili nella pagina Chiavi per la risorsa nel portale di Azure.After you complete these steps, you'll find the _subscription_ keys on the **Keys** page for the resource in the Azure portal.

1. Accedere al portale di Azure e creare una risorsa QnA Maker.Sign in to the Azure portal and [create a QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) resource.

1. Seleziona **Crea** dopo aver letto i termini e le condizioni:

    ![Creare un nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. In **QnA Maker**selezionare i livelli e le aree appropriati:

    ![Creare un nuovo servizio QnA Maker - Piano tariffario e aree](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Nel campo **Nome** immettere un nome univoco per identificare il servizio QnA Maker. Questo nome identifica anche l'endpoint QnA Maker a cui verranno associate le Knowledge Base.
    * Scegliere la **sottoscrizione** in cui verrà distribuita la risorsa QnA Maker.
    * Selezionare il **piano dei prezzi** per i servizi di gestione QnA Maker (API di portale e gestione). Per [ulteriori informazioni sui prezzi degli SKU, vedere .](https://aka.ms/qnamaker-pricing)
    * Creare un nuovo gruppo di risorse (scelta consigliata) o usarne uno esistente in cui distribuire questa risorsa QnA Maker.Create a new **Resource group** (recommended) or use an existing in which to deploy this QnA Maker resource. QnA Maker crea diverse risorse di Azure.QnA Maker creates several Azure resources. Quando si crea un gruppo di risorse per contenere queste risorse, è possibile trovare, gestire ed eliminare facilmente queste risorse in base al nome del gruppo di risorse.
    * Selezionare una **posizione gruppo di risorse**.
    * Scegliere il **piano di determinazione dei prezzi Ricerca** del servizio Ricerca cognitiva di Azure.Choose the Search pricing tier of the Azure Cognitive Search service. Se l'opzione Livello gratuito non è disponibile (visualizzato in grigio), significa che si dispone già di un servizio gratuito distribuito tramite la sottoscrizione. In tal caso, è necessario iniziare con il livello Basic.In that case, you'll need to start with the Basic tier. Vedere [Dettagli sui prezzi di Ricerca cognitiva di Azure.](https://azure.microsoft.com/pricing/details/search/)
    * Scegliere il **percorso di ricerca** in cui si vuole distribuire gli indici di Ricerca cognitiva di Azure.Choose the Search location where you want Azure Cognitive Search indexes to be deployed. Le restrizioni relative alla posizione in cui devono essere archiviati i dati dei clienti consentono di determinare la posizione scelta per Ricerca cognitiva di Azure.Restrictions on where customer data must be stored will help determine the location you choose for Azure Cognitive Search.
    * Nel campo Nome app immettere un nome per l'istanza del servizio app di Azure.In the **App name** field, enter a name for your Azure App Service instance.
    * Per impostazione predefinita, per impostazione predefinita il servizio app è il livello standard (S1). È possibile modificare il piano dopo la creazione. Ulteriori informazioni sui [prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/).
    * Scegliere il **percorso del sito Web** in cui verrà distribuito il servizio app.

        > [!NOTE]
        > La **posizione di ricerca** può differire dalla posizione del sito **Web**.

    * Scegliere se abilitare o meno **Application Insights**. Se **Application Insights** è abilitato, QnA Maker raccoglierà dati di telemetria su traffico, log delle chat ed errori.
    * Scegliere il **percorso di App insights** in cui verrà distribuita la risorsa Application Insights.Choose the App insights location where the Application Insights resource will be deployed.
    * Per le misure di risparmio sui costi, è possibile [condividere](#configure-qna-maker-to-use-different-cognitive-search-resource) alcune ma non tutte le risorse di Azure create per QnA Maker.

1. Dopo aver convalidato tutti i campi, selezionare **Crea**. Il completamento del processo può richiedere alcuni minuti.

1. Al termine della distribuzione, nella sottoscrizione verranno visualizzate le risorse seguenti:After deployment is completed, you'll see the following resources created in your subscription:

   ![Risorsa creata - Nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    La risorsa con il tipo _Servizi cognitivi_ contiene le chiavi _di sottoscrizione._

## <a name="find-subscription-keys-in-the-azure-portal"></a>Trovare le chiavi di sottoscrizione nel portale di AzureFind subscription keys in the Azure portal

È possibile visualizzare e reimpostare le chiavi di sottoscrizione dal portale di Azure, in cui è stata creata la risorsa QnA Maker.You can view and reset your subscription keys from the Azure portal, where you created the QnA Maker resource.

1. Passare alla risorsa QnA Maker nel portale di Azure e selezionare la risorsa con il tipo Servizi cognitivi:Go to the QnA Maker resource in the Azure portal and select the resource that has the _Cognitive Services_ type:

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vai a **Chiavi**:

    ![Chiave della sottoscrizione](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Trovare le chiavi endpoint nel portale QnA MakerFind endpoint keys in the QnA Maker portal

L'endpoint si trova nella stessa area della risorsa perché le chiavi dell'endpoint vengono utilizzate per effettuare una chiamata alla Knowledge Base.

Le chiavi endpoint possono essere gestite dal [portale di QnA Maker](https://qnamaker.ai).

1. Accedere al [portale QnA Maker,](https://qnamaker.ai)passare al proprio profilo e quindi selezionare **Impostazioni servizio:**

    ![Chiave endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visualizzare o reimpostare le chiavi:

    > [!div class="mx-imgBorder"]
    > ![Gestione delle chiavi dell'endpointEndpoint key manager](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Aggiorna le chiavi se pensi che siano state compromesse. Questa operazione può richiedere modifiche corrispondenti al codice del bot o dell'applicazione client.

## <a name="upgrade-qna-maker-sku"></a>Upgrade QnA Maker SKU

Quando si desidera avere più domande e risposte nella Knowledge Base, oltre il livello corrente, aggiornare il livello di prezzo del servizio QnA Maker.When you want to have more questions and answers in your knowledge base, beyond your current tier, upgrade your QnA Maker service pricing.

Per aggiornare lo SKU di gestione di QnA Maker:

1. Passare alla risorsa QnA Maker nel portale di Azure e selezionare **Piano tariffario**.

    ![Risorsa QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Scegliere lo SKU appropriato e fare clic su **Seleziona**.

    ![Prezzi di QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Aggiornare il servizio app

 Quando la Knowledge Base deve soddisfare più richieste dall'app client, aggiornare il livello dei prezzi del servizio app.

È possibile [aumentare](https://docs.microsoft.com/azure/app-service/manage-scale-up) o ridurre la scalabilità orizzontale del servizio app.

Passare alla risorsa del servizio app nel portale di Azure e selezionare l'opzione **Scalabilità verticale** o **Scalabilità verticale** in base alle esigenze.

![Scala del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>Aggiornare il servizio Ricerca cognitiva di AzureUpgrade the Azure Cognitive Search service

Se si prevede di avere molte basi di conoscenza, aggiornare il piano tariffario del servizio Ricerca cognitiva di Azure.If you plan to have many knowledge bases, upgrade your Azure Cognitive Search service pricing tier.

Attualmente, non è possibile eseguire un aggiornamento sul posto dello SKU di ricerca di Azure.Currently, you can't perform an in-place upgrade of the Azure search SKU. È tuttavia possibile creare una nuova risorsa di Ricerca di Azure con lo SKU desiderato, ripristinare i dati nella nuova risorsa e quindi collegarla allo stack di QnA Maker. A questo scopo, seguire questa procedura:

1. Creare una nuova risorsa di ricerca di Azure nel portale di Azure e selezionare lo SKU desiderato.

    ![Risorsa di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Ripristinare gli indici dalla risorsa di Ricerca di Azure originale alla nuova risorsa. Vedere il [codice di esempio](https://github.com/pchoudhari/QnAMakerBackupRestore)di ripristino del backup .

1. Dopo il ripristino dei dati, passare alla nuova risorsa di ricerca di Azure, selezionare **Chiavi**e annotare il **nome** e la **chiave di amministrazione:**

    ![Chiavi di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Per collegare la nuova risorsa di ricerca di Azure allo stack QnA Maker, passare all'istanza del servizio app QnA Maker.To link the new Azure search resource to the QnA Maker stack, go to the QnA Maker App Service instance.

    ![Istanza del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selezionare **Impostazioni applicazione** e modificare le impostazioni nei campi NomeRicercaAutorizzazioni e NomeAmministrazioneAzure del passaggio 3.Select Application settings and modify the settings in the **AzureSearchName** and **AzureSearchAdminKey** fields from step 3.

    ![Impostazione del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Riavviare l'istanza del servizio app.

    ![Riavvio dell'istanza del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Ottenere gli aggiornamenti di runtime più recentiGet the latest runtime updates

Il runtime QnAMaker fa parte dell'istanza del servizio app di Azure distribuita quando si [crea un servizio QnAMaker](./set-up-qnamaker-service-azure.md) nel portale di Azure.The QnAMaker runtime is part of the Azure App Service instance that's deployed when you create a QnAMaker service in the Azure portal. Vengono applicati aggiornamenti periodici al runtime. L'istanza del servizio app QnA Maker è in modalità di aggiornamento automatico dopo la versione dell'estensione del sito di aprile 2019 (versione 5). Questo aggiornamento è stato progettato per prendersi cura di zero tempo di inattività durante gli aggiornamenti.

È possibile controllare la https://www.qnamaker.ai/UserSettingsversione corrente all'indirizzo . Se la versione della versione precedente alla versione 5.x, è necessario riavviare il servizio app per applicare gli aggiornamenti più recenti:If your version is older than version 5.x, you must restart App Service to apply the latest updates:

1. Passare al servizio QnAMaker (gruppo di risorse) nel portale di [Azure.](https://portal.azure.com)

    > [!div class="mx-imgBorder"]
    > ![Gruppo di risorse di Azure per QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selezionare l'istanza del servizio app e aprire la sezione **Panoramica.Select** the App Service instance and open the Overview section.

    > [!div class="mx-imgBorder"]
    > ![Istanza del servizio app QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Riavviare il servizio app. Il processo di aggiornamento dovrebbe terminare in un paio di secondi. Tutte le applicazioni o i bot dipendenti che utilizzano questo servizio QnAMaker non saranno disponibili per gli utenti finali durante questo periodo di riavvio.

    ![Riavvio dell'istanza del servizio app QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configurare QnA Maker per l'utilizzo di risorse di Ricerca cognitiva diverseConfigure QnA Maker to use different Cognitive Search resource

Se si crea un servizio QnA e le relative dipendenze (ad esempio ricerca) tramite il portale, viene creato automaticamente un servizio di ricerca e collegato al servizio QnA Maker. Dopo aver creato queste risorse, è possibile aggiornare l'impostazione del servizio app per usare un servizio di ricerca esistente in precedenza e rimuovere quella appena creata.

La risorsa del **servizio app** di QnA Maker usa la risorsa Ricerca cognitiva. Per modificare la risorsa Ricerca cognitiva usata da QnA Maker, è necessario modificare l'impostazione nel portale di Azure.In order to change the Cognitive Search resource used by QnA Maker, you need to change the setting in the Azure portal.

1. Ottenere la **chiave di amministratore** e il **nome** della risorsa Ricerca cognitiva che si desidera venga noto da QnA Maker.

1. Accedere al [portale](https://portal.azure.com) di Azure e trovare il servizio app associato alla risorsa QnA Maker.Sign in to the Azure portal and find the **App Service** associated with your QnA Maker resource. Entrambi con hanno lo stesso nome.

1. Selezionare **Impostazioni**, quindi **Configurazione**. Verranno visualizzate tutte le impostazioni esistenti per il servizio app di QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Screenshot del portale di Azure che mostra le impostazioni di configurazione del servizio app](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Modificare i valori per le seguenti chiavi:

    * **Chiave AzureSearchAdmin**
    * **NomeAzureSearchName**

1. Per usare le nuove impostazioni, è necessario riavviare il servizio app. Selezionare **Panoramica**, quindi **Riavvia**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot del riavvio del servizio app del portale di Azure dopo la modifica delle impostazioni di configurazione](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Se si crea un servizio QnA tramite i modelli di Azure Resource Manager, è possibile creare tutte le risorse e controllare la creazione del servizio app per usare un servizio di ricerca esistente.

Altre informazioni su come configurare le [impostazioni dell'applicazione](../../../app-service/configure-common.md#configure-app-settings)di servizio app .

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Configurare l'impostazione inattiva del servizio app per evitare il timeout

Il servizio app, che serve il runtime di stima QnA Maker per una Knowledge Base pubblicata, ha una configurazione di timeout di inattività, che per impostazione predefinita scappa automaticamente se il servizio è inattivo. Per QnA Maker, ciò significa che il runtime di stima genera occasionalmente timeout dell'API dopo periodi di non traffico.

Per mantenere caricata l'app dell'endpoint di stima anche in funzione, imposta l'inattività su sempre attiva.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e selezionare il servizio app della risorsa QnA Maker. Avrà lo stesso nome della risorsa QnA Maker, ma avrà un **tipo** diverso di servizio app.
1. Trovare **Impostazioni,** quindi selezionare **Configurazione**.
1. Nel riquadro Configurazione selezionare **Impostazioni generali**, quindi **Sempre attivato**e selezionare **Attivato** come valore.

    > [!div class="mx-imgBorder"]
    > ![Nel riquadro di configurazione, selezionare " Impostazioni generali", quindi trovare " Sempre su " e selezionare "On" come valore.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Selezionare **Salva** per salvare la configurazione.
1. Viene chiesto se si desidera riavviare l'app per utilizzare la nuova impostazione. Selezionare **Continua**.

Ulteriori informazioni su come configurare le [impostazioni generali](../../../app-service/configure-common.md#configure-general-settings)del servizio app .

## <a name="delete-azure-resources"></a>Eliminare le risorse di Azure

Se si elimina una delle risorse di Azure usate per le knowledge base di QnA Maker, le knowledge base non funzioneranno più. Prima di eliminare qualsiasi risorsa, assicurarsi di esportare le knowledge base dalla pagina **Impostazioni**.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sul [servizio app](../../../app-service/index.yml) e sul servizio [di ricerca](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Creare e pubblicare una knowledge base](../Quickstarts/create-publish-knowledge-base.md)
