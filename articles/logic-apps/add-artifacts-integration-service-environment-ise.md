---
title: Aggiungere elementi agli ambienti di Integration Services (ISEs) in app per la logica di Azure
description: Aggiungere app per la logica, connettori personalizzati e account di integrazione all'ambiente di Integration Services (ISE) per accedere alle reti virtuali di Azure (reti virtuali), rimanendo privato e isolato da Azure pubblico o "globale".
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: df43b52514eebc3216dbec01cff0d8a3b14e7940
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517657"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Aggiungere elementi all'ambiente Integration Services (ISE) in app per la logica di Azure

Dopo aver creato un [ambiente di Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), aggiungere elementi come app per la logica, account di integrazione e connettori personalizzati in modo che possano accedere alle risorse nella rete virtuale di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* ISE creato per eseguire le app per la logica. Se non si dispone di un ISE, [creare prima un ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps-in-an-ise"></a>Creare app per la logica in ISE

Per compilare app per la logica che vengono eseguite nell'ambiente Integration Services (ISE), seguire questa procedura:

1. Trovare e aprire ISE, se non è già aperto. Dal menu ISE in **Impostazioni**selezionare **app** > per la logica**Aggiungi**.

   ![Aggiungere una nuova app per la logica ad ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   -oppure-

   Dal menu principale di Azure selezionare **Crea una risorsa** > **Integrazione** > **App per la logica**.

1. Specificare il nome, la sottoscrizione di Azure e il gruppo di risorse di Azure (nuovo o esistente) da usare per l'app per la logica.

1. Dall'elenco **location** , nella sezione **Integration Service Environments** , selezionare ISE, ad esempio:

   ![Selezionare l'ambiente del servizio di integrazione](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Se si vuole usare le app per la logica con un account di integrazione, le app per la logica e l'account di integrazione devono usare lo stesso ISE.

1. Continuare [a creare l'app per la logica nel modo consueto](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Per le differenze di funzionamento dei trigger e delle azioni e del modo in cui vengono etichettati quando si usa ISE rispetto al servizio app per la logica globale, vedere isolated e [Global nella Panoramica di ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Per gestire le app per la logica e le connessioni API in ISE, vedere [gestire l'ambiente del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts-in-an-ise"></a>Creare account di integrazione in ISE

In base allo [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selezionato al momento della creazione, ISE include un utilizzo specifico dell'account di integrazione senza costi aggiuntivi. Le app per la logica esistenti in un ambiente di Integration Services possono fare riferimento solo agli account di integrazione presenti nello stesso ISE. Quindi, affinché un account di integrazione funzioni con le app per la logica in un ISE, l'account di integrazione e le app per la logica devono usare lo *stesso ambiente* del percorso. Per altre informazioni sugli account di integrazione e ISEs, [vedere account di integrazione](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment
)con ISE.

Per creare un account di integrazione che usa ISE, seguire questa procedura:

1. Trovare e aprire ISE, se non è già aperto. Dal menu ISE, in **Impostazioni**, selezionare **account** > di integrazione**Aggiungi**.

   ![Aggiungi nuovo account di integrazione a ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   -oppure-

   Dal menu principale di Azure selezionare **Crea un account di** >  > **integrazione**della risorsa.

1. Specificare il nome, la sottoscrizione di Azure, il gruppo di risorse di Azure (nuovo o esistente) e il piano tariffario da usare per l'account di integrazione.

1. Dall'elenco **percorso** , nella sezione **ambienti del servizio di integrazione** , selezionare lo stesso ISE usato dalle app per la logica, ad esempio:

   ![Selezionare l'ambiente del servizio di integrazione](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Collegare l'app per la logica all'account di integrazione nel modo consueto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Continuare aggiungendo elementi all'account di integrazione, ad esempio [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Per gestire gli account di integrazione in ISE, vedere [gestire l'ambiente del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors-in-an-ise"></a>Creare connettori personalizzati in ISE

Per usare i connettori personalizzati in ISE, creare i connettori personalizzati direttamente all'interno di ISE.

1. Trovare e aprire ISE, se non è già aperto. Dal menu ISE, in **Impostazioni**, selezionare **connettori** > personalizzati**Aggiungi**.

   ![Crea connettore personalizzato](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Specificare il nome, la sottoscrizione di Azure e il gruppo di risorse di Azure (nuovo o esistente) da usare per il connettore personalizzato.

1. Dall'elenco **percorso** , nella sezione **ambienti del servizio di integrazione** , selezionare lo stesso ISE usato dalle app per la logica e selezionare **Crea**, ad esempio:

   ![Selezionare l'ambiente del servizio di integrazione](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Selezionare il nuovo connettore personalizzato, quindi selezionare **modifica**, ad esempio:

   ![Selezionare e modificare il connettore personalizzato](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continuare creando il connettore nel modo consueto da una [definizione openapi](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) o [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Per gestire i connettori personalizzati in ISE, vedere [gestire l'ambiente del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md)
