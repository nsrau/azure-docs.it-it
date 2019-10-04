---
title: Creare e gestire gli accordi tra partner commerciali - App per la logica di Azure
description: Creare e gestire gli accordi tra partner commerciali tramite App per la logica di Azure ed Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 4bfee4ec442c9e7b0351b0fd0c6a2b8e163a2541
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330311"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Creare e gestire accordi tra partner commerciali nelle App per la logica di Azure

Oggetto [partner commerciale](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*contratto* aiuta le organizzazioni e aziende comunicare facilmente tra loro definendo il protocollo standard del settore specifico da usare durante lo scambio messaggi Business-to-business (B2B). Contratti offrono i vantaggi comuni, ad esempio:

* Consentono alle organizzazioni di scambiare informazioni con un formato noto.
* Migliorare l'efficienza durante l'esecuzione di transazioni B2B.
* Sono facili da creare, gestire e utilizzare per la creazione di soluzioni di integrazione aziendale.

Questo articolo illustra come creare un AS2, EDIFACT o X12 accordo in cui è possibile usare durante la compilazione di soluzioni di integrazione per gli scenari B2B dell'organizzazione usando il [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) e [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Dopo aver creato un accordo, è possibile quindi utilizzare AS2, EDIFACT o X12 connettori per lo scambio di messaggi B2B.

Per creare accordi per lo scambio di messaggi di RosettaNet, vedere [i messaggi di Exchange RosettaNet](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per l'archiviazione del contratto e altri artefatti B2B. Questo account di integrazione deve essere associato alla sottoscrizione di Azure.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) che già stato creato nell'account di integrazione. Un contratto richiede un partner host e un partner guest. Entrambi i partner devono utilizzare lo stesso qualificatore di "identità di business" come l'accordo che si desidera creare, ad esempio AS2, X12 O EDIFACT.

* Facoltativo: L'app per la logica in cui si desidera utilizzare il contratto e un trigger che avvia flusso di lavoro dell'app per la logica. Per creare l'account di integrazione e gli elementi B2B, non occorre un'app per la logica. Tuttavia, prima app per la logica possa usare gli artefatti B2B nell'account di integrazione, è necessario collegare l'account di integrazione all'App per la logica. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Creare contratti

1. Accedere al [portale di Azure](https://portal.azure.com).
Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" come filtro. Dai risultati, selezionare la risorsa: **Account di integrazione**

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Sotto **account di integrazione**, selezionare l'account di integrazione in cui si desidera creare il contratto.

   ![Selezionare l'account di integrazione in cui si vuole creare il contratto.](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. Nel riquadro di destra, sotto **componenti**, scegliere il **contratti** riquadro.

   ![Scegliere "Contratti"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. In **Contratti** scegliere **Aggiungi**. Nel **Add** riquadro, specificare le informazioni del contratto, ad esempio:

   ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Proprietà | Obbligatorio | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Nome** | Yes | <*agreement-name*> | Il nome del contratto |
   | **Tipo di contratto** | Yes | **AS2**, **X12**, o **EDIFACT** | Il tipo di protocollo per il contratto. Quando si crea il file del contratto, il contenuto in tale file deve corrispondere al tipo di contratto. | |  
   | **Partner host** | Yes | <*host-partner-name*> | Il partner host rappresenta l'organizzazione che specifica il contratto |
   | **Identità host** | Yes | <*host-partner-identifier*> | Identificatore del partner host |
   | **Partner guest** | Yes | <*guest-partner-name*> | Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host |
   | **Identità guest** | Yes | <*guest-partner-identifier*> | Identificatore del partner guest |
   | **Impostazioni di ricezione** | Variabile | Variabile | Queste proprietà specificano come vengono ricevuti tutti i messaggi in ingresso dal partner guest nell'accordo partner host. Per altre informazioni, vedere il tipo di contratto corrispondente: <p>- [Impostazioni dei messaggi AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Impostazioni dei messaggi EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Impostazioni dei messaggi X12](logic-apps-enterprise-integration-x12.md) |
   | **Impostazioni di invio** | Variabile | Variabile | Queste proprietà specificano come il partner host invia tutti i messaggi in uscita al partner guest nel contratto. Per altre informazioni, vedere il tipo di contratto corrispondente: <p>- [Impostazioni dei messaggi AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Impostazioni dei messaggi EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Impostazioni dei messaggi X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Dopo aver completato la creazione del contratto, nelle **Add** pagina, scegliere **OK**e tornare all'account di integrazione.

   Il **contratti** elenco Mostra ora il nuovo contratto.

## <a name="edit-agreements"></a>Modifica di contratti

1. Nel [portale di Azure](https://portal.azure.com), nel menu principale di Azure, selezionare **tutti i servizi**.

1. Nella casella di ricerca immettere "integrazione" come filtro. Dai risultati, selezionare la risorsa: **Account di integrazione**

1. Sotto **account di integrazione**, selezionare l'account di integrazione con l'accordo che si desidera modificare.

1. Nel riquadro di destra, sotto **componenti**, scegliere il **contratti** riquadro.

1. Sotto **contratti**, selezionare il contratto e scegliere **modificare**.

1. Verificare e quindi salvare le modifiche.

## <a name="delete-agreements"></a>Eliminare i contratti

1. Nel [portale di Azure](https://portal.azure.com), nel menu principale di Azure, selezionare **tutti i servizi**.

1. Nella casella di ricerca immettere "integrazione" come filtro. Dai risultati, selezionare la risorsa: **Account di integrazione**

1. Sotto **account di integrazione**, selezionare l'account di integrazione con l'accordo che si desidera eliminare.

1. Nel riquadro di destra, sotto **componenti**, scegliere il **contratti** riquadro.

1. Sotto **contratti**, selezionare il contratto e scegliere **eliminare**.

1. Confermare che si desidera eliminare l'accordo selezionato.

## <a name="next-steps"></a>Passaggi successivi

* [Scambiare messaggi AS2](logic-apps-enterprise-integration-as2.md)
* [Scambiare messaggi EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Scambiare messaggi X12](logic-apps-enterprise-integration-x12.md)
