---
title: Codificare o decodificare file flat
description: Codificare o decodificare file flat per l'integrazione aziendale in app per la logica di Azure usando il Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001486"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Codificare o decodificare file flat con App per la logica di Azure tramite l'Enterprise Integration Pack

Prima di inviare contenuto XML a un partner aziendale in uno scenario business-to-business (B2B), potrebbe essere necessario codificare prima il contenuto. Compilando un'app per la logica, è possibile codificare e decodificare i file flat usando il connettore **file flat** . L'app per la logica può ottenere questo contenuto XML da diverse origini, ad esempio il trigger di richiesta, un'altra app o altri [connettori supportati da app per la logica di Azure](../connectors/apis-list.md). Per altre informazioni, vedere [che cos'è app per la logica di Azure](logic-apps-overview.md)?

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica in cui si vuole usare il connettore **file flat** e un trigger che avvia il flusso di lavoro dell'app per la logica. Il connettore **file flat** fornisce solo azioni, non trigger. È possibile usare il trigger o un'altra azione per inserire il contenuto XML nell'app per la logica per la codifica o la decodifica. Se non si ha familiarità con le app per la logica, vedere [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associato alla sottoscrizione di Azure e [collegato all'app per la logica](./logic-apps-enterprise-integration-create-integration-account.md#link-account) in cui si prevede di usare il connettore **file flat** . Sia l'app per la logica che l'account di integrazione devono essere nella stessa località o nella stessa area di Azure.

* Uno [schema](logic-apps-enterprise-integration-schemas.md) di file flat che è stato caricato nell'account di integrazione per codificare o decodificare il contenuto XML

* Almeno due [partner commerciali](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione

## <a name="add-flat-file-encode-action"></a>Aggiungere un'operazione di codifica di file flat

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel trigger o nell'azione nell'app per la logica selezionare **nuovo passaggio**  >  **Aggiungi un'azione**. Questo esempio usa il trigger request, denominato **quando viene ricevuta una richiesta http**, e gestisce le richieste in ingresso dall'esterno dell'app per la logica.

   > [!TIP]
   > La specifica di uno schema JSON è facoltativa. Se si dispone di un payload di esempio dalla richiesta in ingresso, selezionare **USA payload di esempio per generare lo schema**, immettere il payload di esempio e fare clic su **fine**. Lo schema viene visualizzato nella casella **dello schema JSON del corpo della richiesta** .

1. In **scegliere un'azione**immettere `flat file` . Nell'elenco azioni selezionare questa azione: **codifica file flat**

   ![Selezionare l'azione "codifica file flat"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Fare clic all'interno della casella **contenuto** in modo che venga visualizzato l'elenco contenuto dinamico. Nell'elenco, nella sezione **quando viene ricevuta una richiesta http** , selezionare la proprietà **Body** , che contiene l'output del corpo della richiesta del trigger e il contenuto da codificare.

   ![Selezionare il contenuto da codificare dall'elenco di contenuto dinamico](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Se non viene visualizzata la proprietà **Body** nell'elenco di contenuto dinamico, selezionare **vedere altro** accanto all'etichetta della sezione **When a request http is received** .
   > È anche possibile immettere direttamente il contenuto da decodificare nella casella **contenuto** .

1. Dall'elenco **nome schema** selezionare lo schema che si trova nell'account di integrazione collegato da usare per la codifica, ad esempio:

   ![Selezionare lo schema da usare per la codifica](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Se nell'elenco non è presente alcuno schema, l'account di integrazione non contiene alcun file di schema da utilizzare per la codifica. Caricare lo schema che si vuole usare per l'account di integrazione.

1. Salvare l'app per la logica. Per testare il connettore, effettuare una richiesta all'endpoint HTTPS, che viene visualizzato nella proprietà **URL post http** del trigger di richiesta e includere il contenuto XML che si vuole codificare nel corpo della richiesta.

A questo punto è stata eseguita la configurazione dell'azione di codifica file flat. In un'app reale, potrebbe essere necessario archiviare i dati codificati in un'app line-of-business (LOB), ad esempio Salesforce. In alternativa, è possibile inviare i dati codificati a un partner commerciale. Per inviare l'output dall'azione di codifica a Salesforce o al partner commerciale, usare gli altri [connettori disponibili in app per la logica di Azure](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Aggiungere un'azione di decodifica di file flat

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel trigger o nell'azione nell'app per la logica selezionare **nuovo passaggio**  >  **Aggiungi un'azione**. Questo esempio usa il trigger request, denominato **quando viene ricevuta una richiesta http**, e gestisce le richieste in ingresso dall'esterno dell'app per la logica.

   > [!TIP]
   > La specifica di uno schema JSON è facoltativa. Se si dispone di un payload di esempio dalla richiesta in ingresso, selezionare **USA payload di esempio per generare lo schema**, immettere il payload di esempio e fare clic su **fine**. Lo schema viene visualizzato nella casella **dello schema JSON del corpo della richiesta** .

1. In **scegliere un'azione**immettere `flat file` . Nell'elenco azioni selezionare questa azione: **decodifica file flat**

   ![Selezionare l'azione "decodifica file flat"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Fare clic all'interno della casella **contenuto** in modo che venga visualizzato l'elenco contenuto dinamico. Nell'elenco, nella sezione **quando viene ricevuta una richiesta http** , selezionare la proprietà **Body** , che contiene l'output del corpo della richiesta del trigger e il contenuto da decodificare.

   ![Selezionare il contenuto da decodificare dall'elenco di contenuto dinamico](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Se non viene visualizzata la proprietà **Body** nell'elenco di contenuto dinamico, selezionare **vedere altro** accanto all'etichetta della sezione **When a request http is received** . È anche possibile immettere direttamente il contenuto da decodificare nella casella **contenuto** .

1. Dall'elenco **nome schema** selezionare lo schema che si trova nell'account di integrazione collegato da usare per la decodifica, ad esempio:

   ![Selezione dello schema da utilizzare per la decodifica](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Se nell'elenco non è presente alcuno schema, l'account di integrazione non contiene alcun file di schema da utilizzare per la decodifica. Caricare lo schema che si vuole usare per l'account di integrazione.

1. Salvare l'app per la logica. Per testare il connettore, effettuare una richiesta all'endpoint HTTPS, che viene visualizzato nella proprietà **URL post http** del trigger di richiesta e includere il contenuto XML che si vuole decodificare nel corpo della richiesta.

A questo punto è possibile configurare l'azione di decodifica del file flat. In un'app reale, potrebbe essere necessario archiviare i dati decodificati in un'app line-of-business (LOB), ad esempio Salesforce. In alternativa, è possibile inviare i dati decodificati a un partner commerciale. Per inviare l'output dall'azione di decodifica a Salesforce o al partner commerciale, usare gli altri [connettori disponibili in app per la logica di Azure](../connectors/apis-list.md).

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulla [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
