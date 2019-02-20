---
title: Endpoint HTTPS | Microsoft Docs
description: Configurare la gestione dei clienti potenziali per un endpoint HTTPS.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pbutlerm
ms.openlocfilehash: 8898149e6858c5a1cdb2d4510ad2764ffe25fda5
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964254"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurare la gestione dei clienti potenziali usando un endpoint HTTPS

È possibile usare un endpoint HTTPS per la gestione dei clienti potenziali di Azure Marketplace e AppSource. Questi clienti potenziali possono essere scritti in un sistema CRM (Customer Relationship Management) o inviati come una notifica di posta elettronica. Questo articolo descrive come configurare la gestione dei clienti potenziali usando il servizio di automazione [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/).

## <a name="create-a-flow-using-microsoft-flow"></a>Creare un flusso usando Microsoft Flow

1. Aprire la pagina Web di [Flow](https://flow.microsoft.com/). Selezionare **Accesso** o **Iscrizione gratuita** per creare un account gratuito di Flow.

2. Accedere e selezionare **Flussi personali** nella barra dei menu.

    ![Flussi personali](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Selezionare **+ Crea da zero**.

    ![Creare da zero](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Fare clic su **Creare da zero**.

    ![Creare da zero](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. Nel campo **Cerca connettori e trigger** digitare "richiesta" per trovare il connettore di richiesta.
6. In **Trigger** selezionare **Alla ricezione di una richiesta HTTP**. 

    ![Selezionare il trigger di ricezione richiesta HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Usare uno dei passaggi seguenti per configurare lo **schema JSON del corpo della richiesta**:

   - Copiare lo [schema JSON](#JSON-schema) riportato alla fine di questo articolo nella casella di testo **Schema JSON del corpo della richiesta**.
   - Selezionare **Usare il payload di esempio per generare lo schema**. Nella casella di testo **Immettere o incollare un payload JSON di esempio** incollare l'[esempio JSON](#JSON-example). Selezionare **Fine** per creare lo schema.

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

Quando si configurano le informazioni sulla gestione dei clienti potenziali per l'offerta, selezionare **Endpoint HTTPS** come **destinazione** dei clienti potenziali e incollare l'URL POST HTTP copiato nel passaggio precedente.  

![Aggiungere contenuto dinamico](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Quando vengono generati i clienti potenziali, Microsoft li invia al flusso, che viene instradato al sistema CRM o all'indirizzo e-mail configurato.

## <a name="json-schema-and-example"></a>Esempio e schema JSON

Nell'esempio di test JSON viene usato lo schema seguente:

### <a name="json-schema"></a>Schema JSON

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
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

È possibile copiare e modificare l'esempio JSON seguente e usarlo come test in Microsoft Flow.

### <a name="json-example"></a>Esempio di JSON

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, configurare i [lead](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) relativi ai clienti nel portale Cloud Partner.
