---
title: Gestire gli ambienti del servizio di integrazione in app per la logica di Azure
description: Controllare l'integrità della rete e gestire app per la logica, connessioni, connettori personalizzati e account di integrazione in Integration Services Environment (ISE) per app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1d91813e0f39207bcf7768de89600a6bdee0fc53
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792631"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gestire l'ambiente Integration Services (ISE) in app per la logica di Azure

Per verificare lo stato di integrità della rete per l' [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) e gestire le app per la logica, le connessioni, gli account di integrazione e i connettori presenti in ISE, seguire i passaggi descritti in questo argomento. Per aggiungere questi elementi a ISE, vedere [aggiungere elementi all'ambiente del servizio di integrazione](../logic-apps/add-artifacts-integration-service-environment-ise.md).

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

È possibile visualizzare e gestire le app per la logica che si trovano in ISE.

1. Nel menu ISE selezionare app per la **logica**in **Impostazioni**.

   ![Visualizzare le app per la logica](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Per rimuovere le app per la logica che non sono più necessarie in ISE, selezionare le app per la logica e quindi fare clic su **Elimina**. Per confermare che si desidera eliminare, selezionare **Sì**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gestisci connessioni API

È possibile visualizzare e gestire le connessioni create dalle app per la logica in esecuzione in ISE.

1. Nel menu ISE, in **Impostazioni**, selezionare **connessioni API**.

   ![Visualizzare le connessioni API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Per rimuovere le connessioni che non sono più necessarie in ISE, selezionare le connessioni e quindi selezionare **Elimina**. Per confermare che si desidera eliminare, selezionare **Sì**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Gestisci connettori ISE

È possibile visualizzare e gestire i connettori API distribuiti in ISE.

1. Nel menu ISE, in **Impostazioni**, selezionare **connettori gestiti**.

   ![Visualizzazione di connettori gestiti](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Per rimuovere i connettori che non si desidera siano disponibili in ISE, selezionarli e quindi selezionare **Elimina**. Per confermare che si desidera eliminare, selezionare **Sì**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gestire i connettori personalizzati

È possibile visualizzare e gestire i connettori personalizzati distribuiti in ISE.

1. Nel menu ISE, in **Impostazioni**, selezionare **connettori personalizzati**.

   ![Trovare connettori personalizzati](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Per rimuovere i connettori personalizzati che non sono più necessari in ISE, selezionare i connettori e quindi selezionare **Elimina**. Per confermare che si desidera eliminare, selezionare **Sì**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gestire gli account di integrazione

1. Nel menu ISE, in **Impostazioni**, selezionare **account di integrazione**.

   ![Trovare gli account di integrazione](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Per rimuovere gli account di integrazione da ISE quando non sono più necessari, selezionare gli account di integrazione e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [connettersi alle reti virtuali di Azure da app per la logica isolate](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
