---
title: Accordi tra partner commerciali
description: Creare e gestire i contratti tra partner commerciali usando app per la logica di Azure e Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790729"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Creare e gestire gli accordi tra partner commerciali in app per la logica di Azure

Un [partner commerciale](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*accordo* consente a organizzazioni e aziende di comunicare senza difficoltà tra loro definendo il protocollo standard del settore specifico da usare per lo scambio di messaggi business-to-business (B2B). I contratti forniscono i vantaggi più comuni, ad esempio:

* Consentire alle organizzazioni di scambiare informazioni usando un formato noto.
* Migliorare l'efficienza quando si eseguono transazioni B2B.
* Sono facili da creare, gestire e usare per la creazione di soluzioni di integrazione aziendale.

Questo articolo illustra come creare un contratto AS2, EDIFACT o X12 che è possibile usare per la compilazione di soluzioni di integrazione aziendale per scenari B2B usando il [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) e [app](../logic-apps/logic-apps-overview.md)per la logica di Azure. Dopo aver creato un contratto, è possibile utilizzare i connettori AS2, EDIFACT o X12 per lo scambio di messaggi B2B.

Per creare accordi per lo scambio di messaggi RosettaNet, vedere [Exchange RosettaNet messages](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per archiviare il contratto e altri artefatti B2B. Questo account di integrazione deve essere associato alla sottoscrizione di Azure.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) già creati nell'account di integrazione. Un contratto richiede un partner host e un partner guest. Entrambi i partner devono utilizzare lo stesso qualificatore "identità di business" del contratto che si desidera creare, ad esempio AS2, X12 o EDIFACT.

* Facoltativo: l'app per la logica in cui si vuole usare il contratto e un trigger che avvia il flusso di lavoro dell'app per la logica. Per creare solo l'account di integrazione e gli artefatti B2B, non è necessaria un'app per la logica. Tuttavia, prima che l'app per la logica possa usare gli artefatti B2B nell'account di integrazione, è necessario collegare l'account di integrazione all'app per la logica. Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Creazione di contratti

1. Accedere al [portale di Azure](https://portal.azure.com).
Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "Integration" come filtro. Nei risultati selezionare questa risorsa: account di **integrazione**

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. In **account di integrazione**selezionare l'account di integrazione in cui si vuole creare il contratto.

   ![Selezionare l'account di integrazione in cui si vuole creare il contratto.](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. Nel riquadro di destra, in **componenti**, scegliere il riquadro **contratti** .

   ![Scegliere "contratti"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. In **Contratti** scegliere **Aggiungi**. Nel riquadro **Aggiungi** fornire le informazioni sul contratto, ad esempio:

   ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Proprietà | Obbligatoria | Value | Description |
   |----------|----------|-------|-------------|
   | **Nome** | SÌ | *nome contratto* <> | Nome del contratto |
   | **Tipo di contratto** | SÌ | **AS2**, **X12**o **EDIFACT** | Tipo di protocollo per il contratto. Quando si crea il file del contratto, il contenuto del file deve corrispondere al tipo di contratto. | |  
   | **Partner host** | SÌ | <*host-partner-name*> | Il partner host rappresenta l'organizzazione che specifica il contratto |
   | **Identità host** | SÌ | <*host-partner-identifier*> | Identificatore del partner host |
   | **Partner Guest** | SÌ | <*nome partner-guest*> | Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host |
   | **Identità Guest** | SÌ | <*identificatore Guest-partner*> | Identificatore del partner Guest |
   | **Impostazioni di ricezione** | Variabile | Variabile | Queste proprietà specificano il modo in cui il partner host riceve tutti i messaggi in arrivo dal partner Guest nel contratto. Per ulteriori informazioni, vedere il rispettivo tipo di contratto: <p>[impostazioni del messaggio - AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>[impostazioni del messaggio - EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Impostazioni messaggio X12](logic-apps-enterprise-integration-x12.md) |
   | **Impostazioni di invio** | Variabile | Variabile | Queste proprietà specificano il modo in cui il partner host invia tutti i messaggi in uscita al Partner Guest nel contratto. Per ulteriori informazioni, vedere il rispettivo tipo di contratto: <p>[impostazioni del messaggio - AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>[impostazioni del messaggio - EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Impostazioni messaggio X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Al termine della creazione del contratto, nella pagina **Aggiungi** scegliere **OK**e tornare all'account di integrazione.

   Nell'elenco dei **contratti** viene ora visualizzato il nuovo contratto.

## <a name="edit-agreements"></a>Modificare i contratti

1. Nel [portale di Azure](https://portal.azure.com)scegliere **tutti i servizi**dal menu principale di Azure.

1. Nella casella di ricerca immettere "Integration" come filtro. Nei risultati selezionare questa risorsa: account di **integrazione**

1. In **account di integrazione**selezionare l'account di integrazione che contiene il contratto che si desidera modificare.

1. Nel riquadro di destra, in **componenti**, scegliere il riquadro **contratti** .

1. In **Agreements (contratti**) selezionare il contratto e scegliere **Edit (modifica**).

1. Apportare e salvare le modifiche.

## <a name="delete-agreements"></a>Elimina contratti

1. Nel [portale di Azure](https://portal.azure.com)scegliere **tutti i servizi**dal menu principale di Azure.

1. Nella casella di ricerca immettere "Integration" come filtro. Nei risultati selezionare questa risorsa: account di **integrazione**

1. In **account di integrazione**selezionare l'account di integrazione che contiene il contratto che si desidera eliminare.

1. Nel riquadro di destra, in **componenti**, scegliere il riquadro **contratti** .

1. In **Agreements (contratti**) selezionare il contratto e scegliere **Delete (Elimina**).

1. Confermare che si desidera eliminare l'accordo selezionato.

## <a name="next-steps"></a>Passaggi successivi

* [Messaggi AS2 di Exchange](logic-apps-enterprise-integration-as2.md)
* [Messaggi EDIFACT di Exchange](logic-apps-enterprise-integration-edifact.md)
* [Messaggi di Exchange X12](logic-apps-enterprise-integration-x12.md)
