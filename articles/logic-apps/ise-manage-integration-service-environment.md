---
title: Gestire gli ambienti del servizio di integrazione nelle app per la logica di AzureManage integration service environments in Azure Logic Apps
description: Controllare l'integrità della rete e gestire app per la logica, connessioni, connettori personalizzati e account di integrazione nell'ambiente del servizio di integrazione (ISE) per le app per la logica di AzureCheck network health and manage logic apps, connections, custom connectors, and integration accounts in your integration service environment (ISE) for Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f48106be67763c093a183be01098cab74391752e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284200"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gestire l'ambiente del servizio di integrazione (ISE) nelle app per la logica di AzureManage your integration service environment (ISE) in Azure Logic Apps

In questo articolo viene illustrato come eseguire attività di gestione per l'ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)ad esempio:This article shows how to perform management tasks for your integration service environment (ISE) , for example:

* Gestire le risorse, ad esempio app per la logica, le connessioni, gli account di integrazione e i connettori nell'ISE.
* Controllare l'integrità della rete ISE.
* Aggiungere capacità, riavviare ISE o eliminare ISE, seguire i passaggi in questo argomento. Per aggiungere questi elementi a ISE, vedere [Aggiungere elementi all'ambiente](../logic-apps/add-artifacts-integration-service-environment-ise.md)del servizio di integrazione.

## <a name="view-your-ise"></a>Visualizza il tuo ISE

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Nella casella di ricerca del portale immettere "Ambienti del servizio di integrazione", quindi selezionare Ambienti del servizio di **integrazione**.

   ![Trovare gli ambienti del servizio di integrazioneFind integration service environments](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Nell'elenco dei risultati selezionare l'ambiente del servizio di integrazione.

   ![Selezionare l'ambiente del servizio di integrazione](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Passare alle sezioni successive per trovare app per la logica, connessioni, connettori o account di integrazione in ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Controllare l'integrità della rete

Nel menu ISE, in **Impostazioni**, selezionare **Integrità rete**. Questo riquadro mostra lo stato di integrità per le subnet e le dipendenze in uscita su altri servizi.

![Controllare l'integrità della rete](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Gestire le app per la logica

È possibile visualizzare e gestire le app per la logica presenti in ISE.

1. Nel menu ISE, in **Impostazioni,** selezionare **App per**la logica .

   ![Visualizzare le app per la logica](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Per rimuovere le app per la logica non più necessarie in ISE, selezionare tali app per la logica e quindi **elimina**. Per confermare l'eliminazione, selezionare **Sì**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gestire le connessioni API

È possibile visualizzare e gestire le connessioni create dalle app per la logica in esecuzione in ISE.

1. Nel menu ISE, in **Impostazioni,** selezionare **Connessioni API**.

   ![Visualizzare le connessioni API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Per rimuovere le connessioni non più necessarie in ISE, selezionare tali connessioni e quindi **eliminare**. Per confermare l'eliminazione, selezionare **Sì**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Gestire i connettori ISE

È possibile visualizzare e gestire i connettori API distribuiti in ISE.

1. Nel menu ISE, in **Impostazioni**, selezionare **Connettori gestiti**.

   ![Visualizzare i connettori gestiti](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Per rimuovere i connettori che non si desidera siano disponibili in ISE, selezionare tali connettori e quindi **elimina**. Per confermare l'eliminazione, selezionare **Sì**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gestire connettori personalizzati

È possibile visualizzare e gestire i connettori personalizzati distribuiti in ISE.

1. Nel menu ISE, in **Impostazioni**, selezionare **Connettori personalizzati**.

   ![Trovare connettori personalizzati](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Per rimuovere i connettori personalizzati non più necessari in ISE, selezionare tali connettori e quindi **elimina**. Per confermare l'eliminazione, selezionare **Sì**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gestire gli account di integrazione

1. Nel menu ISE, in **Impostazioni,** selezionare **Account di integrazione.**

   ![Trovare gli account di integrazione](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Per rimuovere gli account di integrazione da ISE quando non sono più necessari, selezionare tali account di integrazione e quindi selezionare **Elimina**.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Aggiungere la capacità ISE

L'unità di base Premium ISE ha una capacità fissa, quindi se hai bisogno di più velocità effettiva, puoi aggiungere più unità di scala, durante la creazione o in seguito. Lo SKU per sviluppatori non include la possibilità di aggiungere unità di scala.

1. Nel [portale di Azure](https://portal.azure.com)passare all'ISE.

1. Per esaminare le metriche di utilizzo e prestazioni per ISE, scegliere **Panoramica**dal menu ISE .

   ![Visualizzare l'utilizzo di ISE](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. In **Impostazioni**selezionare **Scalabilità orizzontale**. Nel riquadro Configura selezionare una delle opzioni seguenti:On the **Configure** pane, select from these options:

   * [**Scala manuale**](#manual-scale): Scala in base al numero di unità di elaborazione che si desidera utilizzare.
   * [**Scalabilità automatica personalizzata:**](#custom-autoscale)scalabilità in base alle metriche delle prestazioni selezionando tra vari criteri e specificando le condizioni di soglia per soddisfare tali criteri.

   ![Selezionare il tipo di scala desiderato](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Scalabilità manuale

1. Dopo aver selezionato **Scala manuale**, per **Capacità aggiuntiva**, selezionare il numero di unità di scala che si desidera utilizzare.

   ![Selezionare il tipo di scala desiderato](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Al termine, selezionare **Salva**.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Scalabilità automatica personalizzata

1. Dopo aver selezionato **Scalabilità automatica personalizzata**, per **Nome impostazione scalabilità automatica**, specificare un nome per l'impostazione e, facoltativamente, selezionare il gruppo di risorse di Azure a cui appartiene l'impostazione.

   ![Specificare il nome per l'impostazione di scalabilità automatica e selezionare il gruppo di risorseProvide name for autoscale setting and select resource group](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Per la condizione **Predefinito,** selezionare Scala in base a **una metrica** o Scala a un numero di **istanze specifico.**

   * Se si sceglie basato sull'istanza, immettere il numero per le unità di elaborazione, ovvero un valore compreso tra 0 e 10.

   * Se scegli basato su metriche, segui questi passaggi:

     1. Nella sezione **Regole** selezionare **Aggiungi una regola**.

     1. Nel riquadro Regola di **scalabilità** impostare i criteri e le azioni da eseguire quando la regola viene attivata.

     1. Per **Limiti istanza**, specificare i seguenti valori:

        * **Minimo**: Il numero minimo di unità di elaborazione da utilizzare
        * **Massimo**: Il numero massimo di unità di elaborazione da utilizzare
        * **Impostazione predefinita:** se si verificano problemi durante la lettura delle metriche delle risorse e la capacità corrente è inferiore alla capacità predefinita, la scalabilità automatica viene scalata al numero predefinito di unità di elaborazione. Tuttavia, se la capacità corrente supera la capacità predefinita, la scalabilità automatica non viene ridimensionata.

1. Per aggiungere un'altra condizione, selezionare **Aggiungi condizione**di scala .

1. Al termine delle impostazioni di scalabilità automatica, salvare le modifiche.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Riavviare ISE

Se si modificano le impostazioni del server DNS o DNS, è necessario riavviare ISE in modo che ISE possa rilevare tali modifiche. Il riavvio di uno SKU Premium ISE non comporta tempi di inattività a causa della ridondanza e dei componenti che vengono riavviati uno alla volta durante il riciclo. Tuttavia, un ISE SKU per sviluppatori si verifica tempi di inattività perché non esiste alcuna ridondanza. Per ulteriori informazioni, vedere [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. Nel [portale di Azure](https://portal.azure.com)passare all'ISE.

1. Scegliere **Panoramica**dal menu ISE . Nella barra degli strumenti Panoramica, **Riavvia**.

   ![Riavviare l'ambiente del servizio di integrazioneRestart integration service environment](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Eliminare l'ISE

Prima di eliminare un ISE non più necessario o un gruppo di risorse di Azure che contiene un ISE, verificare di non disporre di criteri o blocchi nel gruppo di risorse di Azure che contiene queste risorse o nella rete virtuale di Azure perché questi elementi possono bloccare l'eliminazione.

Dopo aver eliminato ISE, potrebbe essere necessario attendere fino a 9 ore prima di tentare di eliminare la rete virtuale o le subnet di Azure.After you delete your ISE, you might have to wait up to 9 hours before you try to delete your Azure virtual network or subnets.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere risorse agli ambienti del servizio di integrazioneAdd resources to integration service environments](../logic-apps/add-artifacts-integration-service-environment-ise.md)