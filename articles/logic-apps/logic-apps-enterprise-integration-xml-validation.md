---
title: Convalida XML per l'integrazione aziendale B2B
description: Convalidare il codice XML usando gli schemi nelle app per la logica di Azure con Enterprise Integration PackValidate XML by using schemas in Azure Logic Apps with Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792173"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Convalidare XML per l'integrazione aziendale B2B in App per la logica di Azure con Enterprise Integration Pack

Spesso negli scenari B2B, i partner commerciali in un accordo devono assicurarsi che i messaggi scambiati siano validi prima di poter avviare qualsiasi elaborazione dei dati. È possibile convalidare i documenti rispetto a uno schema predefinito utilizzando l'azione di convalida XML, disponibile con Enterprise Integration Pack.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione, [iscriversi per ottenere un account Azure gratuito.](https://azure.microsoft.com/free/)

* Un'app per la logica vuota o esistente in cui si vuole usare l'azione di convalida XML. Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associato alla sottoscrizione di Azure è collegato all'app per la logica in cui si prevede di usare l'azione di convalida XML e contiene lo schema che si vuole usare per la convalida del contenuto XML. Sia l'app per la logica che l'account di integrazione devono esistere nello stesso percorso o nell'area di Azure.Both logic app and integration account must exist in the same location or Azure region.

## <a name="add-xml-validation-action"></a>Aggiungere un'azione di convalida XMLAdd XML validation action

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Se si dispone di un'app per la logica vuota, `HTTP request` nella casella di ricerca di Progettazione app per la logica immettere come filtro e selezionare il trigger **Quando viene ricevuta una richiesta HTTP.** In caso contrario, continuare con il passaggio successivo.

1. Nell'ultimo passaggio del flusso di lavoro selezionare **Nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi esistenti, spostare il puntatore**+** sulla freccia che connette tali passaggi in modo che venga visualizzato il segno più ( ). Selezionare il segno più e quindi selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `xml validation` come filtro. Nell'elenco delle azioni selezionare **Convalida XML**.

   ![Trovare e selezionare l'azione "Convalida XML"](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Per specificare il contenuto XML da convalidare, fare clic all'interno della casella **Contenuto** in modo che venga visualizzato l'elenco del contenuto dinamico.

   ![Aprire l'elenco dei contenuti dinamici](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   L'elenco di contenuto dinamico mostra i token delle proprietà che rappresentano gli output dei passaggi precedenti nel flusso di lavoro. Se nell'elenco non viene visualizzata una proprietà prevista, controllare il trigger o l'intestazione dell'azione se è possibile selezionare **Altro**.

1. Nell'elenco del contenuto dinamico selezionare la proprietà con il contenuto che si desidera convalidare.

   In questo esempio viene selezionato l'output **Body** dal trigger.

   ![Selezionare il contenuto da convalidare](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Per specificare lo schema che si desidera utilizzare per la convalida, aprire l'elenco **Nome schema** e selezionare lo schema di convalida aggiunto all'account di integrazione collegato.

   ![Selezionare lo schema da utilizzare per la convalida](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Salvare l'app per la logica.

   La configurazione della convalida è terminata. In a real world app, you might want to store the validated data in a line-of-business (LOB) app such as SalesForce. Per inviare l'output della convalida a Salesforce, aggiungere un'azione.

1. Per testare l'azione di convalida, è possibile inviare una richiesta per attivare il flusso di lavoro dell'app per la logica.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni su [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)