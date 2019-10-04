---
title: Aggiungere partner commerciali per l'integrazione B2B - App per la logica di Azure
description: Creare i partner commerciali nell'account di integrazione da usare con le App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330161"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Aggiungere partner commerciali per gli account di integrazione per le App per la logica di Azure

Nelle [Azure Logic Apps](../logic-apps/logic-apps-overview.md), è possibile creare business-to-business (B2B) integrazione flussi di lavoro automatizzati con un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) con App per la logica. Per rappresentare l'organizzazione e altri ancora, creare e aggiungere i partner commerciali come elementi all'account di integrazione. I partner sono le entità che partecipano alle transazioni B2B e lo scambio di messaggi tra loro.

Prima di creare i partner, assicurarsi di discutere e condividere informazioni con i partner su come identificare e convalidare i messaggi inviati a altro. Dopo che concordare questi dettagli, si è pronti per creare i partner nell'account di integrazione.

## <a name="partner-roles-in-integration-accounts"></a>Ruoli partner nell'account di integrazione

Per definire i dettagli relativi ai messaggi scambiati con i partner, creare e aggiungere [contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md) come elementi all'account di integrazione. Contratti richiedono almeno due partner nell'account di integrazione. L'organizzazione è sempre il *partner host* nell'accordo. L'organizzazione che scambia messaggi con l'organizzazione è la *partner guest*. Un partner guest può essere un'altra società o anche un reparto dell'organizzazione. Dopo aver aggiunto i partner, è possibile creare un contratto.

In un accordo, specificare i dettagli per la gestione dei messaggi in ingresso e in uscita dal punto di vista del partner host. Per i messaggi in ingresso, il **impostazioni di ricezione** specificare come il partner host riceve messaggi dal partner guest nel contratto. Per i messaggi in uscita, il **impostazioni di invio** specificare come il partner host invia messaggi al partner guest.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per l'archiviazione dei partner, contratti e altri artefatti B2B. Questo account di integrazione deve essere associato alla sottoscrizione di Azure.

## <a name="create-partner"></a>Creare il partner

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" e selezionare **account di integrazione**.

   ![Selezionare "Account di integrazione"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. In **Account di integrazione** selezionare l'account di integrazione nel quale aggiungere i partner.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. In **Partner** scegliere **Aggiungi**. Sotto **aggiungere Partner**, specificare i dettagli del partner come descritto nella tabella seguente.

   ![Fare clic su "Aggiungi" e fornire i dettagli del partner](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Proprietà | Obbligatorio | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Yes | Nome del partner |
   | **Qualifier** | Yes | Il corpo di autenticazione che fornisca le identità di business univoche per le organizzazioni, ad esempio, **D-U-N-S (Dun & Bradstreet)** . <p>I partner possono optare per un'identità di business definite si escludono a vicenda. Per questi scenari, selezionare **Mutually Defined** EDIFACT o **Mutually Defined (X12)** per X12. <p>Per RosettaNet, selezionare solo **DUNS**, ovvero lo standard. |
   | **Valore** | Yes | Un valore che identifica i documenti che App per la logica di ricezione. <p>Per RosettaNet, questo valore deve essere un numero a nove cifre che corrisponde al numero DUNS. |
   ||||

   > [!NOTE]
   > Per i partner che usano RosettaNet, è possibile specificare informazioni aggiuntive creando innanzitutto questi partner e quindi [modifica di questi elementi in un secondo momento](#edit-partner).

1. Al termine dell'operazione, scegliere **OK**.

   Il nuovo partner verrà ora visualizzato nei **partner** elenco. Inoltre, il **partner** riquadro aggiorna il numero corrente di partner.

   ![Nuovo partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Modificare il partner

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione.
Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Sotto **partner**, selezionare il partner che si desidera modificare e scegliere **modificare**. Sotto **modifica**, apportare le modifiche.

   ![Apportare modifiche e salvarle](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Per RosettaNet, sotto **proprietà Partner di RosettaNet**, è possibile specificare queste informazioni aggiuntive:

   | Proprietà | Obbligatorio | Descrizione |
   |----------|----------|-------------|
   | **Classificazione partner** | No | Tipo di organizzazione del partner |
   | **Supply chain code** | No | Il codice del partner supply chain, ad esempio "IT" o "Electronic Components" |
   | **Nome del contatto** | No | Nome contatto del partner |
   | **Indirizzo di posta elettronica** | No | Indirizzo di posta elettronica del partner |
   | **Fax** | No | Numero di fax del partner |
   | **telefono** | No | Numero di telefono del partner |
   ||||

1. Al termine, scegliere **OK** per salvare le modifiche.

## <a name="delete-partner"></a>Eliminare il partner

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione. Scegliere il riquadro **Partner**.

   ![Scegliere il riquadro "Partner"](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. In **Partner** selezionare il partner che si vuole eliminare. Scegliere **Elimina**.

   ![Eliminare il partner](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md)