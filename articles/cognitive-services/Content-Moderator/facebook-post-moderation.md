---
title: 'Esercitazione: Moderare il contenuto di Facebook - Content Moderator'
titleSuffix: Azure Cognitive Services
description: Questa esercitazione illustra come usare Content Moderator basato su Machine Learning per moderare post e commenti di Facebook.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 5aa4cc24484a4ba1da608da9676ade492db35b6c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72936016"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Esercitazione: Moderare i post e i comandi di Facebook con Azure Content Moderator

Questa esercitazione illustra come usare Azure Content Moderator per moderare post e commenti su una pagina di Facebook. Facebook invierà il contenuto pubblicato dai visitatori nel servizio Content Moderator. Successivamente, i flussi di lavoro di Content Moderator pubblicheranno il contenuto o creeranno revisioni con lo strumento di revisione, a seconda dei punteggi e delle soglie del contenuto. Vedere il [video dimostrativo di Build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) per un esempio pratico di questo scenario.

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un team di Content Moderator.
> * Creare Funzioni di Azure in ascolto di eventi HTTP da Content Moderator e Facebook.
> * Collegare una pagina di Facebook a Content Moderator usando un'applicazione Facebook.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Questo diagramma illustra ogni componente di questo scenario:

![Diagramma di Content Moderator che riceve informazioni provenienti da Facebook mediante "FBListener" e che invia informazioni tramite "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> Nel 2018, Facebook ha implementato una verifica più rigorosa delle app Facebook. Se l'app non è stata revisionata e approvata dal team di revisione di Facebook, non sarà possibile completare i passaggi di questa esercitazione.

## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione di Content Moderator. Seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere il servizio Content Moderator e ottenere la chiave.
- Un [account di Facebook](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Creare un team di revisione

Fare riferimento all'argomento di avvio rapido [Provare Content Moderator sul Web](quick-start.md) per istruzioni su come registrarsi per lo [strumento di revisione di Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e creare un team di revisione. Prendere nota del valore **ID Team** nella pagina **Credenziali**.

## <a name="configure-image-moderation-workflow"></a>Configurare il flusso di lavoro di moderazione per le immagini

Fare riferimento alla guida [Definire, testare e usare i flussi di lavoro](review-tool-user-guide/workflows.md) per creare un flusso di lavoro delle immagini personalizzato. Content Moderator userà questo flusso di lavoro per controllare automaticamente le immagini su Facebook e inviarne alcune allo strumento di revisione. Prendere nota del **nome** del flusso di lavoro.

## <a name="configure-text-moderation-workflow"></a>Configurare il flusso di lavoro moderazione testo

Fare di nuovo riferimento alla guida [Definire, testare e usare i flussi di lavoro](review-tool-user-guide/workflows.md) per creare un flusso di lavoro di testo personalizzato. Content Moderator userà questo flusso di lavoro per verificare automaticamente il contenuto del testo. Prendere nota del **nome** del flusso di lavoro.

![Configurare il flusso di lavoro per il testo](images/text-workflow-configure.PNG)

Testare il flusso di lavoro usando il pulsante **Esecuzione flusso di lavoro**.

![Test del flusso di lavoro per il testo](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Creare funzioni di Azure

Accedere al [portale di Azure](https://portal.azure.com/) e seguire questi passaggi:

1. Creare un'app per le funzioni di Azure come mostrato nella pagina [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Andare all'app per le funzioni appena creata.
1. All'interno dell'app passare alla scheda **Funzionalità della piattaforma** e selezionare **Configurazione**. Nella sezione **Impostazioni applicazione** della pagina successiva selezionare **Nuova impostazione applicazione** per aggiungere le seguenti coppie chiave-valore:
    
    | Nome impostazione app | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | ID del team di Content Moderator  | 
    | cm:SubscriptionKey | Chiave di sottoscrizione di Content Moderator - Vedere [Credenziali](review-tool-user-guide/credentials.md) |
    | cm:Region | Nome dell'area di Content Moderator, senza gli spazi. È possibile trovarlo nel campo **Località** della scheda **Panoramica** della risorsa di Azure.|
    | cm:ImageWorkflow | Nome del flusso di lavoro da eseguire sulle immagini |
    | cm:TextWorkflow | Nome del flusso di lavoro da eseguire sul testo |
    | cm:CallbackEndpoint | URL dell'app per le funzioni CMListener che verrà creata più avanti in questa guida |
    | fb:VerificationToken | Un token segreto che viene creato e usato per sottoscrivere eventi di feed di Facebook |
    | fb:PageAccessToken | Il token di accesso per l'API Graph di Facebook non scade e permette alla funzione di nascondere/eliminare post per conto dell'utente. Questo token verrà recuperato in un passaggio successivo. |

    Fare clic sul pulsante **Salva** nella parte superiore della pagina.

1. Tornare nella scheda **Funzionalità della piattaforma**. Usare il pulsante **+** nel riquadro sinistro per visualizzare il riquadro **Nuova funzione**. La funzione che si sta per creare riceverà eventi da Facebook.

    ![Riquadro Funzioni di Azure con il pulsante Aggiungi funzione evidenziato.](images/new-function.png)

    1. Fare clic sul riquadro che indica **Http trigger**.
    1. Immettere il nome **FBListener**. Il campo **Livello di autorizzazione** deve essere impostato su **Funzione**.
    1. Fare clic su **Create**(Crea).
    1. Sostituire i contenuti di **run.csx** con i contenuti da **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Creare una nuova funzione **Http trigger** nominata **CMListener**. Questa funzione riceve eventi da Content Moderator. Sostituire i contenuti di **run.csx** con i contenuti da **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Configurare la pagina e l'app Facebook

1. Creare un'app Facebook.

    ![pagina per sviluppatori di facebook](images/facebook-developer-app.png)

    1. Passare al [sito per sviluppatori di Facebook](https://developers.facebook.com/)
    1. Fare clic su **My apps** (App personali).
    1. Aggiungere una nuova app.
    1. assegnare un nome
    1. Selezionare **Webhook -> Configurare**
    1. Selezionare **Pagina** nel menu a discesa e selezionare **Sottoscrivi a questo oggetto**
    1. Specificare l'**URL di FBListener** come URL di callback e il **token di verifica** configurato in **Impostazioni dell'app per le funzioni**
    1. Dopo aver eseguito la sottoscrizione, scorrere fino al feed e selezionare **subscribe** (sottoscrivi).
    1. Fare clic sul pulsante **Test** della riga di **feed** per inviare un messaggio di testo alla funzione di Azure FBListener, quindi selezionare il pulsante **Send to My Server** (Invia al mio server). Dovrebbe essere visualizzata la richiesta ricevuta nel FBListener.

1. Creare una pagina Facebook.

    > [!IMPORTANT]
    > Nel 2018, Facebook ha implementato una verifica più rigorosa delle app Facebook. Se l'app non è stata revisionata e approvata dal team di revisione di Facebook, non sarà possibile eseguire le sezioni 2, 3 e 4.

    1. Passare a [Facebook](https://www.facebook.com/bookmarks/pages) e creare una **nuova pagina Facebook**.
    1. Consentire all'app Facebook di accedere a questa pagina seguendo questa procedura:
        1. Passare a [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        1. Selezionare **Application** (Applicazione).
        1. Selezionare **Page Access Token** (Token di accesso pagina) e inviare una richiesta **Get**.
        1. Fare clic sull'**ID di pagina** nella risposta.
        1. Ora aggiungere **/subscribed_apps** all'URL e inviare una richiesta **Get** (risposta vuota).
        1. Inviare una richiesta **Post**. Si ottiene la risposta **success: true**.

3. Creare un token di accesso per l'API Graph senza scadenza.

    1. Passare a [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Selezionare l'opzione **Application**.
    3. Selezionare l'opzione **Get User Access Token** (Ottieni token di accesso utente).
    4. In **Select Permissions** (Seleziona autorizzazioni) selezionare le opzioni **manage_pages** e **publish_pages**.
    5. Il **token di accesso** (token di breve durata) verrà usato nel passaggio successivo.

4. Per i pochi passaggi successivi si userà Postman.

    1. Aprire **Postman** (o scaricarlo [qui](https://www.getpostman.com/)).
    2. Importare questi due file:
        1. [Raccolta di Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Ambiente Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Aggiornare queste variabili di ambiente:
    
        | Chiave | Valore   | 
        | -------------------- |-------------|
        | appId   | Inserire qui l'identificatore dell'app Facebook  | 
        | appSecret | Inserire qui il segreto dell'app Facebook | 
        | short_lived_token | Inserire il token di accesso utente di breve durata generato nel passaggio precedente |
    4. A questo punto eseguire le 3 API elencate nella raccolta: 
        1. Selezionare **Generate Long-Lived Access Token** (Genera token di accesso di lunga durata) e fare clic su **Send** (Invia).
        2. Selezionare **Get User ID** (Ottieni ID utente) e fare clic su **Send** (Invia).
        3. Selezionare **Get Permanent Page Access Token** (Ottieni token di accesso pagina permanente) e fare clic su **Send** (Invia).
    5. Copiare il valore di **access_token** dalla risposta e assegnarlo all'impostazione dell'app **fb:PageAccessToken**.

La soluzione invia tutte le immagini e il testo pubblicati nella pagina di Facebook a Content Moderator. Vengono richiamati i flussi di lavoro configurati in precedenza. Il contenuto che non supera i criteri definiti nei flussi di lavoro viene trasmesso alla revisione nello strumento apposito. Il resto del contenuto viene pubblicato automaticamente.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è configurato un programma per analizzare immagini di prodotti allo scopo di assegnarvi tag corrispondenti al tipo di prodotto, consentendo a un team di revisione di prendere decisioni informate sulla moderazione dei contenuti. Nel prossimo articolo si apprenderanno altre informazioni sulla moderazione di immagini.

> [!div class="nextstepaction"]
> [Moderazione immagini](./image-moderation-api.md)
