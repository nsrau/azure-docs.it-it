---
title: Gestione dei lead con un endpoint HTTPS - Marketplace commerciale Microsoft
description: Informazioni su come usare Power Automate e un endpoint HTTPS per gestire i lead da Microsoft AppSource e Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a2be74f6c9178577f86e5522e37e7c210643dd07
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847815"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Usare un endpoint HTTPS per gestire i lead del marketplace commerciale

Se il sistema CRM (Customer Relationship Management) non è supportato in modo esplicito nel Centro per i partner per la ricezione di lead di Microsoft AppSource e Azure Marketplace, è possibile gestire questi lead tramite un endpoint HTTPS in [Power Automate](https://powerapps.microsoft.com/automate-processes/). Con un endpoint HTTPS, i lead del marketplace commerciale possono essere inviati come notifica tramite posta elettronica oppure possono essere scritti in un sistema CRM supportato da Power Automate.

Questo articolo illustra come creare un nuovo flusso in Power Automate per generare l'URL POST HTTP da usare per configurare i lead nel Centro per i partner. Include anche la procedura per testare il flusso con [Postman](https://www.getpostman.com/downloads/).

>[!NOTE]
>Il connettore di Power Automate usato in queste istruzioni richiede una sottoscrizione a pagamento di Power Automate. Prima di configurare questo flusso, assicurarsi di tenere conto di questa informazione.

## <a name="create-a-flow-by-using-power-automate"></a>Creare un flusso tramite Power Automate

1. Aprire la pagina Web di [Power Automate](https://flow.microsoft.com/). Fare clic su **Accedi**. Se non è già disponibile, creare un account gratuito di Power Automate selezionando **Iscrizione gratuita**.

1. Accedere e selezionare **Flussi personali** nel menu.

    ![Accedere a Flussi personali](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. In **+ Nuovo**selezionare **+ Istantaneo - Da modello vuoto**.

    ![Flussi personali + Flusso automatizzato da modello vuoto](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Assegnare un nome al flusso, quindi in **Scegliere come attivare questo flusso** selezionare **Alla ricezione di una richiesta HTTP**.

    ![Pulsante Ignora nella finestra di creazione di un flusso automatizzato](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Fare clic sul passaggio del flusso per espanderlo.

    ![Espandere il passaggio del flusso](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Usare uno dei metodi seguenti per configurare l'opzione **Schema JSON del corpo della richiesta**:

    - Copiare lo schema JSON riportato nella casella di testo **Schema JSON del corpo della richiesta**.
    - Selezionare **Usare il payload di esempio per generare lo schema**. Nella casella di testo **Immettere o incollare un payload JSON di esempio** incollare l'esempio di JSON. Selezionare **Fine** per creare lo schema.

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

    **Esempio di JSON**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>A questo punto della configurazione è possibile selezionare di connettersi a un sistema CRM o di configurare una notifica tramite posta elettronica. Seguire le istruzioni rimanenti in base alla scelta effettuata.

### <a name="connect-to-a-crm-system"></a>Connettersi a un sistema CRM

1. Selezionare **+ nuovo passaggio**.
1. Scegliere un sistema CRM a scelta cercandolo tramite **Cerca connettori e azioni**. Selezionarlo nella scheda **Azioni** con l'azione per creare un nuovo record. La schermata seguente mostra l'azione **Crea un nuovo record (Dynamics 365)** come esempio.

    ![Crea un nuovo record](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Specificare il **Nome organizzazione** associato al sistema CRM. Selezionare **Lead** nell'elenco a discesa **Nome entità**.

    ![Selezionare i clienti potenziali](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Power Automate visualizza un modulo per l'immissione di informazioni sui lead. È possibile mappare elementi dalla richiesta di input scegliendo di aggiungere contenuto dinamico. La schermata seguente mostra **OfferTitle** come esempio.

    ![Aggiungere contenuto dinamico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Mappare i campi prescelti e quindi selezionare **Salva** per salvare il flusso. Viene creato un URL POST HTTP, accessibile nella finestra **Alla ricezione di una richiesta HTTP**. Copiare questo URL usando il controllo di copia, che si trova a destra dell'URL POST HTTP. È importante usare il controllo di copia per evitare di perdere parti dell'intero URL. Salvare questo URL perché sarà necessario quando si configura la gestione dei lead nel portale di pubblicazione.

    ![Quando viene ricevuta una richiesta HTTP](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Configurare la notifica tramite posta elettronica

1. Una volta completato lo schema JSON, selezionare **+ Nuovo passaggio**.
1. In **Scegliere un'azione** selezionare **Azioni**.
1. Nella scheda **Azioni** selezionare **Invia un messaggio di posta elettronica (Office 365 Outlook)** .

    >[!NOTE]
    >Se si vuole usare un provider di posta elettronica diverso, cercare e selezionare l'azione **Invia una notifica di posta elettronica (Mail)** .

    ![Aggiungere un'azione di posta elettronica](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. Nella finestra **Invia un messaggio di posta elettronica** configurare i campi obbligatori seguenti:

   - **A**: immettere almeno un indirizzo di posta elettronica valido a cui verranno inviati i lead.
   - **Soggetto**: Power Automate offre la possibilità di aggiungere contenuto dinamico, ad esempio **LeadSource**, come mostrato nella schermata seguente. Per iniziare, immettere un nome di campo. Selezionare quindi l'elenco di selezione di contenuto dinamico nella finestra popup. 

        >[!NOTE] 
        > Quando si aggiungono i nomi dei campi, è aggiungere due punti (:) dopo ogni nome e quindi premere **INVIO** per creare una nuova riga. Una volta aggiunti i nomi dei campi, è possibile aggiungere ogni parametro associato dall'elenco di selezione di contenuto dinamico.

        ![Aggiungere un'azione di posta elettronica usando contenuto dinamico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo**: dall'elenco di selezione di contenuto dinamico aggiungere le informazioni desiderate nel corpo del messaggio di posta elettronica, ad esempio LastName, FirstName, Email e Company. Una volta configurata, la notifica tramite posta elettronica avrà un aspetto simile a quello dell'esempio della schermata seguente.


       ![Esempio di notifica tramite posta elettronica](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Selezionare **Salva** per completare il flusso. Viene creato un URL POST HTTP, accessibile nella finestra **Alla ricezione di una richiesta HTTP**. Copiare questo URL usando il controllo di copia, che si trova a destra dell'URL POST HTTP. È importante usare questo controllo per evitare di perdere parti dell'intero URL. Salvare questo URL perché sarà necessario quando si configura la gestione dei lead nel portale di pubblicazione.

   ![URL POST HTTP](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Test

È possibile testare la configurazione con [Postman](https://app.getpostman.com/app/download/win64). È disponibile un download online di Postman per Windows. 

1. Avviare Postman e selezionare **New** > **Request** (Nuovo > Richiesta) per configurare lo strumento di test. 

   ![Richiesta di configurazione dello strumento di test](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Compilare il modulo **Save Request** (Salva richiesta), quindi salvarlo nella cartella creata.

   ![Salvare il modulo di richiesta](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Selezionare **POST** nell'elenco a discesa. 

   ![Testare il flusso personale](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Incollare l'URL POST HTTP dal flusso creato in Power Automate nel campo **Enter request URL** (Immettere l'URL della richiesta).

   ![Incollare l'URL POST HTTP](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Tornare in [Power Automate](https://flow.microsoft.com/). Trovare il flusso creato per l'invio dei lead scegliendo **Flussi personali** sulla barra dei menu di Power Automate. Selezionare i puntini di sospensione accanto al nome del flusso per visualizzare altre opzioni, quindi scegliere **Modifica**.


1. Selezionare **Test** nell'angolo in alto a destra, selezionare **Verrà eseguita l'azione del trigger** e quindi selezionare **Test**. Nella parte superiore dello schermo verrà visualizzata l'indicazione che il test è stato avviato.

   ![Opzione Verrà eseguita l'azione del trigger](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Tornare nell'app Postman e selezionare **Send** (Invia).

   ![Pulsante Send](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Tornare nel flusso e verificare il risultato. Se tutto funziona come previsto, verrà visualizzato un messaggio che indica che il flusso è stato creato correttamente.

   ![Controllare i risultati](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Si dovrebbe anche ricevere un messaggio di posta elettronica. Controllare la posta in arrivo. 

    >[!NOTE] 
    >Se il messaggio del test non è disponibile, controllare la cartella della posta indesiderata. Nella schermata seguente si noteranno solo le etichette dei campi aggiunti durante la configurazione della notifica tramite posta elettronica. Se per l'offerta venisse generato un lead effettivo, il corpo e la riga dell'oggetto conterrebbero anche le informazioni effettive del contatto del lead.

   ![Messaggio di posta elettronica ricevuto](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurare l'offerta per inviare clienti potenziali all'endpoint HTTPS

Quando si è pronti a configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura.

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).

1. Selezionare l'offerta e passare alla scheda **Configurazione dell'offerta**.

1. Nella sezione **Customer leads** (Lead clienti) selezionare **Connetti**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="Lead clienti":::

1. Nella finestra popup **Dettagli connessione** selezionare **Endpoint HTTPS** per **Destinazione del lead**. Nel campo **URL dell'endpoint HTTPS** incollare l'URL POST HTTP dal flusso creato seguendo i passaggi precedenti.
    ![Indirizzo di posta elettronica di contatto nei dettagli della connessione](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. In **Indirizzo posta elettronica contatto** immettere gli indirizzi delle persone dell'organizzazione che dovranno ricevere le notifiche tramite posta elettronica quando si riceve un nuovo lead. È possibile specificare più indirizzi di posta elettronica separandoli con un punto e virgola.

1. Selezionare **OK**.

Per assicurarsi di avere eseguito la connessione a una destinazione del lead, selezionare il pulsante **Convalida**. In caso di esito positivo, sarà presente un lead di test nella destinazione del lead.

>[!NOTE] 
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

Quando vengono generati lead, Microsoft li invia al flusso. I lead vengono indirizzati al sistema CRM o all'indirizzo di posta elettronica configurato.
