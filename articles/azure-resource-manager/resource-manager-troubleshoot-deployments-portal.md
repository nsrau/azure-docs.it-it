---
title: Visualizzare le operazioni di distribuzione con il portale | Microsoft Docs
description: Questo articolo descrive come usare il portale di Azure per rilevare gli errori di distribuzione di Resource Manager.
services: azure-resource-manager,virtual-machines
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 2b09c887-5a79-418f-85d0-5e8313b3556c
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 06/15/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f6e684b08ed481cdf84faf2b8426da72f98fc58c
ms.openlocfilehash: bd999b9c9c0f0985493e154f1a4851d4a10df9cd


---
# <a name="view-deployment-operations-with-azure-portal"></a>Visualizzare le operazioni di distribuzione con il portale di Azure
> [!div class="op_single_selector"]
> * [Portale](resource-manager-troubleshoot-deployments-portal.md)
> * [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
> * [Interfaccia della riga di comando di Azure](resource-manager-troubleshoot-deployments-cli.md)
> * [API REST](resource-manager-troubleshoot-deployments-rest.md)
> 
> 

È possibile visualizzare le operazioni per una distribuzione tramite il portale di Azure. È possibile che si sia più interessati a visualizzare le operazioni quando si riceve un errore durante la distribuzione, quindi questo articolo è incentrato sulla visualizzazione delle operazioni non riuscite. Il portale offre un'interfaccia che consente di individuare facilmente gli errori e determinare le potenziali correzioni.

[!INCLUDE [resource-manager-troubleshoot-introduction](../../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="use-deployment-operations-to-troubleshoot"></a>Usare le operazioni di distribuzione per risolvere i problemi
Per visualizzare le operazioni di distribuzione, attenersi alla procedura seguente:

1. Per il gruppo di risorse coinvolte nella distribuzione, si noti lo stato dell'ultima distribuzione. È possibile selezionare questo stato per ottenere altri dettagli.
   
    ![Stato della distribuzione](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)
2. Verrà visualizzata la cronologia di distribuzione recente. Selezionare la distribuzione non riuscita.
   
    ![Stato della distribuzione](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)
3. Selezionare **Operazione non è riuscita. Fare clic qui per i dettagli** per visualizzare una descrizione del motivo per cui la distribuzione non è riuscita. Nell'immagine seguente il record DNS non è univoco.  
   
    ![visualizzare la distribuzione non riuscita](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)
   
    Questo messaggio di errore dovrebbe essere sufficiente per iniziare la risoluzione dei problemi. Tuttavia, se sono necessari altri dettagli sulle attività completate, è possibile visualizzare le operazioni, come illustrato nei passaggi seguenti.
4. È possibile visualizzare tutte le operazioni di distribuzione nel pan nello **Distribuzione** . Selezionare un'operazione per visualizzare altri dettagli.
   
    ![visualizzare operazioni](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)
   
    In questo caso, si noterà che l'account di archiviazione, la rete virtuale e il set di disponibilità sono stati creati correttamente. L'indirizzo IP pubblico non è riuscito e non si sono state tentate altre risorse.
5. È possibile visualizzare gli eventi relativi alla distribuzione selezionando **venti**.
   
    ![visualizzare eventi](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)
6. Visualizzare tutti gli eventi per la distribuzione e selezionarne una per altri dettagli.
   
    ![vedere eventi](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## <a name="use-audit-logs-to-troubleshoot"></a>Usare i log di controllo per risolvere i problemi
[!INCLUDE [resource-manager-audit-limitations](../../includes/resource-manager-audit-limitations.md)]

Per visualizzare gli errori per una distribuzione, seguire questa procedura:

1. Visualizzare i log di controllo per un gruppo di risorse selezionando **Log di controllo**.
   
    ![selezionare i log di controllo](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)
2. Nel pannello **Log di controllo** verrà visualizzato un riepilogo delle operazioni recenti per tutti i gruppi di risorse nella sottoscrizione. Tale riepilogo include una rappresentazione grafica dell'ora e dello stato delle operazioni, nonché un elenco delle operazioni.
   
    ![visualizzare le azioni](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)
3. È possibile filtrare la visualizzazione dei log di controllo per concentrarsi su condizioni specifiche. Selezionare **Filtro** nella parte superiore del pannello **Log di controllo**.
   
    ![filtrare i log](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)
4. Nel pannello **Filtro** selezionare le condizioni per limitare la visualizzazione dei log di controllo solo per le operazioni da visualizzare. Ad esempio, è possibile filtrare le operazioni per visualizzare solo gli errori per un gruppo di risorse.
   
    ![impostare le opzioni di filtro](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)
5. È possibile filtrare ulteriormente le operazioni impostando un intervallo di tempo. L'immagine seguente mostra la visualizzazione è filtrata per un intervallo di tempo specifico di 20 minuti.
   
    ![impostare l'ora](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)
6. È possibile selezionare una delle operazioni nell'elenco. Selezionare l'operazione che contiene l'errore che si desidera esaminare.
   
    ![selezionare l'operazione](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
7. Verranno visualizzati tutti gli eventi per tale operazione. Si notino gli **ID di correlazione** nel riepilogo. Questo ID viene usato per tenere traccia degli eventi correlati. Può essere utile quando si collabora con il supporto tecnico per risolvere un problema. È possibile selezionare qualsiasi evento per visualizzare i relativi dettagli.
   
    ![selezionare l'evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)
8. Verranno visualizzati i dettagli sull'evento. In particolare, prestare attenzione all'opzione **Proprietà** per informazioni sull'errore.
   
    ![mostrare i dettagli dei log di controllo](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Il filtro applicato al log di controllo viene mantenuto nella successiva visualizzazione, quindi potrebbe essere necessario modificare tali valori per ampliare la visualizzazione delle operazioni.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla risoluzione di errori di distribuzione specifici vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per informazioni sull'uso dei log di controllo per monitorare altri tipi di azioni vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
* Per convalidare la distribuzione prima di eseguirla vedere [Distribuire un gruppo di risorse con un modello di Azure Resource Manager](resource-group-template-deploy.md).




<!--HONumber=Nov16_HO3-->


