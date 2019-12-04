---
title: Convalida XML per l'integrazione aziendale B2B
description: Convalidare il codice XML usando gli schemi in app per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792173"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Convalidare XML per l'integrazione aziendale B2B in App per la logica di Azure con Enterprise Integration Pack

Spesso negli scenari B2B, i partner commerciali di un accordo devono assicurarsi che i messaggi scambiati siano validi prima di poter avviare qualsiasi elaborazione dei dati. È possibile convalidare i documenti in base a uno schema predefinito utilizzando l'azione di convalida XML, disponibile con l'Enterprise Integration Pack.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, [iscriversi per ottenere un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica vuota o esistente in cui si vuole usare l'azione di convalida XML. Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associato alla sottoscrizione di Azure è collegato all'app per la logica in cui si prevede di usare l'azione di convalida XML e contiene lo schema che si vuole usare per la convalida del contenuto XML. Sia l'app per la logica che l'account di integrazione devono esistere nella stessa località o nell'area di Azure.

## <a name="add-xml-validation-action"></a>Aggiungi azione di convalida XML

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Se si dispone di un'app per la logica vuota, nella casella di ricerca della finestra di progettazione dell'app per la logica immettere `HTTP request` come filtro e selezionare il trigger **quando viene ricevuta una richiesta http** . In caso contrario, continuare con il passaggio successivo.

1. Nell'ultimo passaggio del flusso di lavoro selezionare **nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi esistenti, spostare il puntatore sulla freccia che connette tali passaggi, in modo che venga visualizzato il segno più ( **+** ). Selezionare il segno più e quindi selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `xml validation` come filtro. Nell'elenco azioni selezionare **convalida XML**.

   ![Trovare e selezionare l'azione "convalida XML"](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Per specificare il contenuto XML che si desidera convalidare, fare clic all'interno della casella **contenuto** in modo che venga visualizzato l'elenco contenuto dinamico.

   ![Apri elenco di contenuto dinamico](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   L'elenco di contenuto dinamico Mostra i token di proprietà che rappresentano gli output dei passaggi precedenti del flusso di lavoro. Se nell'elenco non è visualizzata una proprietà prevista, controllare il trigger o l'intestazione dell'azione se è possibile selezionare **Visualizza altro**.

1. Dall'elenco di contenuto dinamico selezionare la proprietà che contiene il contenuto che si desidera convalidare.

   Questo esempio Mostra come selezionare l'output del **corpo** del trigger.

   ![Selezionare il contenuto da convalidare](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Per specificare lo schema che si desidera utilizzare per la convalida, aprire l'elenco **nome schema** e selezionare lo schema di convalida aggiunto all'account di integrazione collegata.

   ![Selezionare lo schema da usare per la convalida](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Salvare l'app per la logica.

   A questo punto è stata eseguita la configurazione della convalida. In un'app reale è possibile archiviare i dati convalidati in un'app line-of-business (LOB), ad esempio SalesForce. Per inviare l'output della convalida a Salesforce, aggiungere un'azione.

1. Per testare l'azione di convalida, è possibile inviare una richiesta per attivare il flusso di lavoro dell'app per la logica.

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulla [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)