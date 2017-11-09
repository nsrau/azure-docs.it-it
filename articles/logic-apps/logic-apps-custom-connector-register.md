---
title: Registrare i connettori personalizzati - App per la logica di Azure | Microsoft Docs
description: Configurare connettori personalizzati da usare in App per la logica di Azure
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9e3d88abe751b37700590cc68c458f208d5868d2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Registrare i connettori personalizzati in App per la logica di Azure

Dopo aver compilato l'API REST, aver configurato l'autenticazione e aver ottenuto il file di definizione OpenAPI o una raccolta Postman, è possibile registrare il connettore. 

## <a name="prerequisites"></a>Prerequisiti

Per registrare il connettore personalizzato, sono necessari questi elementi:

* Dettagli per la registrazione del connettore in Azure, tra cui nome, sottoscrizione di Azure, gruppo di risorse di Azure e percorso da usare

* Un file OpenAPI (Swagger) o una raccolta Postman che descrive l'API

  Per questa esercitazione, è possibile usare il [file OpenAPI di Azure Resource Manager di esempio](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

* Icona che rappresenta il connettore

* Una breve descrizione per il connettore

* La posizione dell'host per l'API

## <a name="1-create-your-connector"></a>1. Creare il connettore

1. Scegliere **Nuovo** dal menu principale di Azure nel portale di Azure. Nella casella di ricerca immettere "connettore app per la logica" come filtro e premere INVIO.

   ![Nuovo, ricerca di "connettore app per la logica"](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. Nell'elenco dei risultati scegliere **Connettore per app per la logica** > **Crea**.

   ![Creare un connettore per App per la logica](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. Specificare i dettagli per la registrazione del connettore, come descritto nella tabella. Al termine, scegliere **Aggiungi al dashboard** > **Crea**.

   ![Dettagli di un connettore personalizzato per App per la logica](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | Proprietà | Valore consigliato | Descrizione | 
   | -------- | --------------- | ----------- | 
   | **Nome** | *nome-connettore-personalizzato* | Specificare un nome per il connettore. | 
   | **Sottoscrizione** | *nome-sottoscrizione-Azure* | Selezionare la sottoscrizione di Azure. | 
   | **Gruppo di risorse** | *nome-gruppo-risorse-Azure* | Creare o selezionare un gruppo di Azure per organizzare le risorse di Azure. | 
   | **Posizione** | *area-distribuzione* | Selezionare un'area di distribuzione per il connettore. | 
   |||| 

   Dopo che Azure distribuisce il connettore, viene automaticamente visualizzato il menu del connettore personalizzato. 
   In caso contrario, scegliere il connettore personalizzato nel dashboard di Azure.

## <a name="2-define-your-connector"></a>2. Definire il connettore

Specificare ora il file OpenAPI o la raccolta Postman per creare il connettore, l'autenticazione usata dal connettore, le azioni e i trigger forniti dal connettore personalizzato e i parametri che possono essere usati dalle azioni e dai trigger.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. Specificare il file OpenAPI o la raccolta Postman per il connettore

1. Se non è già selezionato, scegliere **Connettore per app per la logica** dal menu del connettore. Scegliere **Modifica** sulla barra degli strumenti.

   ![Modificare il connettore personalizzato](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. Scegliere **Generale** per fornire i dettagli in queste tabelle per la creazione, la protezione e la definizione delle azioni e dei trigger per il connettore personalizzato.

   1. Per **Connettori personalizzati**, selezionare un'opzione per specificare il file OpenAPI (Swagger) che descrive l'API.

      ![Specificare il file OpenAPI per l'API](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | Opzione | Format |Descrizione | 
      | ------ | ------ | ----------- | 
      | **Carica un file OpenAPI** | *file-JSON-OpenAPI (Swagger)* | Passare al percorso del file OpenAPI e selezionare il file. | 
      | **Usa un URL OpenAPI** | http://*percorso-file-JSON-Swagger* | Specificare l'URL per il file OpenAPI dell'API. | 
      | **Carica Postman Collection V1** | *file-raccolta-Postman-V1-esportato* | Passare al percorso di una raccolta Postman esportata in formato V1. | 
      |||| 

   2. Per **Informazioni generali**, caricare un'icona per il connettore. 
   In genere, i campi **Descrizione**, **Host** e **URL di base** vengono completati automaticamente dal file OpenAPI. 
   In caso contrario, aggiungere queste informazioni come descritto nella tabella e scegliere **Continua**. 

      ![Dettagli del connettore](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | Opzione o impostazione | Format | Descrizione | 
      | ----------------- | ------ | ----------- | 
      | **Carica icona** | *file-PNG-o-JPG-minore-di-1-MB* | Icona che rappresenta il connettore <p>Colore: preferibilmente un logo bianco su uno sfondo a colori. <p>Dimensioni: logo di circa 160 pixel all'interno di un quadrato di 230 pixel | 
      | **Colore di sfondo dell'icona** | *codice-esadecimale-colore-icona* | <p>Colore di sfondo dell'icona corrispondente al colore di sfondo nel file di icona. <p>Formato: esadecimale. Ad esempio, #007ee5 rappresenta il colore blu. | 
      | **Descrizione** | *descrizione-connettore* | Fornire una breve descrizione del connettore. | 
      | **Host** | *host-connettore* | Specificare il dominio host per l'API Web. | 
      | **URL di base** | *URL-base-connettore* | Specificare l'URL di base per l'API Web. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Descrivere l'autenticazione usata dal connettore

1. Scegliere ora **Sicurezza** per esaminare o descrivere l'autenticazione usata dal connettore. L'autenticazione garantisce che le identità degli utenti vengano trasmesse nel modo appropriato tra il servizio e i client.

   ![Tipo di autenticazione](./media/logic-apps-custom-connector-register/security.png)

   * Se si carica un file OpenAPI, la registrazione guidata rileva automaticamente il tipo di autenticazione usato dall'API Web e immette automaticamente i valori nella sezione **Sicurezza** della procedura guidata, in base all'oggetto `securityDefinitions` nel file. Ad esempio, ecco una sezione che specifica l'uso di OAuth2.0:

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * Se il tipo di autenticazione e le proprietà non sono stati immessi automaticamente dal file OpenAPI, scegliere **Modifica** per aggiungere queste informazioni. 
   
     Ad esempio, se in precedenza è stata [configurata l'autenticazione di Azure AD in questa esercitazione](../logic-apps/custom-connector-azure-active-directory-authentication.md), sono state create app Azure AD per la protezione dell'API Web e del connettore. 
     È ora possibile specificare l'ID applicazione e la chiave client salvati in precedenza:
    
     | Impostazione | Valore consigliato | Descrizione | 
     | ------- | --------------- | ----------- | 
     | **Tipo di autenticazione** | OAuth 2.0 | | 
     | **Provider di identità** | Azure Active Directory | | 
     | **ID client** | *ID-applicazione-per-app-Azure-AD-connettore* | ID applicazione salvato in precedenza per l'app Azure AD del connettore | 
     | **Segreto client** | *chiave-client-per-app-Azure-AD-connettore* | Chiave client per l'app Azure AD del connettore | 
     | **URL di accesso** | `https://login.windows.net` | | 
     | **URL risorsa** | `https://management.core.windows.net/` | Assicurarsi di aggiungere l'URL esattamente come specificato, inclusa la barra finale. | 
     |||| 

   * Una raccolta Postman, che genera automaticamente un file OpenAPI, immette automaticamente il tipo di autenticazione *solo* quando si usano i tipi di autenticazione supportati, ad esempio OAuth 2.0 o l'autenticazione di base.

2. Per salvare il connettore dopo aver immesso le informazioni di sicurezza, nella parte superiore della pagina scegliere **Aggiorna connettore** e quindi **Continua**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Esaminare, aggiornare o definire azioni e trigger per il connettore

1. Scegliere ora **Definizione** per esaminare, modificare o definire nuovi trigger e azioni che gli utenti possono aggiungere ai propri flussi di lavoro.

   Le azioni e i trigger sono basati sulle operazioni definite nel file OpenAPI o nella raccolta Postman, che completano automaticamente la pagina **Definizione** e includono i valori di richiesta e risposta. Di conseguenza, se le operazioni necessarie sono già visualizzate, è possibile continuare con il passaggio successivo nel processo di registrazione senza apportare modifiche in questa pagina.

   ![Definizione del connettore](./media/logic-apps-custom-connector-register/definition.png)

2. Facoltativo: se si vuole modificare azioni e trigger esistenti, o aggiungerne di nuovi, continuare con questi passaggi.

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>Modificare o aggiungere azioni per il connettore

1. Per modificare un'azione esistente, scegliere il numero per l'azione. Per aggiungere un'azione non inclusa nel file OpenAPI o nella raccolta Postman, in **Azioni** scegliere **Nuova azione**.

2. In **Generale** specificare o modificare queste informazioni per l'azione:
   
   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Riepilogo** | *nome-operazione* | Titolo per l'azione | 
   | **Descrizione** | *descrizione-operazione* | Descrizione per l'azione. <p>**Suggerimento**: assicurarsi che la descrizione termini con un punto. |
   | **ID operazione** | *identificatore-operazione* | Nome univoco per identificare l'azione. Usare la notazione Camel, ad esempio "RecuperoRichiestaPull" | 
   |**Visibilità**| **nessuna**, **avanzata**, **interna** o **importante** | Visibilità agli utenti finali per l'azione. Per altre informazioni, vedere [OpenAPI extensions](../logic-apps/custom-connector-openapi-extensions.md#visibility) (Estensioni OpenAPI). | 
   |||| 

3. Definire ora la richiesta per l'azione.
  
   1. Nella sezione **Richiesta** scegliere **Importa da esempio**. 

   2. Nella pagina **Importa da esempio** incollare una richiesta di esempio. 

      In genere, le richieste di esempio sono disponibili nella documentazione dell'API, in cui è possibile ottenere informazioni per i campi **Verbo**, **URL**, **Intestazioni** e **Corpo**. Ad esempio, vedere la [documentazione dell'API Analisi del testo](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importare una richiesta di esempio](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Se si crea un connettore da una raccolta Postman, assicurarsi di rimuovere l'intestazione `Content-type` dalle azioni e dai trigger. App per la logica aggiunge automaticamente l'intestazione. Rimuovere anche le intestazioni di autenticazione definite nella sezione `Security` dalle azioni e dai trigger.

      Per informazioni sulle funzionalità OpenAPI avanzate, vedere [OpenAPI extensions for custom connectors](../logic-apps/custom-connector-openapi-extensions.md) (Estensioni OpenAPI per connettori personalizzati).

   3. Per completare la definizione della richiesta, scegliere **Importa**.

4. Definire ora la risposta per l'azione.

   1. In **Risposta** è possibile specificare una risposta predefinita. 
   Scegliere **Aggiungi risposta predefinita**.

   2. Nella pagina **Importa da esempio** incollare una risposta di esempio e quindi scegliere **Importa**.

5. Infine, in **Convalida** esaminare e correggere i possibili problemi identificati per l'azione.

#### <a name="edit-or-add-triggers-for-your-connector"></a>Modificare o aggiungere trigger per il connettore

1. Per modificare un trigger esistente, scegliere il numero per il trigger. Per aggiungere un trigger non incluso nel file OpenAPI o nella raccolta Postman, in **Trigger** scegliere **Nuovo trigger**.

2. In **Generale** specificare o modificare queste informazioni per il trigger:

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Riepilogo** | *nome-operazione* | Titolo per il trigger | 
   | **Descrizione** | *descrizione-operazione* | Descrizione per il trigger. <p>**Suggerimento**: assicurarsi che la descrizione termini con un punto. | 
   | **ID operazione** | *identificatore-operazione* | Nome univoco per identificare il trigger. Usare la notazione Camel, ad esempio: "TriggerSuEventoPushGitHub" | 
   |**Visibilità**| **nessuna**, **avanzata**, **interna** o **importante** | Visibilità agli utenti finali per il trigger. Per altre informazioni, vedere [OpenAPI extensions](../logic-apps/custom-connector-openapi-extensions.md#visibility) (Estensioni OpenAPI). | 
   | **Tipo di trigger** | **Webhook** o **Polling** | Tipo per il trigger. Ad esempio, un trigger webhook attende il verificarsi di un evento specifico prima dell'attivazione. Un trigger di polling controlla regolarmente un endpoint di servizio in base a una frequenza e a un intervallo specificati. <p>Per altre informazioni sui modelli di trigger webhook e di polling, vedere [Creare API personalizzate](../logic-apps/logic-apps-create-api-app.md). | 
   |||| 

3. Definire ora la richiesta che crea il trigger. 

   1. Nella sezione **Richiesta** scegliere **Importa da esempio**.

   2. Nella pagina **Importa da esempio** incollare una richiesta di esempio. 

      In genere, le richieste di esempio sono disponibili nella documentazione dell'API, in cui è possibile ottenere informazioni per i campi **Verbo**, **URL**, **Intestazioni** e **Corpo**. Ad esempio, vedere la [documentazione dell'API Analisi del testo](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importare una richiesta di esempio](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Se si crea un connettore da una raccolta Postman, assicurarsi di rimuovere l'intestazione `Content-type` dalle azioni e dai trigger. App per la logica aggiunge automaticamente l'intestazione. Rimuovere anche le intestazioni di autenticazione definite nella sezione `Security` dalle azioni e dai trigger.

      Per informazioni sulle funzionalità OpenAPI avanzate, vedere [OpenAPI extensions for custom connectors](../logic-apps/custom-connector-openapi-extensions.md) (Estensioni OpenAPI per connettori personalizzati).

   3. Per completare la definizione della richiesta, scegliere **Importa**. 

4. Definire ora la risposta del trigger. Nella sezione **Risposta** seguire questi passaggi a seconda del tipo di trigger:

   **Trigger webhook**
   1. In **Risposta webhook** scegliere **Importa da esempio**. 

   2. Nella pagina **Importa da esempio** incollare una risposta di esempio e quindi scegliere **Importa**. Per visualizzare una risposta di esempio, vedere le [informazioni di riferimento sull'API di GitHub per la creazione di un webhook](https://developer.github.com/v3/repos/hooks/#create-a-hook).

   3. In **Configurazione del trigger** selezionare un parametro da usare per la richiesta di creazione del webhook. App per la logica usa questo valore di parametro per immettere l'URL di callback usato dal servizio per comunicare con il trigger.

   **Trigger di polling**
   1. In **Risposta** è possibile specificare una risposta predefinita. 
   Scegliere **Aggiungi risposta predefinita**.

   2. Nella pagina **Importa da esempio** incollare una risposta di esempio e quindi scegliere **Importa**.

   3. In **Configurazione del trigger** specificare il parametro di query, il valore da passare per il parametro e un *hint del trigger* che specifica un intervallo di polling appropriato per la richiesta successiva.

5. Infine, in **Convalida** esaminare e correggere i possibili problemi identificati per il trigger.

#### <a name="review-reference-definitions-for-your-connector"></a>Esaminare le definizioni dei riferimenti per il connettore

La sezione **Riferimenti** immette automaticamente la descrizione dell'API e descrive tutti i campi di parametro cui possono fare riferimento le azioni e i trigger. 

1. In **Riferimenti** scegliere il numero per la definizione del riferimento che si vuole esaminare.

2. In **Nome** esaminare o aggiornare il nome della definizione del riferimento.

3. In **Convalida** esaminare e correggere i possibili problemi identificati per la definizione del riferimento.

## <a name="3-finish-creating-your-connector"></a>3. Completare la creazione del connettore

Quando si è pronti, scegliere **Crea** per distribuire il connettore. Se si aggiorna un connettore esistente, scegliere **Aggiorna connettore**.

Congratulazioni. Quando si crea un'app per la logica, il connettore è ora disponibile in Progettazione app per la logica e può essere aggiunto all'app per la logica.

![In Progettazione app per la logica individuare il connettore](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Condividere il connettore con altri utenti di App per la logica

I connettori personalizzati registrati ma non certificati funzionano come i connettori gestiti da Microsoft, ma sono visibili e disponibili *solo* all'utente che ha creato il connettore e a quelli che hanno lo stesso tenant di Azure Active Directory e la stessa sottoscrizione di Azure per le app per la logica nell'area in cui queste app vengono distribuite. Benché facoltativo, in alcuni scenari si potrebbe voler condividere i connettori con altri utenti. 

> [!IMPORTANT]
> Se si condivide un connettore, altri potrebbero iniziare a dipendere dal connettore. 
> ***Se si elimina il connettore, vengono eliminate anche tutte le connessioni al connettore.***
 
Per condividere il connettore con utenti esterni senza questi limiti, ad esempio con tutti gli utenti di App per la logica, Flow e PowerApps, [inviare il connettore per la certificazione Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>domande frequenti

**D:** esistono limiti per i connettori personalizzati? </br>
**R:** Sì, vedere i [limiti per i connettori personalizzati qui](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Supporto

* Per ottenere supporto per lo sviluppo e il caricamento o per richiedere funzionalità non ancora disponibili nella registrazione guidata, scrivere all'indirizzo [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). Microsoft monitora questo account per esaminare le domande e i problemi degli sviluppatori, per poi inoltrarli al team appropriato.

* Per porre una domanda, fornire una risposta o ottenere informazioni sulle attività degli altri utenti di App per la logica di Azure, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Per contribuire al miglioramento di App per la logica, è possibile votare o inviare idee nel [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passaggi successivi

* [Facoltativo: certificare il connettore](../logic-apps/custom-connector-submit-certification.md)
* [Domande frequenti sui connettori personalizzati](../logic-apps/custom-connector-faq.md)