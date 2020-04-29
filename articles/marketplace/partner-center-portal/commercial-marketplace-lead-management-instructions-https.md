---
title: Microsoft Commercial Marketplace Lead Management con HTTPS
description: Configurare la gestione dei lead di Microsoft Commercial Marketplace per un endpoint HTTPS.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 1c3337e970fdbb22cb1ed88f105d5e7798a68f74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133744"
---
# <a name="configure-lead-management-by-using-an-https-endpoint"></a>Configurare la gestione dei lead tramite un endpoint HTTPS

>[!NOTE]
>Il connettore Power automatizzate usato in queste istruzioni richiede una sottoscrizione a pagamento per l'automazione dell'energia elettrica. Prima di seguire le istruzioni riportate in questo articolo, assicurarsi di tenere conto di questo problema.

Se il sistema CRM (Customer Relationship Management) non è supportato in modo esplicito nel centro per i partner per ricevere Microsoft AppSource e i lead di Azure Marketplace, è possibile usare un endpoint HTTPS in Power automatizzate per gestire questi lead. Con un endpoint HTTPS, questi lead possono essere inviati come notifica tramite posta elettronica oppure possono essere scritti in un sistema CRM supportato da Power Automate. Le istruzioni riportate in questo articolo illustrano il processo di base per creare un nuovo flusso usando Power Automate, che genera l'URL http post che verrà immesso nel portale di pubblicazione per il campo dell'**URL dell'endpoint HTTPS** per la **gestione** > dei lead. Sono inoltre incluse istruzioni su come testare il flusso con l'ausilio di uno strumento denominato [post](https://www.getpostman.com/downloads/), disponibile online.

## <a name="create-a-flow-by-using-power-automate"></a>Creare un flusso usando Power Automate

1. Aprire la pagina Web [Power automatizzate](https://flow.microsoft.com/) . Selezionare **Accedi**. Se non si dispone già di un account, selezionare **iscrizione gratuita** per creare un account di Power automatizzato gratuito.

1. Accedere e selezionare **flussi personali** nel menu.

1. Selezionare **+ automatizzato--da zero**.

    ![Flussi personali + automatizzato--da zero](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. Nella finestra **Compila un flusso automatizzato** selezionare **Ignora**. 

    ![Pulsante per la creazione di una finestra di flusso automatica](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

1. Nel campo **Cerca connettori e trigger** immettere **richiesta** per trovare il connettore della richiesta.
1. In **Trigger** selezionare **Alla ricezione di una richiesta HTTP**. 

    ![Menu trigger](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

1. Nella finestra **quando viene ricevuta una richiesta http** , copiare e incollare lo schema JSON seguente nella casella di testo **dello schema JSON del corpo della richiesta** . Questo schema viene usato da Microsoft per contenere i dati dei lead.

    ![Casella di testo dello schema JSON del corpo della richiesta](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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

>[!NOTE]
>A questo punto della configurazione è possibile scegliere di connettersi a un sistema CRM o configurare una notifica di posta elettronica. Seguire le istruzioni rimanenti in base alla scelta effettuata.

### <a name="connect-to-a-crm-system"></a>Connettersi a un sistema CRM

1. Selezionare **+ nuovo passaggio**.
1. Scegliere il sistema CRM di propria scelta eseguendo una ricerca nel punto in cui viene indicato i **connettori e le azioni di ricerca**. Selezionarlo nella scheda **azioni** con l'azione per creare un nuovo record. La schermata seguente mostra come esempio **creare un nuovo record (Dynamics 365)** .

    ![Crea un nuovo record](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Specificare il **nome dell'organizzazione** associato al sistema CRM. Selezionare **Lead** dall'elenco a discesa **nome entità** .

    ![Selezionare i clienti potenziali](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Power automatizzate Mostra un modulo per fornire informazioni sui lead. È possibile mappare elementi dalla richiesta di input scegliendo di aggiungere contenuto dinamico. La schermata seguente mostra **OfferTitle** come esempio.

    ![Aggiungere contenuto dinamico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Eseguire il mapping dei campi desiderati e quindi selezionare **Save (Salva** ) per salvare il flusso. Un URL HTTP POST viene creato ed è accessibile nella finestra **quando viene ricevuta una richiesta http** . Copiare questo URL usando il controllo di copia, che si trova a destra dell'URL HTTP POST. L'uso del controllo Copy è importante, in modo da non perdere alcuna parte dell'intero URL. Salvare questo URL perché sarà necessario quando si configura la gestione dei lead nel portale di pubblicazione.

    ![Quando viene ricevuta una richiesta HTTP](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Configurare la notifica tramite posta elettronica

1. Ora che è stato completato lo schema JSON, selezionare **+ nuovo passaggio**.
1. In **Scegliere un'azione** selezionare **Azioni**.
1. Nella scheda **azioni** selezionare **Invia un messaggio di posta elettronica (Office 365 Outlook)**.

    >[!NOTE]
    >Se si vuole usare un provider di posta elettronica diverso, cercare e selezionare **Invia una notifica di posta elettronica (mail)** come azione.

    ![Aggiungere un'azione di posta elettronica](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. Nella finestra **Invia un messaggio di posta elettronica** configurare i campi obbligatori seguenti:

   - **Per**: immettere almeno un indirizzo di posta elettronica valido a cui verranno inviati i lead.
   - **Oggetto**: Power automatizzate offre la possibilità di aggiungere contenuto dinamico, ad esempio **LeadSource** , come illustrato nella schermata seguente. Per iniziare, immettere un nome di campo. Selezionare quindi l'elenco di selezione contenuto dinamico dalla finestra popup. 

        >[!NOTE] 
        > Quando si aggiungono i nomi dei campi, è possibile seguire ogni nome con i due punti (:) quindi premere **invio** per creare una nuova riga. Una volta aggiunti i nomi di campo, è possibile aggiungere ogni parametro associato dall'elenco di selezione dinamica.

        ![Aggiungere un'azione di posta elettronica usando il contenuto dinamico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo**: dall'elenco di selezione contenuto dinamico aggiungere le informazioni desiderate nel corpo del messaggio di posta elettronica. Usare, ad esempio, LastName, FirstName, email e Company. Al termine della configurazione della notifica di posta elettronica, l'esempio sarà simile a quello riportato nella schermata seguente.


       ![Esempio di notifica tramite posta elettronica](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Selezionare **Salva** per completare il flusso. Un URL HTTP POST viene creato ed è accessibile nella finestra **quando viene ricevuta una richiesta http** . Copiare questo URL usando il controllo di copia, che si trova a destra dell'URL HTTP POST. L'uso di questo controllo è importante per evitare di perdere parte dell'intero URL. Salvare questo URL perché sarà necessario quando si configura la gestione dei lead nel portale di pubblicazione.

   ![URL POST HTTP](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Test

È possibile verificare che tutto funzioni come previsto usando uno strumento denominato [post](https://app.getpostman.com/app/download/win64), che può essere scaricato online. Questo strumento è disponibile per Windows. 

1. Avviare il post e selezionare **nuova** > **richiesta** per configurare lo strumento di test. 

   ![Richiesta di configurazione dello strumento di test](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Compilare il modulo **Salva richiesta** e quindi salvare nella cartella creata.

   ![Salva modulo di richiesta](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Selezionare **post** nell'elenco a discesa. 

   ![Testare il flusso](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Incollare l'URL HTTP POST dal flusso creato in Power automatizzate, in cui viene indicato **immettere l'URL della richiesta**.

   ![Incollare l'URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Tornare a [Power automatizzate](https://flow.microsoft.com/). Trovare il flusso creato per inviare i lead passando a **flussi personali** dalla barra dei menu di Power Automate. Selezionare i puntini di sospensione accanto al nome del flusso per visualizzare altre opzioni e selezionare **modifica**.


1. Selezionare **test** nell'angolo in alto a destra, selezionare **eseguirò l'azione trigger**, quindi selezionare **test**. Verrà visualizzata un'indicazione nella parte superiore della schermata in cui è stato avviato il test.

   ![Eseguo l'opzione trigger Action](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Tornare all'app post, quindi selezionare **Send (Invia**).

   ![Pulsante Invia](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Tornare al flusso e verificare il risultato. Se tutto funziona come previsto, verrà visualizzato un messaggio che indica che il flusso è stato completato correttamente.

   ![Controllare i risultati](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Si dovrebbe ricevere anche un messaggio di posta elettronica. Controllare la posta in arrivo. 

    >[!NOTE] 
    >Se non viene visualizzato un messaggio di posta elettronica dal test, controllare le cartelle della posta indesiderata. Nella schermata seguente si noteranno solo le etichette di campo aggiunte al momento della configurazione della notifica tramite posta elettronica. Se si trattasse di un lead effettivo generato dall'offerta, si vedranno anche le informazioni effettive del contatto lead nel corpo e nella riga dell'oggetto.

   ![Posta elettronica ricevuta](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurare l'offerta per inviare clienti potenziali all'endpoint HTTPS

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura.

1. Passare alla pagina di **installazione dell'offerta** per l'offerta.
1. Selezionare **Connetti** nella sezione **gestione dei lead** .
1. Nella finestra popup **Dettagli connessione** selezionare **endpoint HTTPS** per la **destinazione principale**. Incollare l'URL HTTP POST dal flusso creato seguendo i passaggi precedenti nel campo **URL endpoint HTTPS** .
1. In **Contact email**immettere gli indirizzi di posta elettronica per gli utenti della società che devono ricevere notifiche tramite posta elettronica quando viene ricevuto un nuovo lead. È possibile specificare più messaggi di posta elettronica separandoli con un punto e virgola.
1. Selezionare **OK**.

Per assicurarsi di avere effettuato la connessione a una destinazione principale, selezionare il pulsante **convalida** . In caso di esito positivo, sarà presente un lead di test nella destinazione principale.

>[!NOTE] 
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

Quando vengono generati i lead, Microsoft invia lead al flusso. I lead vengono indirizzati al sistema CRM o all'indirizzo di posta elettronica configurati.

![Pulsante Connetti Gestione lead](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Destinazione lead dettagli connessione](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Indirizzo di posta elettronica contatto dettagli connessione](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

