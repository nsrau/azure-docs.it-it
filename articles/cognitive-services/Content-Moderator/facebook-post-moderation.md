---
title: Moderazione del contenuto di Facebook con Azure Content Moderator | Microsoft Docs
description: Moderare le pagine di Facebook con Content Moderator basato su Machine Learning
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 4570b514bef33c8dfa6d220ee4cd88ad068ba4d0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "41936738"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Moderazione del contenuto di Facebook con Content Moderator

Questa esercitazione illustra come usare Content Moderator basato su Machine Learning per moderare post e commenti di Facebook.

Questa esercitazione illustra i passaggi seguenti:

1. Creare un team di Content Moderator.
2. Creare Funzioni di Azure in ascolto di eventi HTTP da Content Moderator e Facebook.
3. Creare una pagina e un'app Facebook e connetterle a Content Moderator.

Al termine, Facebook invierà il contenuto pubblicato dai visitatori in Content Moderator. In base alle soglie di corrispondenza, i flussi di lavoro di Content Moderator pubblicano il contenuto o creano revisioni con lo strumento apposito. 

La figura seguente illustra i principali componenti della soluzione.

![Moderazione di post di Facebook](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Creare un team di Content Moderator

Fare riferimento alla pagina [Introduzione](quick-start.md) per iscriversi a Content Moderator e creare un team.

## <a name="configure-image-moderation-workflow-threshold"></a>Configurare il flusso di lavoro di moderazione per le immagini (soglia)

Fare riferimento alla pagina [Flussi di lavoro](review-tool-user-guide/workflows.md) per configurare un flusso di lavoro per immagini personalizzato (soglia). Prendere nota del **nome** del flusso di lavoro.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Configurare il flusso di lavoro di moderazione per il testo (soglia)

Usare una procedura simile a quella descritta nella pagina [Flussi di lavoro](review-tool-user-guide/workflows.md) per configurare una soglia e un flusso di lavoro personalizzati per il testo. Prendere nota del **nome** del flusso di lavoro.

![Configurare il flusso di lavoro per il testo](images/text-workflow-configure.PNG)

Testare il flusso di lavoro usando il pulsante "Execute Workflow" (Esegui flusso di lavoro).

![Test del flusso di lavoro per il testo](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Creare funzioni di Azure

Accedere al [portale di gestione di Azure](https://portal.azure.com/) per creare le funzioni di Azure. A tale scopo, seguire questa procedura:

1. Creare un'app per le funzioni di Azure come mostrato nella pagina [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Aprire l'app per le funzioni appena creata.
3. All'interno dell'app, passare a **Funzionalità della piattaforma -> Impostazioni applicazione**
4. Definire le [impostazioni dell'applicazione](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings) seguenti:

> [!NOTE]
> **cm: Region** deve essere il nome dell'area (senza spazi).
> Ad esempio **westeurope** e non Europa occidentale, **westcentralus** e non Stati Uniti centro-occidentali e così via.
>

| Impostazione app | DESCRIZIONE   | 
| -------------------- |-------------|
| cm:TeamId   | ID del team di Content Moderator  | 
| cm:SubscriptionKey | Chiave di sottoscrizione di Content Moderator - Vedere [Credenziali](/review-tool-user-guide/credentials.md) | 
| cm:Region | Nome dell'area di Content Moderator, senza gli spazi. Vedere la nota precedente. |
| cm:ImageWorkflow | Nome del flusso di lavoro da eseguire sulle immagini |
| cm:TextWorkflow | Nome del flusso di lavoro da eseguire sul testo |
| cm:CallbackEndpoint | URL per l'app per le funzioni CMListener creata più avanti in questa guida |
| fb:VerificationToken | Token segreto, usato anche per sottoscrivere eventi di feed di Facebook |
| fb:PageAccessToken | Il token di accesso per l'API Graph di Facebook non scade e permette alla funzione di nascondere/eliminare post per conto dell'utente. |

5. Creare una nuova funzione **HttpTrigger CSharp** denominata **FBListener**. Questa funzione riceve eventi da Facebook. Creare la funzione seguendo questa procedura:

    1. Mantenere la pagina [Creare un'app per le funzioni](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) aperta per riferimento.
    2. Fare clic su **+** (Aggiungi) per creare una nuova funzione.
    3. Invece dei modelli predefiniti, scegliere l'opzione **Iniziare da zero/funzione personalizzata**.
    4. Fare clic sul riquadro che indica **HttpTrigger-CSharp**.
    5. Immettere il nome **FBListener**. Il campo **Livello di autorizzazione** deve essere impostato su **Funzione**.
    6. Fare clic su **Create**(Crea).
    7. Sostituire il contenuto di **run.csx** con il contenuto da [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Creare una nuova funzione **HttpTrigger CSharp** denominata **CMListener**. Questa funzione riceve eventi da Content Moderator. Seguire questa procedura per creare la funzione.

    1. Mantenere la pagina [Creare un'app per le funzioni](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) aperta per riferimento.
    2. Fare clic su **+** (Aggiungi) per creare una nuova funzione.
    3. Invece dei modelli predefiniti, scegliere l'opzione **Iniziare da zero/funzione personalizzata**.
    4. Fare clic sul riquadro che indica **HttpTrigger-CSharp**
    5. Immettere il nome **CMListener**. Il campo **Livello di autorizzazione** deve essere impostato su **Funzione**.
    6. Fare clic su **Create**(Crea).
    7. Sostituire il contenuto di **run.csx** con il contenuto da [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Configurare la pagina e l'app Facebook
1. Creare un'app Facebook.

    1. Passare al [sito per sviluppatori di Facebook](https://developers.facebook.com/)
    2. Fare clic su **My apps** (App personali).
    3. Aggiungere una nuova app.
    4. Selezionare **Webhooks -> Get Started** (Webhook -> Inizia)
    5. Selezionare **Page -> Subscribe to this topic** (Pagina -> Sottoscrivi questo argomento)
    6. Specificare l'**URL di FBListener** come URL di callback e il **token di verifica** configurato in **Impostazioni dell'app per le funzioni**
    7. Dopo aver eseguito la sottoscrizione, scorrere fino al feed e selezionare **subscribe** (sottoscrivi).

2. Creare una pagina Facebook.

    1. Passare a [Facebook](https://www.facebook.com/bookmarks/pages) e creare una **nuova pagina Facebook**.
    2. Consentire all'app Facebook di accedere a questa pagina seguendo questa procedura:
        1. Passare a [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        2. Selezionare **Application** (Applicazione).
        3. Selezionare **Page Access Token** (Token di accesso pagina) e inviare una richiesta **Get**.
        4. Fare clic sull'**ID di pagina** nella risposta.
        5. Ora aggiungere **/subscribed_apps** all'URL e inviare una richiesta **Get** (risposta vuota).
        6. Inviare una richiesta **Post**. Si ottiene la risposta **success: true**.

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

L'operazione è terminata.

La soluzione invia tutte le immagini e il testo pubblicati nella pagina di Facebook a Content Moderator. Vengono richiamati i flussi di lavoro configurati in precedenza. Per il contenuto che non supera i criteri definiti nei flussi di lavoro viene richiesta la revisione nello strumento apposito. Il resto del contenuto viene pubblicato.

## <a name="license"></a>Licenza

Tutti gli SDK e gli esempi di Servizi cognitivi Microsoft sono concessi su licenza con la licenza MIT. Per altre informazioni, vedere la [licenza](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Codice di comportamento per gli sviluppatori

Gli sviluppatori che usano Servizi cognitivi, compresi questo esempio e questa libreria client, sono tenuti a rispettare il "Codice di comportamento per lo sviluppatore per Servizi cognitivi Microsoft", disponibile alla pagina http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Passaggi successivi

1. [Guardare una demo (video)](https://channel9.msdn.com/Events/Build/2017/T6033) di questa soluzione da Microsoft Build 2017.
1. [Esempio di Facebook su Github](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
