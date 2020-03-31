---
title: Aggiungere risorse agli ambienti del servizio di integrazioneAdd resources to integration service environments
description: Aggiungere app per la logica, account di integrazione, connettori personalizzati e connettori gestiti all'ambiente del servizio di integrazione (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164878"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Aggiungere risorse all'ambiente del servizio di integrazione (ISE) nelle app per la logica di AzureAdd resources to your integration service environment (ISE) in Azure Logic Apps

Dopo aver creato un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)aggiungere risorse quali app per la logica, account di integrazione e connettori in modo che possano accedere alle risorse nella rete virtuale di Azure.After you create an integration service environment (ISE) , add resources such as logic apps, integration accounts, and connectors so that they can access the resources in your Azure virtual network. Ad esempio, i connettori ISE gestiti che diventano disponibili dopo la creazione di ISE non vengono visualizzati automaticamente in Progettazione app per la logica. Prima di poter utilizzare questi connettori ISE, è necessario aggiungere e distribuire manualmente [tali connettori a ISE](#add-ise-connectors-environment) in modo che vengano visualizzati in Progettazione app per la logica.

> [!IMPORTANT]
> Affinché le app per la logica e gli account di integrazione funzionino insieme in isE, entrambi devono usare lo *stesso ISE* della loro posizione.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* ISE creato per eseguire le app per la logica. Se non si dispone di un ISE, [creare prima un ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Per creare, aggiungere o aggiornare le risorse distribuite in un ISE, è necessario disporre del ruolo Proprietario o Collaboratore in tale ISE oppure disporre delle autorizzazioni ereditate tramite la sottoscrizione di Azure o il gruppo di risorse di Azure associato a ISE. Per gli utenti che non dispongono di autorizzazioni di proprietario, collaboratore o ereditate, è possibile assegnare il ruolo Collaboratore ambiente servizio di integrazione o Il ruolo Sviluppatore ambiente servizio di integrazione. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è](../role-based-access-control/overview.md)il controllo degli accessi in base al ruolo per le risorse di Azure?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Creare app per la logica

Per creare app per la logica eseguite nell'ambiente del servizio di integrazione (ISE), attenersi alla seguente procedura:

1. Trova e apri ISE, se non è già aperto. Dal menu ISE, in **Impostazioni**, selezionare **App per** > la logica**Aggiungi**.

   ![Aggiungere una nuova app per la logica a ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Fornire informazioni sull'app per la logica che si desidera creare, ad esempio:Provide information about the logic app that you want to create, for example:

   ![Selezionare l'ambiente del servizio di integrazione](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Sì | Nome dell'app per la logica da creare |
   | **Sottoscrizione** | Sì | Nome della sottoscrizione di Azure da usare. |
   | **Gruppo di risorse** | Sì | Nome del gruppo di risorse di Azure (nuovo o esistente) da usare |
   | **Percorso** | Sì | In Ambienti del servizio di **integrazione**selezionare ISE da utilizzare, se non è già selezionato. <p><p> **Importante**: per usare le app per la logica con un account di integrazione, entrambe devono usare lo stesso ISE. |
   ||||

1. Al termine, selezionare **Crea**.

1. Continuare a [creare l'app per la logica nel modo consueto.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   Per le differenze nel funzionamento dei trigger e delle azioni e nel modo in cui vengono etichettati quando si usa un ISE rispetto al servizio App per la logica multi-tenant, vedere [Isolated versus multi-tenant nella panoramica](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)di ISE .

1. Per gestire le app per la logica e le connessioni API in ISE, vedere [Gestire l'ambiente](../logic-apps/ise-manage-integration-service-environment.md)del servizio di integrazione.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Creare account di integrazione

In base allo [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selezionato al momento della creazione, l'ISE include l'utilizzo di un account di integrazione specifico senza costi aggiuntivi. Le app per la logica presenti in un ambiente del servizio di integrazione (ISE) possono fare riferimento solo agli account di integrazione presenti nello stesso ISE. Pertanto, affinché un account di integrazione funzioni con le app per la logica in un ISE, sia l'account di integrazione che le app per la logica devono usare *lo stesso ambiente* della relativa posizione. Per ulteriori informazioni sugli account di integrazione e gli ISE, vedere Account di [integrazione con ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Per creare un account di integrazione che utilizza un ISE, attenersi alla seguente procedura:

1. Trova e apri ISE, se non è già aperto. Nel menu ISE, in **Impostazioni,** selezionare Account > di **integrazione****Aggiungi**.

   ![Aggiungere un nuovo account di integrazione a ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Fornire informazioni sull'app per la logica che si desidera creare, ad esempio:Provide information about the logic app that you want to create, for example:

   ![Selezionare l'ambiente del servizio di integrazione](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Sì | Nome dell'account di integrazione che si desidera creare |
   | **Sottoscrizione** | Sì | Nome della sottoscrizione di Azure che si vuole usare |
   | **Gruppo di risorse** | Sì | Nome del gruppo di risorse di Azure (nuovo o esistente) da usare |
   | **Piano tariffario** | Sì | Piano tariffario da utilizzare per l'account di integrazione |
   | **Percorso** | Sì | In Ambienti del servizio di **integrazione**selezionare lo stesso ISE usato dalle app per la logica, se non già selezionato. <p><p> **Importante**: per usare il tuo account di integrazione con app per la logica, entrambi devono usare lo stesso ISE. |
   ||||

1. Al termine, selezionare **Crea**.

1. [Collegare l'app per la logica all'account di integrazione nel modo consueto.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. Continuare aggiungendo risorse all'account di integrazione, ad esempio [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) e [accordi](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Per gestire gli account di integrazione in ISE, vedere [Gestire l'ambiente](../logic-apps/ise-manage-integration-service-environment.md)del servizio di integrazione.

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Aggiungere connettori ISE

I connettori gestiti da Microsoft che diventano disponibili dopo la creazione di ISE non vengono visualizzati automaticamente nella selezione connettore in Progettazione app per la logica. Prima di poter utilizzare questi connettori ISE, è necessario aggiungere e distribuire manualmente questi connettori a ISE in modo che vengano visualizzati in Progettazione app per la logica.

1. Nel menu ISE, in **Impostazioni**, selezionare **Connettori gestiti**. Sulla barra degli strumenti fare clic su **Aggiungi**.

   ![Visualizzare i connettori gestiti](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Nel riquadro **Aggiungi un nuovo connettore gestito** aprire l'elenco Trova **connettore.** Selezionare il connettore ISE che si desidera utilizzare ma non è ancora distribuito nell'ISE. Selezionare **Crea**.

   ![Selezionare il connettore ISE che si desidera distribuire](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Solo i connettori ISE idonei ma non ancora distribuiti in ISE vengono visualizzati disponibili per la selezione. I connettori già distribuiti in ISE sembrano non disponibili per la selezione.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Creare connettori personalizzati

Per utilizzare connettori personalizzati in ISE, creare tali connettori personalizzati direttamente all'interno di ISE.

1. Trova e apri ISE, se non è già aperto. Dal menu ISE, in **Impostazioni**, selezionare **Connettori** > personalizzati**Aggiungi**.

   ![Crea connettore personalizzato](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Specificare il nome, la sottoscrizione di Azure e il gruppo di risorse di Azure (nuovo o esistente) da usare per il connettore personalizzato.

1. Nella sezione Ambienti del servizio di **integrazione** dell'elenco **Percorso** selezionare gli stessi ISE utilizzati dalle app per la logica e selezionare **Crea**, ad esempio:

   ![Selezionare l'ambiente del servizio di integrazione](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Selezionare il nuovo connettore personalizzato e quindi **Modifica**, ad esempio:

   ![Selezionare e modificare il connettore personalizzato](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continuare creando il connettore nel modo consueto da una [definizione OpenAPI](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) o [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Per gestire i connettori personalizzati in ISE, vedere [Gestire l'ambiente](../logic-apps/ise-manage-integration-service-environment.md)del servizio di integrazione.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md)
