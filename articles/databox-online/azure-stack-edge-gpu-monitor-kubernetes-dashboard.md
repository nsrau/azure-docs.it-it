---
title: Monitorare il dispositivo Azure Stack Edge Pro tramite il dashboard di Kubernetes | Microsoft Docs
description: Viene descritto come accedere e usare il dashboard di Kubernetes per monitorare il dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: 1a4f22e7ae3cc60d0a16b24a1f0e5f93d3a86d8c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899197"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Usare il dashboard di Kubernetes per monitorare il dispositivo GPU Pro Azure Stack Edge

Questo articolo descrive come accedere e usare il dashboard di Kubernetes per monitorare il dispositivo GPU Pro Azure Stack Edge. Per monitorare il dispositivo, è possibile usare i grafici in portale di Azure, visualizzare il dashboard di Kubernetes o eseguire `kubectl` comandi tramite l'interfaccia di PowerShell del dispositivo. 

Questo articolo è incentrato solo sulle attività di monitoraggio che possono essere eseguite nel dashboard di Kubernetes.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Accedere al dashboard di Kubernetes nel dispositivo
> * Visualizzare i moduli distribuiti nel dispositivo
> * Ottenere l'indirizzo IP per le applicazioni distribuite nel dispositivo
> * Visualizzare i log dei contenitori per i moduli distribuiti nel dispositivo


## <a name="about-kubernetes-dashboard"></a>Informazioni sul dashboard di Kubernetes

Il dashboard di Kubernetes è un'interfaccia utente basata sul Web che è possibile usare per risolvere i problemi delle applicazioni incluse in contenitori. Il dashboard di Kubernetes è un'alternativa basata sull'interfaccia utente alla riga di comando di Kubernetes `kubectl` . Per altre informazioni, vedere [Dashboard Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

Sul dispositivo Azure Stack Edge Pro, è possibile usare il dashboard di Kubernetes in modalità di sola *lettura* per ottenere una panoramica delle applicazioni in esecuzione sul dispositivo Azure stack Edge Pro, visualizzare lo stato delle risorse del cluster Kubernetes e visualizzare gli eventuali errori che si sono verificati nel dispositivo.

## <a name="access-dashboard"></a>Accedi al dashboard

Il dashboard Kubernetes è di sola *lettura* e viene eseguito nel nodo master Kubernetes sulla porta 31000. Per accedere al dashboard, seguire questa procedura: 

