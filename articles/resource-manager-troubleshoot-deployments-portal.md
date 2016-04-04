<properties
   pageTitle="Risoluzione dei problemi relativi alle distribuzioni con il portale | Microsoft Azure"
   description="Questa sezione descrive come usare il portale di Azure per rilevare e correggere i problemi relativi alla distribuzione di Gestione risorse."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure

> [AZURE.SELECTOR]
- [Portale](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interfaccia della riga di comando di Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se è stato riscontrato un errore durante la distribuzione di risorse in Azure, è necessario risolvere la causa dell'errore. Il portale di Azure offre un'interfaccia che consente di individuare facilmente gli errori e determinare potenziali correzioni.

È possibile risolvere i problemi relativi alla distribuzione esaminando i log di controllo o le operazioni di distribuzione. Questo argomento illustra entrambi i metodi.

È possibile evitare alcuni errori convalidando il modello e l'infrastruttura prima della distribuzione. Per altre informazioni, vedere [Distribuire un gruppo di risorse con un modello di Azure Resource Manager](resource-group-template-deploy.md).

## Usare dei log di controllo per risolvere i problemi

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Per visualizzare gli errori per una distribuzione, attenersi alla procedura seguente:

1. Visualizzare i log di controllo tramite il portale selezionando **Sfoglia** e **Log di controllo**.

    ![selezionare i log di controllo](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. Nel pannello **Log di controllo** verrà visualizzato un riepilogo delle operazioni recenti per tutti i gruppi di risorse nella sottoscrizione. Tale riepilogo include una rappresentazione grafica dell'ora e dello stato delle operazioni, nonché un elenco delle operazioni.

    ![visualizzare le azioni](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. È possibile selezionare una delle operazioni nell'elenco. Selezionare l'operazione che contiene l'errore che si desidera esaminare.

    ![selezionare l'operazione](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
4. Verranno visualizzati tutti gli eventi per tale operazione. Si notino gli **ID di correlazione** nel riepilogo. Questo ID viene usato per tenere traccia degli eventi correlati. Può essere utile quando si collabora con il supporto tecnico per risolvere un problema. È possibile selezionare qualsiasi evento per visualizzare i relativi dettagli.

    ![selezionare l'evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

5. Verranno visualizzati i dettagli sull'evento. In particolare, prestare attenzione all'opzione **Proprietà** per le informazioni sull'errore.

    ![mostrare i dettagli dei log di controllo](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

È possibile filtrare la visualizzazione dei log di controllo per concentrarsi su condizioni specifiche. Per personalizzare la visualizzazione del log di controllo:

1. Selezionare **Filtro** nella parte superiore del pannello **Log di controllo**.

    ![filtrare i log](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

2. Nel pannello **Filtro** selezionare le condizioni per limitare la visualizzazione dei log di controllo solo per le operazioni che si desidera visualizzare. Ad esempio, è possibile filtrare le operazioni per visualizzare solo gli errori per un gruppo di risorse specifico.

    ![impostare le opzioni di filtro](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

3. È possibile filtrare ulteriormente le operazioni impostando un intervallo di tempo. Nell'immagine seguente la visualizzazione viene filtrata per un intervallo di tempo specifico di 20 minuti.

    ![impostare l'ora](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

Dopo l'aggiornamento della visualizzazione dei log di controllo, verranno visualizzate solo le operazioni che soddisfano la condizione specificata. Tali impostazioni vengono mantenute alla successiva visualizzazione dei log di controllo, quindi potrebbe essere necessario modificare tali valori per ampliare la visualizzazione delle operazioni.

È auspicabile che sia stato possibile scoprire il motivo della distribuzione non riuscita. Per informazioni sullo stato, è anche possibile esaminare le operazioni di distribuzione, come illustrato nella sezione successiva.

## Usare le operazioni di distribuzione per risolvere i problemi

Per visualizzare le operazioni di distribuzione, attenersi alla procedura seguente:

1. Per il gruppo di risorse coinvolte nella distribuzione, si noti lo stato dell'ultima distribuzione. È possibile selezionare questo stato per ottenere altri dettagli.

    ![Stato della distribuzione](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Verrà visualizzata la cronologia di distribuzione recente. Selezionare la distribuzione non riuscita.

    ![Stato della distribuzione](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Visualizzare le informazioni sulla distribuzione, quindi selezionare l'operazione non riuscita per visualizzare i dettagli sull'errore.

    ![visualizzare la distribuzione non riuscita](./media/resource-manager-troubleshoot-deployments-portal/view-failed-deployment.png)

4. Nel pannello **Dettagli operazione** verranno visualizzate le informazioni sull'operazione non riuscita. In particolare, prestare attenzione al messaggio di stato.

    ![visualizzare il messaggio di stato](./media/resource-manager-troubleshoot-deployments-portal/operations-status.png)



## Passaggi successivi

- Per altre informazioni sull'uso dei log di controllo per monitorare altri tipi di azioni, vedere [Operazioni di controllo con Gestione risorse](resource-group-audit.md).
- Per convalidare la distribuzione prima dell'esecuzione, vedere [Distribuire un gruppo di risorse con un modello di Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->