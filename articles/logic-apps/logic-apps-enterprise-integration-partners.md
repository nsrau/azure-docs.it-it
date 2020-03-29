---
title: Aggiungere partner commerciali per le integrazioni B2B
description: Creare partner commerciali nell'account di integrazione da usare con le app per la logica di AzureCreate trading partners in your integration account to use with Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792433"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Aggiungere partner commerciali agli account di integrazione per le app per la logica di AzureAdd trading partners to integration accounts for Azure Logic Apps

Nelle [app per](../logic-apps/logic-apps-overview.md)la logica di Azure è possibile creare flussi di lavoro di integrazione business-to-business (B2B) automatizzati usando un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) con le app per la logica. Per rappresentare l'organizzazione e altri, è necessario creare e aggiungere partner commerciali come elementi all'account di integrazione. I partner sono entità che partecipano a transazioni B2B e si scambiano messaggi tra loro.

Prima di creare questi partner, assicurarsi di discutere e condividere con i partner informazioni su come identificare e convalidare i messaggi inviati dall'altro. Dopo aver concordato questi dettagli, sei pronto per creare partner nel tuo account di integrazione.

## <a name="partner-roles-in-integration-accounts"></a>Ruoli partner negli account di integrazione

Per definire i dettagli sui messaggi scambiati con i partner, è necessario creare e aggiungere [contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md) come elementi all'account di integrazione. I contratti richiedono almeno due partner nell'account di integrazione. L'organizzazione è sempre il *partner host* nel contratto. L'organizzazione che scambia messaggi con l'organizzazione è il *partner guest.* Un partner guest può essere un'altra società o anche un reparto della propria organizzazione. Dopo aver aggiunto i partner, è possibile creare un contratto.

In un accordo, specificare i dettagli per la gestione dei messaggi in ingresso e in uscita dal punto di vista del partner host. Per i messaggi in arrivo, le impostazioni di **ricezione** specificano il modo in cui il partner host riceve i messaggi dal partner guest nel contratto. Per i messaggi in uscita, le impostazioni di **invio** specificano il modo in cui il partner host invia i messaggi al partner guest.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per l'archiviazione di partner, accordi e altri elementi B2B. Questo account di integrazione deve essere associato alla sottoscrizione di Azure.This integration account must be associated with your Azure subscription.

## <a name="create-partner"></a>Creare il partner

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" e selezionare **Account di integrazione**.

   ![Selezionare "Account di integrazione"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. In **Account di integrazione** selezionare l'account di integrazione nel quale aggiungere i partner.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. In **Partner** scegliere **Aggiungi**. In **Aggiungi partner**specificare i dettagli del partner come descritto nella tabella seguente.

   ![Scegli "Aggiungi" e fornisci i dettagli del partner](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Sì | Il nome del partner |
   | **Qualifier** | Sì | Il corpo di autenticazione che fornisce identità di business univoche alle organizzazioni, ad esempio **D-U-N-S (Dun & Bradstreet)**. <p>I partner possono optare per un'identità aziendale definita reciprocamente. Per questi scenari, selezionare **Definito mutualmente** per EDIFACT o **Mutuamente definito (X12)** per X12. <p>Per RosettaNet, selezionare solo **DUNS**, che è lo standard. |
   | **Valore** | Sì | Valore che identifica i documenti ricevuti dalle app per la logica. <p>Per RosettaNet, questo valore deve essere un numero a nove cifre che corrisponde al numero DUNS. |
   ||||

   > [!NOTE]
   > Per i partner che utilizzano RosettaNet, è possibile specificare informazioni aggiuntive creando prima questi partner e quindi [modificandoli in un secondo momento.](#edit-partner)

1. Al termine dell'operazione, scegliere **OK**.

   Il nuovo partner viene ora visualizzato nell'elenco **Partner.** Inoltre, il riquadro **Partner** aggiorna il numero corrente di partner.

   ![Nuovo partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Modificare il partner

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione.
Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. In **Partner**selezionare il partner che si desidera modificare e scegliere **Modifica**. In **Modifica**, apportare le modifiche desiderate.

   ![Apportare modifiche e salvarle](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Per RosettaNet, in **RosettaNet Partner Properties**, è possibile specificare queste informazioni aggiuntive:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Partner Classification** | No | Tipo di organizzazione del partner |
   | **Codice della catena di fornitura** | No | Il codice della catena di fornitura del partner, ad esempio "Information Technology" o "Electronic Components" |
   | **Nome contatto** | No | Nome del contatto del partner |
   | **Posta elettronica** | No | Indirizzo e-mail del partner |
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

* Ulteriori informazioni sugli [accordi](../logic-apps/logic-apps-enterprise-integration-agreements.md)