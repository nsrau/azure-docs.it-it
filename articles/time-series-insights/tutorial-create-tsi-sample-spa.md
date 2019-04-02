---
title: "Esercitazione: Creare un'app Web a singola pagina Azure Time Series Insights| Microsoft Docs"
description: Informazioni su come creare un'applicazione Web a singola pagina che esegue query e il rendering di dati da un ambiente TSI.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: a9baf36221c99ece0703e2caa3f3e6c3b9364480
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312207"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Esercitazione: Creare un'app Web a singola pagina Azure Time Series Insights

Questa esercitazione guida l'utente nel processo di creazione di un'applicazione Web a pagina singola per accedere ai dati di TSI, modellata in base all'[applicazione di esempio di Time Series Insights (TSI)](https://insights.timeseries.azure.com/clientsample). In questa esercitazione si vedrà:

> [!div class="checklist"]
> * La progettazione dell'applicazione
> * Come registrare l'applicazione con Azure Active Directory (AD)
> * Come creare, pubblicare e testare l'applicazione Web 

## <a name="prerequisites"></a>Prerequisiti

Registrarsi per una [sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/), se non è ancora stato fatto. 

Sarà inoltre necessario installare Visual Studio, se non è già installato. Per questa esercitazione, è possibile [scaricare e installare la versione Community gratuita o una versione di valutazione gratuita](https://www.visualstudio.com/downloads/).

## <a name="application-design-overview"></a>Panoramica della progettazione dell'applicazione

Come accennato, l'applicazione di esempio TSI costituisce la base per la progettazione e il codice usati in questa esercitazione. Il codice include l'utilizzo della libreria JavaScript per il client Time Series Insights. La libreria per il client Time Series Insights rende disponibile un'astrazione per due categorie principali di API:

- **Metodi wrapper per chiamare le API di query di Time Series Insights**: API REST che permettono di eseguire query per dati di Time Series Insights usando espressioni basate su JSON. I metodi sono organizzati all'interno dello spazio dei nomi `TsiClient.server` della libreria.
- **Metodi per la creazione e il popolamento di diversi tipi di controllo dei grafici**: Metodi utilizzati per visualizzare i dati di Time Series Insights in una pagina Web. I metodi sono organizzati all'interno dello spazio dei nomi `TsiClient.ux` della libreria.

In questa esercitazione si useranno anche i dati dell'ambiente di Time Series Insights dell'applicazione di esempio. Per informazioni dettagliate sulla struttura dell'applicazione di esempio TSI e su come usa la libreria per il client Time Series Insights, vedere l'esercitazione [Esplorare la libreria client JavaScript per Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrare l'applicazione con Azure AD 

Prima di compilare l'applicazione, è necessario registrarla con Azure AD. La registrazione consente di configurare l'identità di un'applicazione, in modo che possa usare il supporto OAuth per il Single Sign-On. OAuth richiede che le applicazioni a pagina singola utilizzino la concessione dell'autorizzazione "implicita", che deve essere aggiornata nel manifesto dell'applicazione. Il manifesto dell'applicazione è una rappresentazione JSON della configurazione dell'identità dell'applicazione. 

1. Accedere al [portale di Azure](https://portal.azure.com) usando l'account della sottoscrizione di Azure.  
1. Selezionare la risorsa **Azure Active Directory** nel riquadro sinistro, quindi **Registrazioni per l'app** e infine **+ Registrazione nuova applicazione**:  
   
   ![Registrazione dell'applicazione con Azure AD nel portale di Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. Nella pagina **Crea** compilare i parametri obbligatori:
   
   Parametro|DESCRIZIONE
   ---|---
   **Nome** | Specificare un nome di registrazione significativo.  
   **Tipo di applicazione** | Poiché si sta creando un'applicazione Web a pagina singola, lasciare "App Web/API".
   **URL di accesso** | Immettere l'URL della home page/pagina di accesso dell'applicazione. Poiché l'applicazione verrà ospitata nel Servizio app di Azure (che si vedrà più avanti), è necessario usare un URL nel dominio "https:\//azurewebsites.net". In questo esempio il nome è basato sul nome di registrazione.

   Al termine fare clic su **Crea** per creare la registrazione della nuova applicazione.

   ![Registrazione dell'applicazione con Azure AD nel portale di Azure - creazione](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. Le applicazioni di risorse offrono API REST per l'uso da parte di altre applicazioni e sono anch'esse registrate con Azure AD. Le API offrono un accesso granulare/protetto alle applicazioni client, esponendo "ambiti". Poiché l'applicazione che si sta creando chiamerà l'API "Azure Time Series Insights", è necessario specificare l'API e l'ambito per cui sarà richiesta/concessa l'autorizzazione in fase di esecuzione. Selezionare **Impostazioni**, quindi **Autorizzazioni necessarie** e infine **+ Aggiungi**:

   ![Aggiunta di autorizzazioni Azure AD nel portale di Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. Nella pagina **Aggiungi accesso all'API** fare clic su **1 Selezionare un'API** per specificare l'API TSI. Nella pagina **Selezionare un'API** digitare "azure time" nel campo di ricerca. Selezionare quindi l'API "Azure Time Series Insights" nell'elenco dei risultati e fare clic su **Seleziona**: 

   ![Aggiunta di autorizzazioni Azure AD nel portale di Azure - API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. Ora si specificherà l'ambito dell'API. Sempre nella pagina **Aggiungi accesso all'API** fare clic su **2 Selezionare le autorizzazioni**. Nella pagina **Abilita accesso** selezionare l'ambito "Access Azure Time Series Insights service" (Accesso al servizio Azure Time Series Insights) Fare clic su **Seleziona**. Verrà nuovamente visualizzata la pagina **Aggiungi accesso all'API**. Fare clic su **Fatto**:

   ![Aggiunta di autorizzazioni Azure AD nel portale di Azure - ambito](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. Tornando alla pagina **Autorizzazioni necessarie**, si vedrà ora elencata l'API "Azure Time Series Insights". È anche necessario concedere all'applicazione l'autorizzazione preventiva di accesso all'API e all'ambito per tutti gli utenti. Fare clic sul pulsante **Concedi autorizzazioni** in alto e selezionare **Sì**:

   ![Autorizzazioni necessarie di Azure AD nel portale di Azure - consenso](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. Come accennato in precedenza, è anche necessario aggiornare il manifesto dell'applicazione. Fare clic sul nome dell'applicazione nel percorso di navigazione per tornare alla pagina **App registrata**. Selezionare **Manifesto**, modificare la proprietà `oauth2AllowImplicitFlow` su `true` e quindi fare clic su **Salva**:

   ![Aggiornamento del manifesto di Azure AD nel portale di Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. Infine, fare clic sul percorso di navigazione per tornare nuovamente alla pagina **App registrata** e copiare l'URL della **Home page** e l'**ID applicazione**. Queste proprietà dell'applicazione saranno usate in un passaggio successivo:

   ![Proprietà di Azure AD nel portale di Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>Compilare e pubblicare l'applicazione Web

1. Creare una directory dove memorizzare i file di progetto dell'applicazione. Passare a ognuno degli URL seguenti, fare clic con il pulsante destro del mouse sul collegamento "Raw" in alto a destra nella pagina e salvare la destinazione del collegamento nella directory del progetto:

   > [!NOTE]
   > A seconda del browser, può essere necessario correggere l'estensione del file (in HTML o CSS) prima di salvare il file.

   - **index.html** HTML e JavaScript per la pagina https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css:** Foglio di stile CSS: https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
1. Avviare e accedere a Visual Studio per creare un progetto per l'applicazione Web. Dal menu **File** scegliere **Apri** e quindi **Sito Web**. Nella finestra di dialogo **Apri sito Web** selezionare la directory di lavoro dove sono stati salvati i file HTML e CSS, quindi fare clic su **Apri**:

   ![Visual Studio - File, Apri sito Web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. Aprire **Esplora Soluzioni** dal menu **Visualizza** di Visual Studio. Sarà visualizzata la nuova soluzione, contenente un progetto di sito Web (icona del globo) con i file HTML e CSS:

   ![Visual Studio - Esplora soluzioni, nuova soluzione](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. Prima di poter pubblicare l'applicazione, è necessario aggiornare alcune parti del codice JavaScript in **index.html**: 

   a. In primo luogo, modificare i percorsi dei riferimenti ai file JavaScript e del foglio di stile nell'elemento `<head>`. Aprire il file **index.html** nella soluzione Visual Studio e individuare le righe di codice JavaScript che seguono. Rimuovere il commento dalle tre righe nella sezione "PROD RESOURCE LINKS" e impostare come commento le tre righe nella sezione "DEV RESOURCE LINKS":
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Il codice modificato sarà simile all'esempio seguente:

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. Modificare ora la logica del token di accesso in modo da usare la registrazione della nuova applicazione di Azure AD. Assegnare alle variabili `clientID` e `postLogoutRedirectUri` seguenti, rispettivamente, l'ID applicazione e l'URL della home page copiati nel passaggio 9 della sezione [Registrare l'applicazione con Azure AD](#register-the-application-with-azure-ad):

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Il codice modificato sarà simile all'esempio seguente:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. Al termine delle modifiche, salvare il file **index.html**.

1. Pubblicare ora l'applicazione Web nella propria sottoscrizione di Azure come servizio app di Azure:  

   > [!NOTE]
   > Molti dei campi nelle finestre di dialogo seguenti vengono popolati con i dati della sottoscrizione di Azure. Di conseguenza, può essere necessario attendere alcuni secondi il caricamento completo di ogni finestra di dialogo prima di poter continuare.  

   a. Fare clic con il pulsante destro del mouse sul nodo del progetto di sito Web in **Esplora soluzioni** e scegliere **Pubblica app Web**.  

      ![Visual Studio - Esplora soluzioni, Pubblica app Web](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. Selezionare **Servizio app di Microsoft Azure** per creare una destinazione di pubblicazione:  

      ![Visual Studio - Profilo di pubblicazione](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. Selezionare la sottoscrizione da usare per la pubblicazione dell'applicazione, quindi fare clic su "Nuovo": 

      ![Visual Studio - Profilo di pubblicazione - servizio app](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. Attendere qualche istante il caricamento di tutti i campi nella finestra di dialogo **Crea servizio app**, quindi modificare i campi seguenti:
   
      Campo | DESCRIZIONE
      ---|---
      **Nome app** | Modificarlo nel nome di registrazione dell'applicazione in Azure AD usato nel passaggio 3, in [Registrare l'applicazione con Azure AD](#register-the-application-with-azure-ad). 
      **Gruppo di risorse** | Usare il pulsante **Nuovo** per impostarlo sullo stesso valore del campo **Nome app**.
      **Piano di servizio app** | Usare il pulsante **Nuovo** per impostarlo sullo stesso valore del campo **Nome app**.

      Al termine, fare clic su **Crea**. Il pulsante **Esporta** in basso a sinistra verrà sostituito da "Distribuzione" per alcuni secondi, durante la creazione delle risorse di Azure. Dopo la creazione delle risorse, verrà nuovamente visualizzata la finestra di dialogo precedente. 

      ![Visual Studio - Profilo di pubblicazione - aggiunta di un nuovo servizio app](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. Quando viene visualizzata la finestra di dialogo **Pubblica**, verificare che **Metodo di pubblicazione** sia impostato su "Distribuzione Web". Modificare anche il campo **URL di destinazione** specificando `https` anziché `http`, in modo che corrisponda alla registrazione dell'applicazione in Azure AD. Fare quindi clic su "Pubblica" per distribuire l'applicazione Web:  

      ![Visual Studio - Pubblicazione dell'app Web - pubblicazione del servizio app](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. Nella finestra **Output** di Visual Studio verrà visualizzato un log di pubblicazione. Al termine della distribuzione, Visual Studio aprirà l'applicazione Web in una scheda del browser e chiederà l'inserimento delle credenziali di accesso. Dopo l'accesso verranno visualizzati tutti i controlli TSI popolati con i dati:  

      [![Visual Studio - Pubblicazione dell'app Web - output del log di pubblicazione](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![App TSI a pagina singola - accesso](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>risoluzione dei problemi  

Codice di errore/condizione | DESCRIZIONE
---------------------| -----------
*AADSTS50011: Nessun indirizzo di risposta registrato per l'applicazione.* | Nella registrazione in Azure AD manca la proprietà "URL di risposta". Passare alla pagina **Impostazioni** / **URL di risposta** della registrazione dell'applicazione in Azure AD. Verificare che sia presente l'**URL di accesso** specificato nel passaggio 3 della sezione [Registrare l'applicazione con Azure AD](#register-the-application-with-azure-ad). 
*AADSTS50011: L'URL di risposta specificato nella richiesta non corrisponde agli URL di risposta configurati per l'applicazione: '<Application ID GUID>'.* | Il valore di `postLogoutRedirectUri` specificato nel passaggio 4.b della sezione [Compilare e pubblicare l'applicazione Web](#build-and-publish-the-web-application) deve corrispondere al valore specificato nella proprietà **Impostazioni** / **URL di risposta** della registrazione dell'applicazione in Azure AD. Assicurarsi di cambiare anche l'**URL di destinazione** usando `https`, come indicato nel passaggio 5.e della sezione [Compilare e pubblicare l'applicazione Web](#build-and-publish-the-web-application).
L'applicazione Web viene caricata, ma la pagina di accesso contiene solo testo senza alcuno stile applicato, con uno sfondo bianco. | Verificare che i percorsi descritti nel passaggio 4.a della sezione [Compilare e pubblicare l'applicazione Web](#build-and-publish-the-web-application) siano corretti. Se l'applicazione Web non trova i file CSS, non potrà applicare lo stile corretto alla pagina.

## <a name="clean-up-resources"></a>Pulire le risorse

In questa esercitazione vengono creati ed eseguiti diversi servizi di Azure. Se non si prevede di completare questa serie di esercitazioni, è consigliabile eliminare tutte le risorse per evitare l'addebito di costi non necessari. 

Dal menu a sinistra nel portale di Azure:

1. Fare clic sull'icona **Gruppi di risorse** e quindi selezionare il gruppo di risorse creato per l'ambiente TSI. Nella parte superiore della pagina fare clic su **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi fare clic su **Elimina**. 
1. Fare clic sull'icona **Gruppi di risorse** e quindi selezionare il gruppo di risorse creato dall'acceleratore di soluzione Simulazione dispositivi. Nella parte superiore della pagina fare clic su **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi fare clic su **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * La progettazione dell'applicazione
> * Come registrare l'applicazione con Azure Active Directory (AD)
> * Come creare, pubblicare e testare l'applicazione Web 

Questa esercitazione si integra con Azure AD, usando l'identità dell'utente connesso per acquisire un token di accesso. Per informazioni su come accedere all'API TSI mediante l'identità di un'applicazione di servizio/daemon, vedere:

> [!div class="nextstepaction"]
> [Autenticazione e autorizzazione per l'API Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
