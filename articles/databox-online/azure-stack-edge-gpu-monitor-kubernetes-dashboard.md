---
title: Monitorare il dispositivo Azure Stack Edge tramite il dashboard di Kubernetes | Microsoft Docs
description: Viene descritto come accedere e usare il dashboard di Kubernetes per monitorare il dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: 7274cef73bff3fb87d55ad636ff0167c8a064796
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180678"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-gpu-device"></a>Usare il dashboard di Kubernetes per monitorare il dispositivo GPU Azure Stack Edge

Questo articolo descrive come accedere e usare il dashboard di Kubernetes per monitorare il dispositivo GPU Azure Stack Edge. Per monitorare il dispositivo, è possibile usare i grafici in portale di Azure, visualizzare il dashboard di Kubernetes o eseguire `kubectl` comandi tramite l'interfaccia di PowerShell del dispositivo. 

Questo articolo è incentrato solo sulle attività di monitoraggio che possono essere eseguite nel dashboard di Kubernetes.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Accedere al dashboard di Kubernetes nel dispositivo
> * Scarica `aseuser` configurazione
> * Visualizzare i moduli distribuiti nel dispositivo
> * Ottenere l'indirizzo IP per le applicazioni distribuite nel dispositivo
> * Visualizzare i log dei contenitori per i moduli distribuiti nel dispositivo


## <a name="about-kubernetes-dashboard"></a>Informazioni sul dashboard di Kubernetes

Il dashboard di Kubernetes è un'interfaccia utente basata sul Web che è possibile usare per risolvere i problemi delle applicazioni incluse in contenitori. Il dashboard di Kubernetes è un'alternativa basata sull'interfaccia utente alla riga di comando di Kubernetes `kubectl` . Per altre informazioni, vedere [Dashboard Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

Sul dispositivo Azure Stack Edge, è possibile usare il dashboard di Kubernetes in modalità di sola *lettura* per ottenere una panoramica delle applicazioni in esecuzione sul dispositivo Azure stack Edge, visualizzare lo stato delle risorse del cluster Kubernetes e visualizzare gli eventuali errori che si sono verificati nel dispositivo.

## <a name="access-dashboard"></a>Accedi al dashboard

Il dashboard Kubernetes è di sola *lettura* e viene eseguito nel nodo master Kubernetes sulla porta 31000. Per accedere al dashboard, seguire questa procedura: 

1. Nell'interfaccia utente locale del dispositivo passare a **dispositivo** , quindi passare a **endpoint dispositivo**. Selezionare l'URL del dashboard di Kubernetes per aprire il dashboard in un browser.

    ![URL dashboard Kubernetes nella pagina dispositivo nell'interfaccia utente locale](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. Nella pagina **di accesso del dashboard di Kubernetes** selezionare **token**. 
1. Fornire un token. 
    1. Per ottenere il token, [connettersi tramite l'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).
    1. Eseguire il comando:  `Get-HcsKubernetesDashboardToken`
    
    1. Copiare la stringa di token visualizzata al prompt. Di seguito è riportato un output di esempio:
        
        ```powershell
        [10.100.10.10]: PS>Get-HcsKubernetesDashboardToken
        eyJhbGciOiJSUzI1NiIsImtpZCI6IkpFTEtBYTMyZ0Ezb01OYTVFSnVaUV85OWtLdXNETTZQR0k0UlFybGdReFUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi03czZ6ayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3NzY3ZDAzLTJlYWUtNDlkMi1hNDEyLTNkOTU3MDFiMThiMyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UgNrpVYVJBEaWxFlljuENUQQmzFXMYG2VsJUIYFdp2AO20zX0k5dRvwcCpeGlqSKb9MyYjG0c6RmT9uCOZk-vAwt7btszQLD7KPCwh_nn_NiIyO8ApgGRYZP8NuP8CBTX3tl_hpwfHtZ0ksbuKAduIL-0uPF0rG5wgLk9cTEw6fKSc2UZW6bIzhNSp_uSiP6MexOS6OftF9JFZejkIGd33dSp-k-tgFlm2Zy96sdFJC0q-XsH7jygiVnfxA9XMs5wqW26LkCh0rfO2WI3C1XFK-4TpufRZLJHo5WPlu-Tnsxa8xmtk2jQ3us-sXcBRrvhPNPrNKkbqc9hbjmWfGD0Q
        [10.100.10.10]: PS>
        ```
        
1. Selezionare **Accedi**.

    ![Accedere al dashboard di Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png)

6. È ora possibile visualizzare il dashboard di Kubernetes per il dispositivo Azure Stack Edge in modalità di sola lettura.

    ![Pagina principale del dashboard Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Visualizza lo stato del modulo

I moduli di calcolo sono contenitori per i quali è implementata una logica di business. È possibile usare il dashboard per verificare se un modulo di calcolo è stato distribuito correttamente sul dispositivo Azure Stack Edge.

PER visualizzare lo stato del modulo, seguire questa procedura nel Dashboard:

1. Nel riquadro sinistro del Dashboard passare a **spazio dei nomi**. Filtrare in base allo spazio dei nomi in cui vengono visualizzati i moduli IoT Edge, in questo caso **iotedge**.
1. Nel riquadro sinistro passare a **carichi di lavoro > distribuzioni**.
1. Nel riquadro di destra vengono visualizzati tutti i moduli distribuiti nel dispositivo. In questo caso, un modulo GettingStartedWithGPU è stato distribuito in Azure Stack Edge. È possibile osservare che il modulo è stato distribuito.

    ![Visualizza distribuzione moduli](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Ottenere l'indirizzo IP per i servizi o i moduli

È possibile usare il dashboard per ottenere gli indirizzi IP dei servizi o dei moduli che si vuole esporre all'esterno del cluster Kubernetes. 

Si assegna l'intervallo di indirizzi IP per questi servizi esterni tramite l'interfaccia utente Web locale del dispositivo nella pagina delle **impostazioni di rete di calcolo** . Dopo aver distribuito IoT Edge moduli, è possibile che si desideri ottenere l'indirizzo IP assegnato a un modulo o un servizio specifico. 

Per ottenere l'indirizzo IP, seguire questa procedura nel Dashboard:

1. Nel riquadro sinistro del Dashboard passare a **spazio dei nomi**. Filtrare in base allo spazio dei nomi in cui viene distribuito un servizio esterno, in questo caso **iotedge**.
1. Nel riquadro sinistro passare a **individuazione e bilanciamento del carico > Servizi**.
1. Nel riquadro di destra verranno visualizzati tutti i servizi in esecuzione nello `iotedge` spazio dei nomi nel dispositivo Azure stack Edge.

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
    

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come risolvere i problemi relativi a Kubernetes <!--insert link-->.
