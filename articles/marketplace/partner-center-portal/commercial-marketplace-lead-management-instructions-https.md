---
title: 'Endpoint HTTPS : Azure Marketplace'
description: Configurare la gestione dei clienti potenziali per un endpoint HTTPS.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: dsindona
ms.openlocfilehash: 6a0131cf94759fc529a52ea33d5392a60c5ef30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281596"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurare la gestione dei clienti potenziali usando un endpoint HTTPS

Se il sistema CRM (Customer Relationship Management) non è supportato in modo esplicito nel Centro per i partner per la ricezione dei lead di Azure Marketplace e AppSource, è possibile usare un endpoint HTTPS in MS Flow per gestire questi lead. Con un endpoint HTTPS, questi lead possono essere inviati come notifica di posta elettronica o possono essere scritti in un sistema CRM (Customer Relationship Management) supportato da MS Flow. Le istruzioni contenute in questo articolo illustrano il processo di base per creare un nuovo flusso usando Microsoft Flow, che genererà l'URL HTTP POST che verrà immesso nel portale di pubblicazione per il campo **URL dell'endpoint HTTPS** di Lead Management >. Inoltre, sono incluse le istruzioni su come testare il flusso con l'aiuto di uno strumento chiamato [Postman](https://www.getpostman.com/downloads/) che può essere trovato online.

## <a name="create-a-flow-using-microsoft-flow"></a>Creare un flusso usando Microsoft Flow