1. Nell'interfaccia utente locale del dispositivo passare a **dispositivo** , quindi passare a **endpoint dispositivo**. 
1. Selezionare **download config** per scaricare un `kubeconfig` che consente di accedere al dashboard. Salvare il `config.json` file nel sistema locale.
1. Selezionare l'URL del dashboard di Kubernetes per aprire il dashboard in un browser.

    ![URL dashboard Kubernetes nella pagina dispositivo nell'interfaccia utente locale](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. Nella pagina **di accesso del dashboard di Kubernetes** :
    
    1. Selezionare **kubeconfig**. 
        ![Selezionare l'opzione kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. Selezionare i puntini di sospensione **...**. Individuare e selezionare il `kubeconfig` scaricato in precedenza nel sistema locale. Selezionare **Accedi**.
        ![Passare al file kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. È ora possibile visualizzare il dashboard di Kubernetes per il dispositivo Azure Stack Edge Pro in modalità di sola lettura.

    ![Pagina principale del dashboard Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Visualizza lo stato del modulo

I moduli di calcolo sono contenitori per i quali è implementata una logica di business. È possibile usare il dashboard per verificare se un modulo di calcolo è stato distribuito correttamente sul dispositivo Azure Stack Edge Pro.

PER visualizzare lo stato del modulo, seguire questa procedura nel Dashboard:

1. Nel riquadro sinistro del Dashboard passare a **spazio dei nomi**. Filtrare in base allo spazio dei nomi in cui vengono visualizzati i moduli IoT Edge, in questo caso **iotedge**.
1. Nel riquadro sinistro passare a **carichi di lavoro > distribuzioni**.
1. Nel riquadro di destra vengono visualizzati tutti i moduli distribuiti nel dispositivo. In questo caso, un modulo GettingStartedWithGPU è stato distribuito in Azure Stack Edge Pro. È possibile osservare che il modulo è stato distribuito.

    ![Visualizza distribuzione moduli](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Ottenere l'indirizzo IP per i servizi o i moduli

È possibile usare il dashboard per ottenere gli indirizzi IP dei servizi o dei moduli che si vuole esporre all'esterno del cluster Kubernetes. 

Si assegna l'intervallo di indirizzi IP per questi servizi esterni tramite l'interfaccia utente Web locale del dispositivo nella pagina delle **impostazioni di rete di calcolo** . Dopo aver distribuito IoT Edge moduli, è possibile che si desideri ottenere l'indirizzo IP assegnato a un modulo o un servizio specifico. 

Per ottenere l'indirizzo IP, seguire questa procedura nel Dashboard:

1. Nel riquadro sinistro del Dashboard passare a **spazio dei nomi**. Filtrare in base allo spazio dei nomi in cui viene distribuito un servizio esterno, in questo caso **iotedge**.
1. Nel riquadro sinistro passare a **individuazione e bilanciamento del carico > Servizi**.
1. Nel riquadro di destra verranno visualizzati tutti i servizi in esecuzione nello `iotedge` spazio dei nomi nel dispositivo Azure stack Edge Pro.

    ![Ottenere l'indirizzo IP per i servizi esterni](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Visualizzare i log dei contenitori

Sono presenti istanze in cui è necessario visualizzare i log del contenitore. È possibile usare il dashboard per ottenere i log per un contenitore specifico che è stato distribuito nel cluster Kubernetes.

Per visualizzare i log dei contenitori, seguire questa procedura nel Dashboard:

1. Nel riquadro sinistro del Dashboard passare a **spazio dei nomi**. Filtrare in base allo spazio dei nomi in cui vengono distribuiti i moduli IoT Edge, in questo caso **iotedge**.
1. Nel riquadro sinistro passare a **carichi di lavoro > Pod**.
1. Nel riquadro di destra vengono visualizzati tutti i pod in esecuzione nel dispositivo. Identificare il pod in cui è in esecuzione il modulo per il quale si desidera visualizzare i log. Selezionare i puntini di sospensione verticali per il Pod identificato dall'utente e scegliere **registri**dal menu di scelta rapida.

    ![Visualizzare i log dei contenitori 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. I log vengono visualizzati in un visualizzatore di log incorporato nel dashboard. È anche possibile scaricare i log.

    ![Visualizzare i log del contenitore 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>Visualizza CPU, utilizzo memoria

Il dashboard di Kubernetes per il dispositivo Azure Stack Edge Pro dispone anche di un [componente aggiuntivo](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) per il server delle metriche che aggrega l'utilizzo della CPU e della memoria tra le risorse di Kubernetes.
 
Ad esempio, è possibile visualizzare la CPU e la memoria utilizzate tra le distribuzioni in tutti gli spazi dei nomi. 

![Visualizzare l'utilizzo della CPU e della memoria in tutte le distribuzioni](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

È anche possibile filtrare in base a uno spazio dei nomi specifico. Nell'esempio seguente, è possibile visualizzare l'utilizzo di CPU e memoria solo per le distribuzioni di Azure Arc.  

![Visualizzare l'utilizzo della CPU e della memoria per le distribuzioni di Azure Arc](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Il server delle metriche Kubernetes fornisce pipeline di scalabilità automatica simili a quelle del dispositivo di [scalabilità automatica di Pod orizzontali](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come risolvere i problemi relativi a Kubernetes <!--insert link-->.
