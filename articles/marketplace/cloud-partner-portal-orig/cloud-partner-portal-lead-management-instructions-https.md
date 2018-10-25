---
title: Endpoint HTTPS | Microsoft Docs
description: Configurare la gestione dei clienti potenziali per Https.
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807148"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurare la gestione dei clienti potenziali usando un endpoint HTTPS

È possibile usare un endpoint HTTPS per la gestione dei clienti potenziali di Azure Marketplace e AppSource che possono essere scritti in un sistema CRM. Questo articolo descrive come configurare la gestione dei clienti potenziali usando il servizio di automazione Microsoft Flow.


## <a name="create-a-flow-using-microsoft-flow"></a>Creare un flusso usando Microsoft Flow

1.  Aprire la pagina Web di [Flow](https://flow.microsoft.com/). Selezionare **Accesso** o **Iscrizione gratuita** per creare un account gratuito di Flow.

2.  Accedere e selezionare **Flussi personali** nella barra dei menu.

    ![Flussi personali](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  Fare clic su **Creare da zero**.

    ![Creare da zero](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  Selezionare il connettore di **richiesta/risposta** e quindi cercare il trigger di richiesta. 

    ![Creare da zero](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. Selezionare il trigger **Request**.
    ![Trigger di richiesta](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  Copiare l'**esempio di JSON** riportato alla fine di questo articolo nello **schema JSON del corpo della richiesta**.

7.  Aggiungere un nuovo passaggio e scegliere il sistema CRM preferito con l'azione per creare un nuovo record. L'acquisizione di schermata successiva illustra **Dynamics 365 - Crea un nuovo record** come esempio.

    ![Crea un nuovo record](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  Specificare gli input di connessione per il connettore e selezionare l'entità **Leads**.

    ![Selezionare i clienti potenziali](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Flow visualizza un modulo per l'immissione delle informazioni sui clienti potenziali. È possibile mappare elementi dalla richiesta di input scegliendo di aggiungere contenuto dinamico.

    ![Aggiungere contenuto dinamico](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  Mappare i campi prescelti e quindi selezionare **Salva** per salvare il flusso.

11. Viene creato un URL POST HTTP nella richiesta. Copiare questo URL e usarlo come endpoint HTTPS.

    ![URL POST HTTP](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurare l'offerta per inviare clienti potenziali all'endpoint HTTPS

Quando si configurano le informazioni sulla gestione dei clienti potenziali per l'offerta, selezionare **Endpoint HTTPS** come destinazione dei clienti potenziali e incollare l'elemento nell'URL POST HTTP copiato nel passaggio precedente.  

![Aggiungere contenuto dinamico](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

Quando vengono generati i clienti potenziali, Microsoft li invia al flusso, che viene instradato al sistema CRM configurato.


## <a name="json-example"></a>Esempio di JSON

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
