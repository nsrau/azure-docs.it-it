---
title: Aggiungere risorse agli ambienti del servizio di integrazione
description: Aggiungere app per la logica, account di integrazione, connettori personalizzati e connettori gestiti all'ambiente di Integration Services (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 7b9ce8be4ffc306417dd836e28127641f6100321
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899191"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Aggiungere risorse all'ambiente Integration Services (ISE) in app per la logica di Azure

Dopo aver creato un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), aggiungere risorse come app per la logica, account di integrazione e connettori in modo che possano accedere alle risorse nella rete virtuale di Azure. Ad esempio, i connettori ISE gestiti che diventano disponibili dopo la creazione di ISE non vengono visualizzati automaticamente nella finestra di progettazione dell'app per la logica. Prima di poter usare questi connettori ISE, è necessario [aggiungerli e distribuirli](#add-ise-connectors-environment) manualmente in ISE, in modo che vengano visualizzati nella finestra di progettazione dell'app per la logica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* ISE creato per eseguire le app per la logica. Se non si dispone di un ISE, [creare prima un ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Per creare, aggiungere o aggiornare le risorse distribuite in un ISE, è necessario assegnare il ruolo di proprietario o collaboratore a tale ISE oppure avere le autorizzazioni ereditate tramite la sottoscrizione di Azure o il gruppo di risorse di Azure associato a ISE. Per gli utenti che non dispongono di autorizzazioni proprietario, collaboratore o ereditato, è possibile assegnare il ruolo Collaboratore ambiente del servizio di integrazione o ambiente del servizio di integrazione ruolo sviluppatore. Per ulteriori informazioni sul controllo degli accessi in base al ruolo (RBAC), vedere [che cos'è il controllo degli accessi in base al ruolo per le risorse di Azure](../role-based-access-control/overview.md)?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Creare app per la logica

Per compilare app per la logica che vengono eseguite nell'ambiente Integration Services (ISE), seguire questa procedura:

1. Trovare e aprire ISE, se non è già aperto. Dal menu ISE in **Impostazioni**selezionare app per la **logica** > **Aggiungi**.

   ![Aggiungere una nuova app per la logica ad ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Fornire informazioni sull'app per la logica che si vuole creare, ad esempio:

   ![Selezionare l'ambiente del servizio di integrazione](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Sì | Nome dell'app per la logica da creare |
   | **Sottoscrizione** | Sì | Nome della sottoscrizione di Azure da usare. |
   | **Gruppo di risorse** | Sì | Nome del gruppo di risorse di Azure (nuovo o esistente) da usare |
   | **Posizione** | Sì | In **ambienti del servizio di integrazione**selezionare ISE da usare, se non è già selezionato. <p><p> **Importante**: per usare le app per la logica con un account di integrazione, è necessario che entrambi usino lo stesso ISE. |
   ||||

1. Al termine, selezionare **Crea**.

1. Continuare [a creare l'app per la logica nel modo consueto](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Per le differenze nel funzionamento dei trigger e delle azioni e del modo in cui vengono etichettati quando si usa ISE rispetto al servizio app per la logica multi-tenant, vedere [isolated e multi-tenant nella Panoramica di ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Per gestire le app per la logica e le connessioni API in ISE, vedere [gestire l'ambiente del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Creare gli account di integrazione

In base allo [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selezionato al momento della creazione, ISE include un utilizzo specifico dell'account di integrazione senza costi aggiuntivi. Le app per la logica esistenti in un ambiente di Integration Services possono fare riferimento solo agli account di integrazione presenti nello stesso ISE. Quindi, affinché un account di integrazione funzioni con le app per la logica in un ISE, l'account di integrazione e le app per la logica devono usare lo *stesso ambiente* del percorso. Per altre informazioni sugli account di integrazione e ISEs, vedere [account di integrazione con ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Per creare un account di integrazione che usa ISE, seguire questa procedura:

1. Trovare e aprire ISE, se non è già aperto. Dal menu ISE, in **Impostazioni**, selezionare **account di integrazione** > **Aggiungi**.

   ![Aggiungi nuovo account di integrazione a ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Fornire informazioni sull'app per la logica che si vuole creare, ad esempio:

   ![Selezionare l'ambiente del servizio di integrazione](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Sì | Nome dell'account di integrazione che si desidera creare |
   | **Sottoscrizione** | Sì | Nome della sottoscrizione di Azure che si vuole usare |
   | **Gruppo di risorse** | Sì | Nome del gruppo di risorse di Azure (nuovo o esistente) da usare |
   | **Piano tariffario** | Sì | Piano tariffario da usare per l'account di integrazione |
   | **Posizione** | Sì | In **ambienti del servizio di integrazione**selezionare lo stesso ISE usato dalle app per la logica, se non è già selezionato. <p><p> **Importante**: per usare l'account di integrazione con le app per la logica, è necessario che entrambi usino lo stesso ISE. |
   ||||

1. Al termine, selezionare **Crea**.

1. [Collegare l'app per la logica all'account di integrazione nel modo consueto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Continuare aggiungendo risorse all'account di integrazione, ad esempio [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Per gestire gli account di integrazione in ISE, vedere [gestire l'ambiente del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Aggiungi connettori ISE

I connettori gestiti da Microsoft che diventano disponibili dopo la creazione di ISE non vengono visualizzati automaticamente in selezione connettore nella finestra di progettazione dell'app per la logica. Prima di poter usare questi connettori ISE, è necessario aggiungerli e distribuirli manualmente in ISE, in modo che vengano visualizzati nella finestra di progettazione dell'app per la logica.

1. Nel menu ISE, in **Impostazioni**, selezionare **connettori gestiti**. Sulla barra degli strumenti fare clic su **Aggiungi**.

   ![Visualizzazione di connettori gestiti](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Nel riquadro **Aggiungi un nuovo connettore gestito** aprire l'elenco **trova connettore** . Selezionare il connettore ISE che si vuole usare ma non è ancora distribuito in ISE. Selezionare **Crea**.

   ![Selezionare il connettore ISE che si vuole distribuire in ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Solo i connettori ISE che sono idonei, ma non ancora distribuiti in ISE, saranno disponibili per la selezione. I connettori già distribuiti in ISE non sono disponibili per la selezione.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Creare connettori personalizzati

Per usare i connettori personalizzati in ISE, creare i connettori personalizzati direttamente all'interno di ISE.

1. Trovare e aprire ISE, se non è già aperto. Dal menu ISE, in **Impostazioni**, selezionare **connettori personalizzati** > **Aggiungi**.

   ![Crea connettore personalizzato](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Specificare il nome, la sottoscrizione di Azure e il gruppo di risorse di Azure (nuovo o esistente) da usare per il connettore personalizzato.

1. Dall'elenco **percorso** , nella sezione **ambienti del servizio di integrazione** , selezionare lo stesso ISE usato dalle app per la logica e selezionare **Crea**, ad esempio:

   ![Selezionare l'ambiente del servizio di integrazione](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Selezionare il nuovo connettore personalizzato, quindi selezionare **modifica**, ad esempio:

   ![Selezionare e modificare il connettore personalizzato](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continuare creando il connettore nel modo consueto da una [definizione openapi](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) o [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Per gestire i connettori personalizzati in ISE, vedere [gestire l'ambiente del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md)
