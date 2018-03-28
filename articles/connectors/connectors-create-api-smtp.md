---
title: Connettore SMTP per App per la logica di Azure | Microsoft Docs
description: Creare app per la logica con Servizio app di Azure. Connettersi a SMTP per inviare messaggi di posta elettronica.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 9bf7c9b7c3e775ab03b071d13d792f4b2d8fb3e3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-smtp-connector"></a>Introduzione al connettore SMTP
Connettersi a SMTP per inviare messaggi di posta elettronica.

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-smtp"></a>Connettersi a un server SMTP
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a un server SMTP, è necessaria prima di tutto una *connessione* SMTP. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Nell'esempio SMTP è quindi necessario immettere le credenziali per nome della connessione, indirizzo del server SMTP e informazioni sull'accesso utente per creare la connessione a SMTP.  

### <a name="create-a-connection-to-smtp"></a>Creare una connessione a SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Usare un trigger SMTP
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts).

In questo esempio SMTP non ha un proprio trigger. Usare quindi il trigger **Salesforce - Quando viene creato un oggetto**. Questo trigger viene attivato quando viene creato un nuovo oggetto in Salesforce. Per questo esempio viene impostato in modo che, ogni volta che viene creato un nuovo cliente potenziale in Salesforce, venga eseguita un'azione di *invio messaggio di posta elettronica* usando il connettore SMTP con una notifica della creazione del nuovo cliente potenziale.

1. Immettere *salesforce* nella casella di ricerca della finestra di progettazione App per la logica, quindi selezionare il trigger **Salesforce - Quando viene creato un oggetto** .  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. Viene visualizzato il controllo **Quando viene creato un oggetto** .
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Selezionare **Tipo di oggetto** e *Lead* dall'elenco di oggetti. In questo passaggio si crea un trigger che invia una notifica all'app per la logica ogni volta che viene creato un nuovo cliente potenziale in Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Il trigger è stato creato.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Usare un'azione SMTP
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Ora che il trigger è stato aggiunto, usare i passaggi seguenti per aggiungere un'azione SMTP che viene eseguita quando viene creato un nuovo cliente potenziale in Salesforce.

1. Selezionare **+ Nuovo passaggio** per aggiungere l'azione da eseguire quando viene creato un nuovo cliente potenziale.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Selezionare **Aggiungi un'azione**. Viene aperta la casella di ricerca per cercare qualsiasi azione si desideri eseguire.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Immettere *smtp* per cercare le azioni correlate a SMTP.  
4. Selezionare **SMTP - Invia messaggio di posta elettronica** come azione da eseguire quando viene creato il cliente potenziale. Si apre il blocco di controllo azione. È necessario stabilire la connessione SMTP nel blocco di progettazione se non lo si è ancora fatto.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Immettere le informazioni di posta elettronica nel blocco **SMTP - Invia messaggio di posta elettronica**.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Salvare il lavoro per attivare il flusso di lavoro.  

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/smtpconnector/).

## <a name="more-connectors"></a>Altri connettori
Tornare all' [elenco di API](apis-list.md).