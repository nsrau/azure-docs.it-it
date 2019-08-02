---
title: Gestire gli ambienti del servizio di integrazione in app per la logica di Azure
description: Controllare l'integrità della rete e gestire app per la logica, connessioni, connettori personalizzati e account di integrazione in Integration Services Environment (ISE) per app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517462"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gestire l'ambiente Integration Services (ISE) in app per la logica di Azure

Per verificare lo stato di integrità della rete per l' [ambiente di Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) e gestire le app per la logica, le connessioni, gli account di integrazione e i connettori personalizzati presenti in ISE, attenersi alla procedura descritta in questo argomento.

## <a name="view-your-ise"></a>Visualizza ISE

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella casella di ricerca del portale immettere "Integration Services environments" e quindi selezionare **Integration Services environments**.

   ![Trovare gli ambienti del servizio di integrazione](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Dall'elenco dei risultati selezionare l'ambiente del servizio di integrazione.

   ![Selezionare l'ambiente del servizio di integrazione](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Passare alle sezioni successive per trovare le app per la logica, le connessioni, i connettori o gli account di integrazione in ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Controllare l'integrità della rete

Nel menu ISE selezionare **integrità rete**in **Impostazioni**. Questo riquadro Mostra lo stato di integrità per le subnet e le dipendenze in uscita da altri servizi.

![Controllare l'integrità della rete](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Gestire le app per la logica

1. Nel menu ISE selezionare app per la **logica**in **Impostazioni**.

   ![Trovare app per la logica](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Per rimuovere le app per la logica da ISE quando non sono più necessarie, selezionare le app per la logica e quindi fare clic su **Elimina**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gestisci connessioni API

1. Per visualizzare le connessioni API create dalle app per la logica in esecuzione in ISE, nel menu ISE, in **Impostazioni**, selezionare **connessioni API**.

   ![Trovare le connessioni API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Per rimuovere le connessioni da ISE quando non è più necessario, selezionare le connessioni e quindi selezionare **Elimina**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gestire i connettori personalizzati

1. Per visualizzare i connettori personalizzati creati in ISE, nel menu ISE, in **Impostazioni**, selezionare **connettori personalizzati**.

   ![Trovare connettori personalizzati](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Per rimuovere i connettori personalizzati da ISE quando non sono più necessari, selezionarli e quindi selezionare **Elimina**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gestire gli account di integrazione

1. Nel menu ISE, in **Impostazioni**, selezionare **account di integrazione**.

   ![Trovare gli account di integrazione](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Per rimuovere gli account di integrazione da ISE quando non sono più necessari, selezionare gli account di integrazione e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [connettersi alle reti virtuali di Azure da app per la logica isolate](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
