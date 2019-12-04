---
title: Aggiungere partner commerciali per le integrazioni B2B
description: Creare partner commerciali nell'account di integrazione da usare con le app per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792433"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Aggiungere partner commerciali agli account di integrazione per app per la logica di Azure

In [app](../logic-apps/logic-apps-overview.md)per la logica di Azure è possibile creare flussi di lavoro di integrazione business-to-business (B2B) automatici usando un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) con le app per la logica. Per rappresentare l'organizzazione e altri utenti, è possibile creare e aggiungere partner commerciali come elementi all'account di integrazione. I partner sono entità che partecipano alle transazioni B2B e scambiano messaggi tra loro.

Prima di creare questi partner, assicurarsi di discutere e condividere le informazioni con i partner su come identificare e convalidare i messaggi inviati dall'altro. Dopo aver accettato questi dettagli, si è pronti per creare i partner nell'account di integrazione.

## <a name="partner-roles-in-integration-accounts"></a>Ruoli partner negli account di integrazione

Per definire i dettagli relativi ai messaggi scambiati con i partner, è possibile creare e aggiungere [contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md) come elementi all'account di integrazione. I contratti richiedono almeno due partner nell'account di integrazione. L'organizzazione è sempre il *partner host* del contratto. L'organizzazione che scambia messaggi con l'organizzazione è il *Partner Guest*. Un partner guest può essere un'altra società o anche un reparto della propria organizzazione. Dopo aver aggiunto i partner, è possibile creare un contratto.

In un accordo è possibile specificare i dettagli per la gestione dei messaggi in ingresso e in uscita dal punto di vista del partner host. Per i messaggi in arrivo, le **impostazioni di ricezione** specificano il modo in cui il partner host riceve i messaggi dal partner Guest nel contratto. Per i messaggi in uscita, le **impostazioni di invio** specificano il modo in cui il partner host invia messaggi al partner guest.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per l'archiviazione di partner, contratti e altri artefatti B2B. Questo account di integrazione deve essere associato alla sottoscrizione di Azure.

## <a name="create-partner"></a>Creare il partner

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "Integration" e selezionare account di **integrazione**.

   ![Selezionare "account di integrazione"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. In **Account di integrazione** selezionare l'account di integrazione nel quale aggiungere i partner.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. In **Partner** scegliere **Aggiungi**. In **Aggiungi partner**specificare i dettagli del partner come descritto nella tabella seguente.

   ![Scegliere "Aggiungi" e specificare i dettagli del partner](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Proprietà | Obbligatoria | Description |
   |----------|----------|-------------|
   | **Nome** | SÌ | Nome del partner |
   | **Qualificatore** | SÌ | Il corpo di autenticazione che fornisce identità di business univoche alle organizzazioni, ad esempio **D-U-N-S (Dun & Bradstreet)** . <p>I partner possono optare per un'identità aziendale definita reciprocamente. Per questi scenari, selezionare **definito reciprocamente** per EDIFACT o **definito reciprocamente (X12)** per X12. <p>Per RosettaNet selezionare solo **DUNS**, che è lo standard. |
   | **Valore** | SÌ | Valore che identifica i documenti ricevuti dalle app per la logica. <p>Per RosettaNet, questo valore deve essere un numero di nove cifre che corrisponde al numero DUNS. |
   ||||

   > [!NOTE]
   > Per i partner che usano RosettaNet, è possibile specificare informazioni aggiuntive creando prima questi partner e quindi [modificarli successivamente](#edit-partner).

1. Al termine dell'operazione, scegliere **OK**.

   Il nuovo partner viene ora visualizzato nell'elenco **partner** . Il riquadro **partner** aggiorna inoltre il numero corrente di partner.

   ![Nuovo partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Modificare il partner

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione.
Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. In **partner**selezionare il partner che si desidera modificare e scegliere **modifica**. In **modifica**apportare le modifiche.

   ![Apportare modifiche e salvarle](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Per RosettaNet, in **Proprietà partner RosettaNet**è possibile specificare queste informazioni aggiuntive:

   | Proprietà | Obbligatoria | Description |
   |----------|----------|-------------|
   | **Classificazione partner** | No | Tipo di organizzazione del partner |
   | **Codice Supply Chain** | No | Il codice della catena di fornitura del partner, ad esempio, "Information Technology" o "Electronic Components" |
   | **Nome contatto** | No | Nome del contatto del partner |
   | **Indirizzo di posta elettronica** | No | Indirizzo di posta elettronica del partner |
   | **Fax** | No | Numero di fax del partner |
   | **Telefono** | No | Il numero di telefono del partner |
   ||||

1. Al termine, scegliere **OK** per salvare le modifiche.

## <a name="delete-partner"></a>Eliminare il partner

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione. Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. In **Partner** selezionare il partner che si vuole eliminare. Scegliere **Elimina**.

   ![Eliminare il partner](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md)