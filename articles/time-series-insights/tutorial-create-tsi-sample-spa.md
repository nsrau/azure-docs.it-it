---
title: "Esercitazione: Creare un'app Web a singola pagina Azure Time Series Insights| Microsoft Docs"
description: Informazioni su come creare un'applicazione Web a singola pagina che esegue query e il rendering di dati da un ambiente Azure Time Series Insights.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 4d9af918c222107cfca5863309efb391b8e6d2e0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720874"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Esercitazione: Creare un'app Web a singola pagina Azure Time Series Insights

Questa esercitazione descrive il processo di creazione di un'applicazione Web a singola pagina per accedere a dati di Azure Time Series Insights.

In questa esercitazione si vedrà:

> [!div class="checklist"]
> * La progettazione dell'applicazione
> * Come registrare l'applicazione con Azure Active Directory (Azure AD)
> * Come creare, pubblicare e testare l'applicazione Web

> [!NOTE]
> * Il codice sorgente completo per questa esercitazione è disponibile in [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * È ospitata l'[app di esempio client](https://insights.timeseries.azure.com/clientsample) Time Series Insights per mostrare l'app completata usata in questa esercitazione.

Registrarsi per creare una [sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/), se non è già stato fatto.

## <a name="prerequisites"></a>Prerequisiti

* Copia gratuita di Visual Studio. Scaricare la [versione Community 2017 o 2019](https://www.visualstudio.com/downloads/) per iniziare.

* Componenti degli strumenti di base IIS Express, Distribuzione Web e Servizi cloud di Azure per Visual Studio. Aggiungere i componenti modificando l'installazione di Visual Studio.

## <a name="understand-application-design"></a>Comprendere la progettazione dell'applicazione

L'applicazione a singola pagina Time Series Insights costituisce la base per la progettazione e il codice usati in questa esercitazione. Il codice usa la libreria client JavaScript per Time Series Insights. La libreria client per Time Series Insights fornisce un'astrazione per due categorie di API principali:

- **Metodi wrapper per chiamare le API di query di Time Series Insights**: API REST che è possibile usare per eseguire query sui dati di Time Series Insights usando espressioni basate su JSON. I metodi sono organizzati all'interno dello spazio dei nomi TsiClient.server della libreria.

- **Metodi per la creazione e il popolamento di diversi tipi di controllo dei grafici**: metodi che è possibile usare per visualizzare dati di Azure Time Series Insights in una pagina Web. I metodi sono organizzati all'interno dello spazio dei nomi TsiClient.ux della libreria.

Questa esercitazione usa anche i dati dall'ambiente Time Series Insights dell'applicazione di esempio. Per informazioni dettagliate sulla struttura dell'applicazione di esempio Time Series Insights e su come usa la libreria client per Time Series Insights, vedere l'esercitazione [Esplorare la libreria client JavaScript per Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-with-azure-ad"></a>Effettuare la registrazione con Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>Compilare e pubblicare

1. Creare una directory dove memorizzare i file di progetto dell'applicazione. Passare quindi a ognuno degli URL seguenti. Fare clic con il pulsante destro del mouse sul collegamento **Non elaborato** nell'angolo in alto a destra della pagina e quindi selezionare **Salva con nome** per salvare i file nella directory del progetto.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML e JavaScript per la pagina
   - [*sampleStyles.css*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): foglio di stile CSS

   > [!NOTE]
   > A seconda del browser, può essere necessario modificare le estensioni in html o css prima di salvare il file.

1. Verificare che i componenti necessari siano installati in Visual Studio. Devono essere installati i componenti degli strumenti di base IIS Express, Distribuzione Web e Servizi cloud di Azure per Visual Studio.

    [![Visual Studio - Modifica dei componenti installati](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > L'esperienza di Visual Studio può variare leggermente rispetto agli esempi mostrati, a seconda della versione e delle impostazioni di configurazione.

1. Aprire Visual Studio e accedere. Per creare un progetto per l'applicazione Web, dal menu **File** scegliere **Apri** > **Sito Web**.

    [![Visual Studio - Creazione di una nuova soluzione](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. Nel riquadro **Apri sito Web** selezionare la directory di lavoro in cui sono stati archiviati i file HTML e CSS e quindi fare clic su **Apri**.

   [![Visual Studio - Menu File, con le opzioni Apri e Sito Web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. Scegliere **Esplora soluzioni** dal menu **Visualizza** di Visual Studio. Viene aperta la nuova soluzione. Questa contiene un progetto di sito Web (icona di globo) che include i file HTML e CSS.

   [![Visual Studio - Nuova soluzione in Esplora soluzioni](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Prima di pubblicare l'app, è necessario modificare le impostazioni di configurazione in *index.html*.

   1. Rimuovere il commento dalle tre righe sotto il commento `"PROD RESOURCE LINKS"` per trasferire le dipendenze da SVILUPPO a PRODUZIONE. Commentare le tre righe sotto il commento `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      I commenti per le dipendenze saranno simili a quelli mostrati nell'esempio seguente:

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Per configurare l'app in modo da usare l'ID registrazione dell'app di Azure AD, modificare il valore di `clientID` in modo da usare il valore di **ID applicazione** copiato nel **passaggio 3** quando è stata [registrata l'applicazione per l'uso di Azure AD](#register-with-azure-ad). Se è stato specificato **URL disconnessione** in Azure AD, impostarlo come valore `postLogoutRedirectUri`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Ad esempio:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Dopo aver apportato le modifiche, salvare *index.html*.

1. Pubblicare l'applicazione Web nella sottoscrizione di Azure come servizio app di Azure.  

   > [!NOTE]
   > Diverse opzioni negli screenshot mostrati nei passaggi successivi vengono automaticamente impostate con i dati della sottoscrizione di Azure. Possono essere necessari alcuni secondi prima che ogni riquadro venga caricato completamente.  

   1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo del progetto di sito Web e quindi scegliere **Pubblica app Web**.  

      [![Visual Studio - Selezione dell'opzione Pubblica app Web in Esplora soluzioni](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Selezionare **Avvia** per iniziare a pubblicare l'app.

      [![Visual Studio - Riquadro Pubblica](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Selezionare la sottoscrizione che si vuole usare per pubblicare l'applicazione. Selezionare il progetto **TsiSpaApp**. Quindi selezionare **OK**.

      [![Visual Studio - Riquadro Pubblica in Servizio app](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Selezionare **Pubblica** per distribuire l'applicazione Web.

      [![Visual Studio - Opzione Pubblica e output del log di pubblicazione](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Viene visualizzato un log di pubblicazione che indica che l'operazione è riuscita nel riquadro **Output** di Visual Studio. Al termine della distribuzione, Visual Studio apre l'applicazione Web in una scheda del browser e chiede di accedere. Dopo aver effettuato l'accesso, vengono immessi i dati nei controlli di Time Series Insights.

## <a name="troubleshoot"></a>Risolvere problemi  

Codice di errore/condizione | DESCRIZIONE
---------------------| -----------
*AADSTS50011: Nessun indirizzo di risposta registrato per l'applicazione.* | Nella registrazione in Azure AD manca la proprietà **URL di risposta**. Passare a **Impostazioni** > **URL di risposta** per la registrazione dell'applicazione in Azure AD. Verificare che sia presente il valore **URI di reindirizzamento** che è stato possibile specificare nel **passaggio 2** o **passaggio 4** quando [è stata registrata l'applicazione per l'uso di Azure AD](#register-with-azure-ad).
*AADSTS50011: The reply url specified in the request does not match the reply urls configured for the application (L'URL di risposta specificato nella richiesta non corrisponde agli URL di risposta configurati per l'applicazione): "\<GUID ID applicazione>".* | Il valore di `postLogoutRedirectUri` specificato nel **passaggio 6.b** in [Compilare e pubblicare l'applicazione Web](#build-and-publish) deve corrispondere al valore specificato in **Impostazioni** > **URL di risposta** nella registrazione dell'applicazione in Azure AD. |
L'applicazione Web viene caricata, ma la pagina di accesso contiene solo testo senza stile, con uno sfondo bianco. | Verificare che i percorsi indicati nel **passaggio 6** in [Compilare e pubblicare l'applicazione Web](#build-and-publish) siano corretti. Se l'applicazione Web non trova i file CSS, non potrà applicare lo stile corretto alla pagina.

## <a name="clean-up-resources"></a>Pulire le risorse

In questa esercitazione vengono creati ed eseguiti diversi servizi di Azure. Se non si prevede di completare questa serie di esercitazioni, è consigliabile eliminare tutte le risorse per evitare l'addebito di costi non necessari.

Nel menu a sinistra nel portale di Azure:

1. Scegliere **Gruppi di risorse** e quindi selezionare il gruppo di risorse creato per l'ambiente Time Series Insights. Nella parte superiore della pagina fare clic su **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi fare clic su **Elimina**.
1. Scegliere **Gruppi di risorse** e quindi selezionare il gruppo di risorse creato dall'acceleratore di soluzione per la simulazione del dispositivo. Nella parte superiore della pagina fare clic su **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * La progettazione dell'applicazione
> * Come registrare l'applicazione con Azure AD
> * Come creare, pubblicare e testare l'applicazione Web

Questa esercitazione si integra con Azure AD e usa l'identità dell'utente connesso per acquisire un token di accesso. Per informazioni su come accedere all'API di Time Series Insights tramite l'identità di un'applicazione di servizio o daemon, vedere questo articolo:

> [!div class="nextstepaction"]
> [Autenticazione e autorizzazione per l'API Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
