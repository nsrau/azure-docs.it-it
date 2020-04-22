---
title: Configurare la gestione dei lead utilizzando un endpoint HTTPS. Azure Marketplace
description: Informazioni su come usare un endpoint HTTP per gestire i lead di Microsoft AppSource e Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770146"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurare la gestione dei clienti potenziali usando un endpoint HTTPS

È possibile usare un endpoint HTTPS per gestire i lead di Microsoft AppSource e Azure Marketplace.You can use an HTTPS endpoint to handle Microsoft AppSource and Azure Marketplace leads. Questi lead possono essere scritti in un sistema CRM (Customer Relationship Management) o inviati come notifica di posta elettronica. In questo articolo viene descritto come utilizzare il servizio di automazione [Microsoft Power Automate](https://powerapps.microsoft.com/automate-processes/) per configurare la gestione dei lead.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Creare un flusso con Microsoft Power AutomateCreate a flow using Microsoft Power Automate

1. Aprire la pagina Web [Power Automate.](https://flow.microsoft.com/) Selezionare **Accesso** o **Iscrizione gratuita** per creare un account gratuito di Flow.

1. Accedere e selezionare **Flussi personali** nella barra dei menu.
    > [!div class="mx-imgBorder"]
    > ![Flussi personali](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. In **Nuovo**, selezionare **"Istantaneo" dal campo vuoto.**
    > [!div class="mx-imgBorder"]
    > ![Creare da zero](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Assegnare un nome al flusso e quindi in **Scegliere come attivare il flusso**selezionare Quando viene ricevuta una richiesta **HTTP**.

    > [!div class="mx-imgBorder"]
    > ![Selezionare il trigger di ricezione richiesta HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Fare clic sul passaggio del flusso per espanderlo.

    > [!div class="mx-imgBorder"]
    > ![Espandere il passaggio del flusso](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Utilizzare uno dei metodi seguenti per configurare lo schema JSON del corpo della **richiesta:**

   - Copiare lo [schema JSON](#json-schema) riportato alla fine di questo articolo nella casella di testo **Schema JSON del corpo della richiesta**.
   - Selezionare **Usare il payload di esempio per generare lo schema**. Nella casella di testo **Immettere o incollare un payload JSON di esempio** incollare l'[esempio JSON](#json-example). Selezionare **Fine** per creare lo schema.

   >[!Note]
   >A questo punto nel flusso è possibile connettersi a un sistema CRM o configurare una notifica di posta elettronica.

### <a name="to-connect-to-a-crm-system"></a>Per connettersi a un sistema CRM

1. Selezionare **+ nuovo passaggio**.
2. Scegliere il sistema CRM preferito con l'azione per creare un nuovo record. L'acquisizione di schermata successiva illustra **Dynamics 365 - Crea un nuovo record** come esempio.

    ![Crea un nuovo record](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Inserire il **Nome organizzazione** per specificare gli input di connessione per il connettore. Selezionare **Leads** dall'elenco a discesa **Nome entità**.

    ![Selezionare i clienti potenziali](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow visualizza un modulo per l'immissione delle informazioni sui clienti potenziali. È possibile mappare elementi dalla richiesta di input scegliendo di aggiungere contenuto dinamico. L'acquisizione di schermata seguente mostra **OfferTitle** come esempio.

    ![Aggiungere contenuto dinamico](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mappare i campi prescelti e quindi selezionare **Salva** per salvare il flusso.

6. Viene creato un URL POST HTTP nella richiesta. Copiare questo URL e usarlo come endpoint HTTPS.

    ![URL POST HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Per configurare la notifica di posta elettronica

1. Selezionare **+ nuovo passaggio**.
2. In **Scegliere un'azione** selezionare **Azioni**.
3. In **Azioni** selezionare **Invia un messaggio di posta elettronica**.

    ![Aggiungere un'azione di posta elettronica](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. In **Invia un messaggio di posta elettronica** configurare i campi obbligatori seguenti:

   - **A**: immettere almeno un indirizzo di posta elettronica valido.
   - **Oggetto**: Flow offre la possibilità di aggiungere contenuto dinamico, ad esempio **LeadSource** nell'acquisizione di schermata seguente.

     ![Aggiungere un'azione di posta elettronica usando contenuto dinamico](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Corpo** : dall'elenco Contenuto dinamico aggiungere le informazioni desiderate nel corpo del messaggio di posta elettronica. Ad esempio, LastName, FirstName, Email e Company.

   Al termine della configurazione, la notifica di posta elettronica, avrà un aspetto simile all'esempio nell'acquisizione di schermata seguente.

   ![Aggiungere un'azione di posta elettronica](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Selezionare **Salva** per completare il flusso.

6. Viene creato un URL POST HTTP nella richiesta. Copiare questo URL e usarlo come endpoint HTTPS.

    ![URL POST HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurare l'offerta per inviare clienti potenziali all'endpoint HTTPS

Quando si configurano le informazioni di gestione dei lead per l'offerta, selezionare **Endpoint HTTPS** per destinazione **lead** e incollare l'URL HTTP POST copiato nel passaggio precedente.  

![Aggiungere contenuto dinamico](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Quando vengono generati lead, Microsoft invia lead al flusso di Power Automate, che vengono instradati al sistema CRM o all'indirizzo di posta elettronica configurato.

## <a name="json-schema-and-example"></a>Esempio e schema JSON

Nell'esempio di test JSON viene usato lo schema seguente:

### <a name="json-schema"></a>Schema JSON

``` json
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
    "Description": {
      "id": "/properties/Description",
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

È possibile copiare e modificare l'esempio JSON seguente da usare come test nel flusso.

### <a name="json-example"></a>Esempio di JSON

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

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, configurare i [lead](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) relativi ai clienti nel portale Cloud Partner.
