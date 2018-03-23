---
title: Creare partner per i messaggi Business to Business (B2B) - App per la logica di Azure | Documentazione Microsoft
description: Informazioni su come aggiungere partner al proprio account di integrazione con Enterprise Integration Pack e App per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: ''
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 17f15c49e0f8137d5f11c57fa600588cda791c28
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Aggiungere o aggiornare i partner nei contratti Business to Business nel proprio flusso di lavoro

I partner sono le entità che partecipano alle transazioni e allo scambio di messaggi Business-To-Business (B2B). Prima di poter creare i partner che rappresentano l'utente e l'altra organizzazione nelle transazioni, è necessario che entrambe le parti condividano le informazioni che identificano e convalidano i messaggi inviati. Dopo aver definito questi dettagli e quando si è pronti per avviare una relazione commerciale, è possibile creare i partner nell'account di integrazione che rappresentino entrambe le parti.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Che ruoli hanno i partner nell'account di integrazione?

Per definire i dettagli dello scambio di messaggi tra i partner, è necessario creare contratti tra i partner. Tuttavia, prima di creare un contratto è necessario aggiungere almeno due partner all'account di integrazione. L'organizzazione deve far parte del contratto in qualità di **partner host**. L'altro partner, o **partner guest**, rappresenta l'organizzazione che scambia i messaggi con l'organizzazione. Il partner guest può essere un'altra società o anche un reparto all'interno della stessa organizzazione.

Dopo aver aggiunto i partner, è possibile creare un contratto.

Le impostazioni per la ricezione e l'invio sono configurate dal punto di vista del partner host. Le impostazioni di ricezione del contratto, ad esempio, determinano la modalità con cui il partner ospitato riceve i messaggi inviati da un partner guest. Analogamente, le impostazioni di invio del contratto indicano in che modo il partner ospitato invia messaggi al partner guest.

## <a name="create-partner"></a>Creare il partner

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" e quindi selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. In **Account di integrazione** selezionare l'account di integrazione nel quale aggiungere i partner.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Scegliere il riquadro **Partner**.

   ![Scegliere "Partner"](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. In **Partner** scegliere **Aggiungi**.

   ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Immettere il nome del partner, quindi selezionare un **Qualificatore**. Immettere un **valore** per identificare i documenti ricevuti dalle app. Al termine dell'operazione, scegliere **OK**.

   ![Aggiungere i dettagli del partner](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Scegliere nuovamente il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Viene visualizzato il nuovo partner. 

   ![Visualizzare il nuovo partner](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Modificare il partner

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione. Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/edit.png)

2. In **Partner** selezionare il partner da modificare.

   ![Selezionare i partner da eliminare](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. In **Aggiorna partner** apportare le modifiche necessarie.
Al termine dell'operazione, scegliere **Salva**. 

   ![Apportare modifiche e salvarle](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Per annullare le modifiche, selezionare **Ignora**.

## <a name="delete-partner"></a>Eliminare il partner

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione. Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/delete.png)

2. In **Partner** selezionare il partner che si vuole eliminare.
Scegliere **Elimina**.

   ![Eliminare il partner](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  

