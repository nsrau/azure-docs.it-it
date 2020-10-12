---
title: Abilitare Azure Arc in Kubernetes sul dispositivo GPU Pro Azure Stack Edge | Microsoft Docs
description: Viene descritto come abilitare Azure Arc in un cluster Kubernetes esistente sul dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/01/2020
ms.author: alkohli
ms.openlocfilehash: 423345739ca5c078fbff4f267e1e8a118abf107c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903190"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Abilitare Azure Arc in un cluster Kubernetes nel dispositivo GPU Pro Azure Stack Edge

Questo articolo illustra come abilitare Azure Arc in un cluster Kubernetes esistente nel dispositivo Azure Stack Edge Pro. 

Questa procedura è destinata agli utenti che hanno esaminato i [carichi di lavoro Kubernetes sul dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-kubernetes-workload-management.md) e hanno familiarità con i concetti di [Azure Arc Enabled Kubernetes (anteprima)](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).


## <a name="prerequisites"></a>Prerequisiti

Prima di abilitare Azure Arc in un cluster Kubernetes, verificare di aver completato i prerequisiti seguenti sul dispositivo Azure Stack Edge Pro e sul client che verrà usato per accedere al dispositivo:

### <a name="for-device"></a>Per il dispositivo

1. Si dispone di credenziali di accesso a un dispositivo Azure Stack Edge Pro a 1 nodo.
    1. Il dispositivo è attivato. Vedere [attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    1. Il dispositivo ha il ruolo di calcolo configurato tramite portale di Azure ed è dotato di un cluster Kubernetes. Vedere [configurare](azure-stack-edge-gpu-deploy-configure-compute.md)le risorse di calcolo.

1. Il proprietario ha accesso alla sottoscrizione. Questo accesso è necessario durante il passaggio di assegnazione di ruolo per l'entità servizio.
 

### <a name="for-client-accessing-the-device"></a>Per l'accesso client al dispositivo

1. Si dispone di un sistema client Windows che verrà usato per accedere al dispositivo Azure Stack Edge Pro.
  
    - Il client esegue Windows PowerShell 5,0 o versione successiva. Per scaricare la versione più recente di Windows PowerShell, vedere [installare Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - È possibile avere anche un altro client con un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Questo articolo descrive la procedura quando si usa un client Windows. 
    
1. È stata completata la procedura descritta in [accedere al cluster Kubernetes nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Precisamente:
    
    - Installato `kubectl` nel client  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Verificare che la versione del `kubectl` client non sia più di una versione della versione master di Kubernetes in esecuzione nel dispositivo Azure stack Edge Pro. 
      - Usare `kubectl version` per verificare la versione di kubectl in esecuzione sul client. Prendere nota della versione completa.
      - Nell'interfaccia utente locale del dispositivo Azure Stack Edge Pro passare a **aggiornamento software** e prendere nota del numero di versione del server Kubernetes. 
    
        ![Verificare il numero di versione del server Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Verificare che le due versioni siano compatibili. 

<!-- az cli version requirements-->

## <a name="register-kubernetes-resource-providers"></a>Registrare i provider di risorse Kubernetes

Prima di abilitare Azure Arc sul cluster Kubernetes, è necessario abilitare e registrare `Microsoft.Kubernetes` e per `Microsoft.KubernetesConfiguration` la sottoscrizione. 

1. Per abilitare un provider di risorse, nella portale di Azure passare alla sottoscrizione che si intende usare per la distribuzione. Passare a **provider di risorse**. 
1. Nel riquadro di destra cercare i provider che si desidera aggiungere. In questo esempio, `Microsoft.Kubernetes` e `Microsoft.KubernetesConfiguration` .

    ![Registrare i provider di risorse Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Selezionare un provider di risorse e, nella parte superiore della barra dei comandi, selezionare **registra**. La registrazione richiede alcuni minuti. 

    ![Registrare i provider di risorse Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Aggiornare l'interfaccia utente fino a quando non si noterà che il provider di risorse è registrato. Ripetere il processo per entrambi i provider di risorse.
    
    ![Registrare i provider di risorse Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

È anche possibile registrare i provider di risorse tramite `az cli` . Per altre informazioni, vedere [registrare i due provider per Azure Arc Enabled Kubernetes](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>Creare un'entità servizio, assegnare un ruolo

1. Assicurarsi di avere `Subscription ID` e il nome del gruppo di risorse usato per la distribuzione delle risorse per il servizio Azure stack Edge. Per ottenere l'ID sottoscrizione, passare alla risorsa Azure Stack Edge nel portale di Azure. Passare a **panoramica > Essentials**.

    ![Ottenere l'ID sottoscrizione](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Per ottenere il nome del gruppo di risorse, passare a **Proprietà**.

    ![Ottenere il nome del gruppo di risorse](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Per creare un'entità servizio, usare il comando seguente tramite `az cli` .

    `az ad sp create-for-rbac --skip assignment --name "<Informative name for service principal>"`  

    Per informazioni su come accedere a `az cli` , [avviare Cloud Shell in portale di Azure](../cloud-shell/quickstart-powershell.md?view=azure-cli-latest#start-cloud-shell)

    Di seguito è riportato un esempio. 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Prendere nota dell'oggetto `appID` , `name` , `password` e `tenantID` come verrà usato come input nel comando successivo.

1. Dopo aver creato la nuova entità servizio, assegnare il `Kubernetes Cluster - Azure Arc Onboarding` ruolo all'entità appena creata. Si tratta di un ruolo predefinito di Azure (usare l'ID del ruolo nel comando) con autorizzazioni limitate. Usare il comando seguente:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Di seguito è riportato un esempio.
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Per altre informazioni su come creare un'entità servizio ed eseguire l'assegnazione di ruolo, vedere la procedura descritta in [creare un'entità servizio di onboarding abilitata per Azure Arc](https://docs.microsoft.com/azure/azure-arc/kubernetes/create-onboarding-service-principal).


## <a name="enable-arc-on-kubernetes-cluster"></a>Abilitare Arc nel cluster Kubernetes

Per configurare il cluster Kubernetes per la gestione di Azure Arc, seguire questa procedura:

1. [Connettersi all'interfaccia di PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) del dispositivo.

1. Digitare:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`

    Per distribuire Azure Arc sul dispositivo Azure Stack Edge Pro, assicurarsi di usare un' [area supportata per Azure Arc](../azure-arc/kubernetes/overview.md#supported-regions). Azure Arc è attualmente in fase di anteprima. È anche possibile determinare il nome esatto dell'area da passare nel cmdlet usando il `az account list-locations` comando.
    
    Esempio:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    Nel portale di Azure, è necessario creare una risorsa con il nome specificato nel comando precedente.

    ![Vai alla risorsa Azure Arc](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Per verificare che Azure Arc sia stato abilitato correttamente, eseguire il comando seguente dall'interfaccia di PowerShell:

    `kubectl get deployments -n azure-arc`

    Questo comando consente di trovare tutte le applicazioni distribuite nello `azure-arc` spazio dei nomi corrispondente ad Azure Arc.

    Di seguito è riportato un esempio di output che Mostra gli agenti di Azure Arc distribuiti nel cluster Kubernetes nello `azure-arc` spazio dei nomi. 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    È anche possibile ottenere un elenco dei pod in esecuzione nel cluster Kubernetes nello `azure-arc` spazio dei nomi. Un pod è un contenitore dell'applicazione o un processo in esecuzione nel cluster Kubernetes. 

    Usare il comando seguente:
    
    `kubectl get pods -n azure-arc`
    
    Di seguito è riportato un esempio di output.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


Come illustrato nell'output precedente, Azure Arc Enabled Kubernetes è costituito da alcuni agenti (operatori) che vengono eseguiti nel cluster distribuito allo `azure-arc` spazio dei nomi.

- `config-agent`: verifica nel cluster connesso le risorse di configurazione controllo del codice sorgente applicate al cluster e aggiorna lo stato di conformità
- `controller-manager`: è un operatore di operatori e orchestra le interazioni tra i componenti di Azure Arc
- `metrics-agent`: raccoglie le metriche di altri agenti Arc per assicurarsi che registrino prestazioni ottimali
- `cluster-metadata-operator`: raccoglie i metadati del cluster, la versione del cluster, il numero di nodi e la versione dell'agente di Azure Arc
- `resource-sync-agent`: sincronizza con Azure i metadati del cluster sopra indicati
- `clusteridentityoperator`: Azure Arc Enabled Kubernetes supporta attualmente l'identità assegnata dal sistema. clusteridentityoperator gestisce il certificato dell'identità del servizio gestito usato da altri agenti per la comunicazione con Azure.
- `flux-logs-agent`: raccoglie i log degli operatori Flux distribuiti come parte della configurazione del controllo del codice sorgente.
- `connect-agent`: comunica con la risorsa di Azure Arc.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Rimuovere l'arco dal cluster Kubernetes

Per rimuovere la gestione di Azure Arc, attenersi alla procedura seguente:

1. 1. [Connettersi all'interfaccia di PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) del dispositivo.
2. Digitare:

    `Remove-HcsKubernetesAzureArcAgent` 


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come eseguire una distribuzione di Azure Arc, vedere [distribuire un'applicazione php senza stato Guestbook con Redis tramite GitOps in un dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)