1. Aprire la pagina Web di [Flow](https://flow.microsoft.com/). Seleziona **Accedi**o, se non hai già un account, seleziona **Iscriviti gratuitamente** per creare un account Flow gratuito.

2. Accedere e selezionare **Flussi personali** nella barra dei menu.

3. Selezionare **"Automatico - da vuoto.**

    ![I miei flussi - automatizzati - da vuoto](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Nella finestra *Crea flusso automatizzato* selezionare **Ignora**. 

    ![Crea flusso automatizzato - Salta](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. Nel campo **Cerca connettori e trigger** digitare "richiesta" per trovare il connettore di richiesta.
6. In *Trigger* selezionare **Alla ricezione di una richiesta HTTP**. 

    ![connettore di richiesta - Trigger](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. Nella casella di testo *Quando viene ricevuta una richiesta HTTP* copiare e incollare lo schema JSON riportato di seguito nella casella di testo Schema JSON corpo **richiesta.** Questo schema viene utilizzato da Microsoft per contenere i dati del lead.

    ![connettore di richiesta - Trigger](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **Schema JSON**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>A questo punto della configurazione è possibile scegliere di connettersi a un sistema CRM o configurare una notifica di posta elettronica. Seguire le istruzioni rimanenti in base alla scelta.

### <a name="to-connect-to-a-crm-system"></a>Per connettersi a un sistema CRM

1. Selezionare **+ nuovo passaggio**.
2. Scegliere il sistema CRM desiderato cercandolo nel punto in cui è indicato *Cerca connettori e azioni*e selezionarlo nella sezione *Azioni* con l'azione per creare un nuovo record. L'acquisizione schermo seguente mostra **Dynamics 365 - Crea** un nuovo record come esempio.

    ![Crea un nuovo record](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Specificare il **nome dell'organizzazione** associato al sistema CRM. Selezionare **Leads** dall'elenco a discesa **Nome entità**.

    ![Selezionare i clienti potenziali](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow visualizza un modulo per l'immissione delle informazioni sui clienti potenziali. È possibile mappare gli elementi dalla richiesta di input scegliendo di aggiungere contenuto dinamico. L'acquisizione di schermata seguente mostra **OfferTitle** come esempio.

    ![Aggiungere contenuto dinamico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Mappare i campi prescelti e quindi selezionare **Salva** per salvare il flusso. Viene creato un URL HTTP POST ed è accessibile nella finestra *Quando viene ricevuta una richiesta HTTP.* Copiare questo URL utilizzando il controllo di copia che si trova a destra dell'URL HTTP POST - questo è importante in modo da non perdere per errore qualsiasi parte dell'intero URL. Salvare questo URL perché sarà necessario quando si configura la gestione dei lead nel portale di pubblicazione.

    ![Quando viene ricevuta una richiesta HTTP.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Per configurare la notifica di posta elettronica

1. Dopo aver completato lo schema JSON, selezionare **Nuovo passaggio**.
2. In **Scegliere un'azione** selezionare **Azioni**.
3. In **Azioni**selezionare **Invia un messaggio di posta elettronica (Office 365 Outlook)**.

    >[!Note]
    >Se si desidera utilizzare una ricerca di un provider di posta elettronica diverso e selezionare Invia una *notifica tramite posta elettronica (Posta)* come azione.

    ![Aggiungere un'azione di posta elettronica](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Nella finestra **Invia un messaggio di posta elettronica** configurare i seguenti campi obbligatori:

   - **A:** immettere almeno un indirizzo di posta elettronica valido, a cui verranno inviati i lead.
   - **Oggetto**: Flow offre la possibilità di aggiungere contenuto dinamico, ad esempio **LeadSource** nell'acquisizione di schermata seguente. Iniziare digitando il nome di un campo seguito facendo clic sull'elenco di selezione Contenuto dinamico dalla finestra popup. 

        >[!Note] 
        > Quando si aggiungono nomi di campo, è possibile seguire ciascuno con un ":" e quindi INVIO per creare una nuova riga. Una volta aggiunti i nomi dei campi, è possibile aggiungere ogni parametro associato dall'elenco di selezione dinamico.

        ![Aggiungere un'azione di posta elettronica usando contenuto dinamico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo:** dall'elenco di selezione Contenuto dinamico, aggiungere le informazioni desiderate nel corpo del messaggio di posta elettronica. Ad esempio, LastName, FirstName, Email e Company. <br> <br> Al termine della configurazione, la notifica di posta elettronica, avrà un aspetto simile all'esempio nell'acquisizione di schermata seguente.


       ![Aggiungere un'azione di posta elettronica](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Selezionare **Salva** per completare il flusso. Viene creato un URL HTTP POST ed è accessibile nella finestra *Quando viene ricevuta una richiesta HTTP.* Copiare questo URL utilizzando il controllo di copia che si trova a destra dell'URL HTTP POST - questo è importante in modo da non perdere per errore qualsiasi parte dell'intero URL. Salvare questo URL perché sarà necessario quando si configura la gestione dei lead nel portale di pubblicazione.

   ![URL POST HTTP ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Test

È possibile verificare che tutto funzioni come previsto utilizzando i seguenti passaggi utilizzando uno strumento chiamato [Postman](https://app.getpostman.com/app/download/win64), che può essere scaricato online. Questa opzione è disponibile per Windows. 

1. Avviare Postman e selezionare **Nuova** > **richiesta** per impostare lo strumento di test. 

   ![Richiesta di configurazione dello strumento di test](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Compilare il modulo *Di richiesta* di salvataggio e quindi **salvare** nella cartella creata.

   ![Salvare la richiesta](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Selezionare **POST** dall'elenco a discesa. 

   ![Testare il flussoTest my flow](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Incollare l'URL HTTP POST dal flusso creato in MS Flow in cui è indicato *Immetti URL richiesta*.

   ![Incollare l'URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Tornare a [Flusso](https://flow.microsoft.com/) e trovare il flusso creato per inviare i lead, passando a **Flussi personali** dalla barra dei menu Flusso.  Selezionare i 3 puntini accanto al nome del flusso e selezionare **Modifica**.

   ![I miei flussi - Modifica](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Selezionare **Test** nell'angolo superiore destro, selezionare "Eseguirò l'azione trigger", quindi selezionare **Test**. Vedrai un'indicazione nella parte superiore dello schermo che indica che il test è iniziato

   ![Flusso di test - trigger](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Tornare all'app Postman e selezionare **Invia** accanto alla posizione in cui è stato incollato l'URL HTTPS.

   ![Testare il flusso - InviaTest my flow - Send](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Torna al tuo flusso e controlla il risultato. Se tutto funziona come previsto, verrà visualizzato un messaggio che indica che è stato eseguito correttamente.

   ![Flusso - Controlla i risultati](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Avresti dovuto anche ricevere un'e-mail. Controlla la tua casella di posta elettronica. 

    >[!Note] 
    >Se non vedi un'email del test, controlla le cartelle di spam e posta indesiderata. Qui di seguito si noterà solo le etichette dei campi che hai aggiunto quando si configura la notifica e-mail. Se si trattasse di un lead effettivo generato dall'offerta, è necessario visualizzare anche le informazioni effettive del contatto del lead nel corpo e nella riga Oggetto.

   ![E-mail ricevuta](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurare l'offerta per inviare clienti potenziali all'endpoint HTTPS

Quando si è pronti a configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, attenersi alla seguente procedura:

1. Accedi alla pagina **Configurazione offerta** per la tua offerta.
2. Selezionare **Connetti** nella sezione Gestione lead.
3. Nella finestra popup Dettagli connessione selezionare **Endpoint HTTPS** per Destinazione **lead** e incollare l'URL HTTP POST dal flusso creato seguendo i passaggi precedenti nel campo URL **endpoint HTTPS.**
4. Selezionare **Salva**. 

>[!Note] 
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

Quando vengono generati i clienti potenziali, Microsoft li invia al flusso, che viene instradato al sistema CRM o all'indirizzo e-mail configurato.

![Gestione dei lead - connessione](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Dettagli della connessione](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Dettagli della connessione](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

