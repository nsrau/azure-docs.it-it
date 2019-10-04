---
title: Endpoint HTTPS | Azure Marketplace
description: Configurare la gestione dei clienti potenziali per un endpoint HTTPS.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 6a34bdcab5a13af682515bbae96e9a1800ccc37f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902195"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurare la gestione dei clienti potenziali usando un endpoint HTTPS

Se il sistema CRM (Customer Relationship Management) non è supportato in modo esplicito nel centro partner per la ricezione di lead di Azure Marketplace e AppSource, è possibile usare un endpoint HTTPS in MS Flow per gestire questi lead. Con un endpoint HTTPS, questi lead possono essere inviati come notifica tramite posta elettronica oppure possono essere scritti in un sistema CRM (Customer Relationship Management) supportato da Microsoft Flow. Le istruzioni riportate in questo articolo illustrano il processo di base per la creazione di un nuovo flusso con Microsoft Flow, che genererà l'URL HTTP POST che verrà immesso nel portale di pubblicazione per il campo Gestione lead > **URL endpoint HTTPS** . Sono inoltre disponibili istruzioni su come testare il flusso con l'ausilio di uno strumento denominato [postazione](https://www.getpostman.com/downloads/) , disponibile online.

## <a name="create-a-flow-using-microsoft-flow"></a>Creare un flusso usando Microsoft Flow

1. Aprire la pagina Web di [Flow](https://flow.microsoft.com/). Selezionare **Accedi**oppure, se non si dispone già di un account, selezionare **iscrizione gratuita** per creare un account di flusso gratuito.

2. Accedere e selezionare **Flussi personali** nella barra dei menu.

3. Selezionare **+ automatico-da zero**.

    ![Flussi personali + automatico-da zero](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Nella finestra *Compila un flusso automatizzato* selezionare **Ignora**. 

    ![Flusso automatizzato di compilazione-ignora](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. Nel campo **Cerca connettori e trigger** digitare "richiesta" per trovare il connettore di richiesta.
6. In *Trigger* selezionare **Alla ricezione di una richiesta HTTP**. 

    ![connettore di richiesta-trigger](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. *Quando viene ricevuta una richiesta http* , copiare e incollare lo schema JSON seguente nella casella di testo **dello schema JSON del corpo della richiesta** . Questo schema viene usato da Microsoft per contenere i dati dei lead.

    ![connettore di richiesta-trigger](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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
>A questo punto della configurazione è possibile scegliere di connettersi a un sistema CRM o configurare una notifica di posta elettronica. Seguire le istruzioni rimanenti in base alla scelta effettuata.

### <a name="to-connect-to-a-crm-system"></a>Per connettersi a un sistema CRM

1. Selezionare **+ nuovo passaggio**.
2. Scegliere il sistema CRM di propria scelta eseguendo una ricerca nel punto in cui viene indicato i *connettori e le azioni di ricerca*e selezionarlo nella sezione *azioni* con l'azione per creare un nuovo record. L'acquisizione schermo seguente mostra **Dynamics 365: creare** un nuovo record come esempio.

    ![Crea un nuovo record](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Specificare il **nome dell'organizzazione** associato al sistema CRM. Selezionare **Leads** dall'elenco a discesa **Nome entità**.

    ![Selezionare i clienti potenziali](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow visualizza un modulo per l'immissione delle informazioni sui clienti potenziali. È possibile eseguire il mapping degli elementi dalla richiesta di input scegliendo di aggiungere contenuto dinamico. L'acquisizione di schermata seguente mostra **OfferTitle** come esempio.

    ![Aggiungi contenuto dinamico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Mappare i campi prescelti e quindi selezionare **Salva** per salvare il flusso. Viene creato un URL POST HTTP ed è possibile accedervi nella finestra *quando viene ricevuta una richiesta http* . Copiare questo URL usando il controllo di copia che si trova a destra dell'URL HTTP POST. questa operazione è importante, in modo da non perdere erroneamente alcuna parte dell'intero URL. Salvare questo URL perché sarà necessario quando si configura la gestione dei lead nel portale di pubblicazione.

    ![Quando viene ricevuta una richiesta HTTP.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Per configurare la notifica di posta elettronica

1. Ora che è stato completato lo schema JSON, selezionare **+ nuovo passaggio**.
2. In **Scegliere un'azione** selezionare **Azioni**.
3. In **azioni**selezionare **Invia un messaggio di posta elettronica (Office 365 Outlook)** .

    >[!Note]
    >Se si vuole usare una ricerca diversa del provider di posta elettronica per e selezionare *Invia una notifica di posta elettronica (mail)* come azione.

    ![Aggiungere un'azione di posta elettronica](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Nella finestra **Invia un messaggio di posta elettronica** configurare i campi obbligatori seguenti:

   - **Per** immettere almeno un indirizzo di posta elettronica valido, in cui verranno inviati i lead.
   - **Oggetto**: Flow offre la possibilità di aggiungere contenuto dinamico, ad esempio **LeadSource** nell'acquisizione di schermata seguente. Per iniziare, digitare un nome di campo seguito facendo clic sull'elenco di selezione contenuto dinamico nella finestra popup. 

        >[!Note] 
        > Quando si aggiungono i nomi dei campi, è possibile seguire ognuno con ":" e quindi immettere per creare una nuova riga. Una volta aggiunti i nomi di campo, è possibile aggiungere ogni parametro associato dall'elenco di selezione dinamica.

        ![Aggiungere un'azione di posta elettronica usando contenuto dinamico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo** : dall'elenco di selezione contenuto dinamico aggiungere le informazioni desiderate nel corpo del messaggio di posta elettronica. Ad esempio, LastName, FirstName, Email e Company. <br> <br> Al termine della configurazione, la notifica di posta elettronica, avrà un aspetto simile all'esempio nell'acquisizione di schermata seguente.


       ![Aggiungere un'azione di posta elettronica](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Selezionare **Salva** per completare il flusso. Un URL HTTP POST viene creato ed è accessibile nella finestra *quando viene ricevuta una richiesta http* . Copiare questo URL usando il controllo di copia che si trova a destra dell'URL HTTP POST. questa operazione è importante, in modo da non perdere erroneamente alcuna parte dell'intero URL. Salvare questo URL perché sarà necessario quando si configura la gestione dei lead nel portale di pubblicazione.

   ![URL POST HTTP ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Test

È possibile verificare che tutto funzioni come previsto attenendosi alla procedura seguente usando uno strumento denominato [post](https://app.getpostman.com/app/download/win64), che può essere scaricato online. Disponibile per Windows. 

1. Avviare il post e selezionare **nuova** > **richiesta** per configurare lo strumento di test. 

   ![Richiedere la configurazione dello strumento di test](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Compilare il modulo *Salva richiesta* e quindi **salvarlo** nella cartella creata.

   ![Salva richiesta](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Selezionare **post** nell'elenco a discesa. 

   ![Testare il flusso](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Incollare l'URL HTTP POST dal flusso creato in MS Flow in cui è indicato *immettere l'URL della richiesta*.

   ![Incollare l'URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Tornare a [Flow](https://flow.microsoft.com/) e trovare il flusso creato per inviare i lead, passando a **flussi personali** dalla barra dei menu di Flow.  Selezionare i 3 puntini accanto al nome del flusso e selezionare **modifica**.

   ![Flussi personali-modifica](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Selezionare **test** nell'angolo in alto a destra, selezionare "eseguirò l'azione trigger" e selezionare **test**. Nella parte superiore della schermata viene visualizzata un'indicazione che indica che il test è stato avviato

   ![Flusso di test-trigger](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Tornare all'app postazione e selezionare **Invia** accanto al percorso in cui è stato incollato l'URL HTTPS.

   ![Testare il flusso-invio](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Tornare al flusso e verificare il risultato. Se tutto funziona come previsto, verrà visualizzato un messaggio che indica che l'operazione è riuscita.

   ![Risultati del controllo di flusso](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Si dovrebbe ricevere anche un messaggio di posta elettronica. Controllare la posta in arrivo. 

    >[!Note] 
    >Se non viene visualizzato un messaggio di posta elettronica dal test, controllare le cartelle della posta indesiderata. Di seguito si noteranno solo le etichette dei campi aggiunti durante la configurazione della notifica di posta elettronica. Se si trattasse di un lead effettivo generato dall'offerta, si vedranno anche le informazioni effettive del contatto lead nel corpo e nella riga dell'oggetto.

   ![Posta elettronica ricevuta](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurare l'offerta per inviare clienti potenziali all'endpoint HTTPS

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura:

1. Passare alla pagina di **configurazione dell'offerta** per l'offerta.
2. Selezionare **Connetti** nella sezione gestione dei lead.
3. Nella finestra popup Dettagli connessione selezionare **endpoint HTTPS** per la **destinazione lead** e incollare l'URL http post dal flusso creato seguendo i passaggi precedenti nel campo **URL endpoint HTTPS** .
4. Selezionare **Salva**. 

>[!Note] 
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

Quando vengono generati i clienti potenziali, Microsoft li invia al flusso, che viene instradato al sistema CRM o all'indirizzo e-mail configurato.

![Gestione dei lead-connessione](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Dettagli connessione](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Dettagli connessione](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

