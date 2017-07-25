---
title: Creare soluzioni B2B - App per la logica di Azure | Microsoft Docs
description: "Ricevere dati in app per la logica usando le funzionalità B2B in Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 0625787ddcbc0091e70b111f687e25929720ad15
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>Ricevere dati in app per la logica con le funzionalità B2B in Enterprise Integration Pack

Dopo aver creato un account di integrazione con partner e contratti, si è pronti per creare un flusso di lavoro Business to Business (B2B) per l'app per la logica con [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Per usare le azioni AS2 e X12, è necessario un account Enterprise Integration. Informazioni sulla [creazione di un account Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Creare un'app per la logica con connettori B2B

Seguire questa procedura per creare un'app per la logica B2B che usi le azioni AS2 e X12 per ricevere dati da un partner commerciale:

1. Creare un'app per la logica, quindi [collegare l'app al proprio account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Aggiungere un trigger **Richiesta - Alla ricezione di una richiesta HTTP** all'app per la logica.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Per aggiungere l'azione **Decodifica AS2**, selezionare **Aggiungi un'azione**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Per filtrare tutte le azioni e trovare quella desiderata, immettere **as2** nella casella di ricerca.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Selezionare l'azione **AS2 - Decode AS2 message** (AS2 - Decodifica messaggio AS2).

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Aggiungere il **corpo** che si desidera usare come input. In questo esempio selezionare il corpo della richiesta HTTP che attiva l'app per la logica. Oppure, immettere un'espressione che inserisce le intestazioni nel campo **INTESTAZIONI**:

    @triggerOutputs()['headers']

7. Aggiungere le **intestazioni** necessarie per AS2, che è possibile trovare nelle intestazioni della richiesta HTTP. In questo esempio selezionare le intestazioni della richiesta HTTP che attivano l'app per la logica.

8. A questo punto aggiungere l'azione del messaggio Decode X12. Selezionare **Aggiungi un'azione**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Per filtrare tutte le azioni e trovare quella desiderata, immettere **x12** nella casella di ricerca.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Selezionare l'azione **X12 - Decode X12 message** (X12 - Decodifica messaggio X12).

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. A questo punto è necessario specificare l'input per questa azione. Questo input è l'output dell'azione AS2 precedente.

    Il contenuto effettivo del messaggio è in un oggetto JSON e codificato in base64, pertanto è necessario specificare un'espressione come input. 
    Immettere l'espressione seguente nel campo di input **X12 FLAT FILE MESSAGE TO DECODE** (MESSAGGIO FILE FLAT X12 DA DECODIFICARE):
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    A questo punto aggiungere i passaggi per decodificare i dati X12 ricevuti dal partner commerciale e gli elementi di output in un oggetto JSON. 
    Per comunicare al partner che i dati sono stati ricevuti, è possibile inviare una risposta contenente la notifica sulla ricezione del messaggio (MDN, Message Disposition Notification) AS2 in un'azione di risposta HTTP.

12.    Per aggiungere l'azione **Risposta**, scegliere **Aggiungi un'azione**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Per filtrare tutte le azioni e trovare quella desiderata, immettere **response** nella casella di ricerca.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Selezionare l'azione **Risposta**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Per accedere all'MDN dall'output dell'azione **Decode X12 message** (Decodifica messaggio X12), impostare il campo **CORPO** ella risposta usando l'espressione seguente:

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Salvare il lavoro.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

La configurazione dell'app per la logica B2B è completata. In un'applicazione reale, si potrebbe voler archiviare i dati X12 decodificati in un'app line-of-business (LOB) o in un archivio dati. Per connettere le proprie app LOB e usare queste API nell'app per la logica, è possibile aggiungere altre azioni o scrivere API personalizzate.

## <a name="features-and-use-cases"></a>Funzionalità e casi d'uso

* Le azioni di decodifica e codifica AS2 e X12 consentono di scambiare dati tra partner commerciali tramite protocolli standard del settore in app per la logica.
* È possibile usare AS2 e X12, da soli o combinati, per scambiare dati con i partner commerciali.
* Le azioni B2B aiutano a creare facilmente partner e contratti nell'account di integrazione e a usarli in un'app per la logica.
* Quando si estende la propria app per la logica con altre azioni, è possibile inviare e ricevere dati tra altre applicazioni e altri servizi come SalesForce.

## <a name="learn-more"></a>Altre informazioni
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

