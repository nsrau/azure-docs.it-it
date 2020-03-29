---
title: Accordi con i partner commerciali
description: Creare e gestire accordi tra partner commerciali tramite App per la logica di Azure e Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790729"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Creare e gestire gli accordi con i partner commerciali nelle app per la logica di AzureCreate and manage trading partner agreements in Azure Logic Apps

Un*accordo* tra partner [commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) 
aiuta le organizzazioni e le aziende a comunicare senza problemi tra loro definendo il protocollo standard del settore specifico da utilizzare quando si scambiano messaggi business-to-business (B2B). Gli accordi offrono vantaggi comuni, ad esempio:

* Consentire alle organizzazioni di scambiare informazioni utilizzando un formato noto.
* Migliorare l'efficienza durante lo svolgimento di transazioni B2B.
* Sono facili da creare, gestire e utilizzare per la creazione di soluzioni di integrazione aziendale.

Questo articolo illustra come creare un contratto AS2, EDIFACT o X12 che è possibile usare quando si creano soluzioni di integrazione aziendale per scenari B2B usando [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) e App per la logica di [Azure.](../logic-apps/logic-apps-overview.md) Dopo aver creato un accordo, è possibile utilizzare i connettori AS2, EDIFACT o X12 per lo scambio di messaggi B2B.

Per creare accordi per lo scambio di messaggi RosettaNet, vedere [Exchange RosettaNet messages](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per l'archiviazione del contratto e di altri elementi B2B. Questo account di integrazione deve essere associato alla sottoscrizione di Azure.This integration account must be associated with your Azure subscription.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) che hai già creato nel tuo account di integrazione. Un accordo richiede sia un partner ospitante che un partner guest. Entrambi i partner devono utilizzare lo stesso qualificatore di "identità aziendale" dell'accordo che si desidera creare, ad esempio AS2, X12 o EDIFACT.

* Facoltativo: l'app per la logica in cui vuoi usare il contratto e un trigger che avvia il flusso di lavoro dell'app per la logica. Per creare solo l'account di integrazione e gli elementi B2B, non è necessaria un'app per la logica. Tuttavia, prima che l'app per la logica possa usare gli elementi B2B nell'account di integrazione, è necessario collegare l'account di integrazione all'app per la logica. Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Creare contratti

1. Accedere al [portale](https://portal.azure.com)di Azure .
Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" come filtro. Dai risultati, selezionare questa risorsa: **Account di integrazione**

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. In Account di **integrazione**selezionare l'account di integrazione in cui si desidera creare il contratto.

   ![Selezionare l'account di integrazione in cui si vuole creare il contratto.](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. Nel riquadro di destra, in **Componenti**, scegliere il riquadro **Contratti.**

   ![Scegli "Accordi"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. In **Contratti** scegliere **Aggiungi**. Nel riquadro Aggiungi fornire informazioni sull'accordo, ad esempio:In the **Add** pane, provide information about your agreement, for example:

   ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome** | Sì | <*nome-accordo*> | Il nome del tuo accordo |
   | **Tipo di accordo** | Sì | **AS2**, **X12**o **EDIFACT** | Tipo di protocollo per il contratto. Quando si crea il file di accordo, il contenuto di tale file deve corrispondere al tipo di accordo. | |  
   | **Host Partner (Partner host)** | Sì | <*nome-partner host*> | Il partner host rappresenta l'organizzazione che specifica l'accordo |
   | **Host Identity (Identità host)** | Sì | <*identificatore del partner host*> | Identificatore del partner host |
   | **Guest Partner (Partner guest)** | Sì | <*guest-partner-name*> | Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host |
   | **identità guest** | Sì | <*guest-partner-identifier*> | Identificatore del partner ospite |
   | **Receive Settings (Impostazioni di ricezione)** | Variabile | Variabile | Queste proprietà specificano il modo in cui il partner host riceve tutti i messaggi in arrivo dal partner guest nell'accordo. Per ulteriori informazioni, vedere il rispettivo tipo di contratto: <p>- [Impostazioni dei messaggi AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Impostazioni dei messaggi EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Impostazioni dei messaggi X12](logic-apps-enterprise-integration-x12.md) |
   | **Send Settings (Impostazioni di invio)** | Variabile | Variabile | Queste proprietà specificano il modo in cui il partner host invia tutti i messaggi in uscita al partner guest nell'accordo. Per ulteriori informazioni, vedere il rispettivo tipo di contratto: <p>- [Impostazioni dei messaggi AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Impostazioni dei messaggi EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Impostazioni dei messaggi X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Al termine della creazione del contratto, nella pagina **Aggiungi** scegliere **OK**e tornare all'account di integrazione.

   L'elenco **Accordi** ora mostra il nuovo accordo.

## <a name="edit-agreements"></a>Modificare gli accordi

1. Nel [portale di Azure](https://portal.azure.com), nel menu principale di Azure selezionare **Tutti i servizi**.

1. Nella casella di ricerca immettere "integrazione" come filtro. Dai risultati, selezionare questa risorsa: **Account di integrazione**

1. In Account di **integrazione**selezionare l'account di integrazione con il contratto che si desidera modificare.

1. Nel riquadro di destra, in **Componenti**, scegliere il riquadro **Contratti.**

1. In **Accordi**selezionare il contratto e scegliere **Modifica**.

1. Apportare e quindi salvare le modifiche.

## <a name="delete-agreements"></a>Eliminare accordi

1. Nel [portale di Azure](https://portal.azure.com), nel menu principale di Azure selezionare **Tutti i servizi**.

1. Nella casella di ricerca immettere "integrazione" come filtro. Dai risultati, selezionare questa risorsa: **Account di integrazione**

1. In Account di **integrazione**selezionare l'account di integrazione con il contratto che si desidera eliminare.

1. Nel riquadro di destra, in **Componenti**, scegliere il riquadro **Contratti.**

1. In **Accordi**selezionare il contratto e scegliere **Elimina**.

1. Confermare che si desidera eliminare l'accordo selezionato.

## <a name="next-steps"></a>Passaggi successivi

* [Scambiare messaggi AS2](logic-apps-enterprise-integration-as2.md)
* [Scambiare messaggi EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Scambiare messaggi X12](logic-apps-enterprise-integration-x12.md)
