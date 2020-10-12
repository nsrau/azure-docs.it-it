---
title: Come arrestare il monitoraggio del cluster Kubernetes ibrido | Microsoft Docs
description: Questo articolo descrive come è possibile arrestare il monitoraggio del cluster Kubernetes ibrido con monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 2754649cd990b015162be158effa2b85aa1fe27e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986056"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Come arrestare il monitoraggio del cluster ibrido

Dopo aver abilitato il monitoraggio del cluster Kubernetes, è possibile arrestare il monitoraggio del cluster con monitoraggio di Azure per i contenitori se si decide che non si vuole più monitorarlo. Questo articolo illustra come eseguire questa operazione per gli ambienti seguenti:

- Motore AKS in Azure e Azure Stack
- OpenShift versione 4 e successive
- Kubernetes con abilitazione di Azure Arc (anteprima)

## <a name="how-to-stop-monitoring-using-helm"></a>Come arrestare il monitoraggio con Helm

I passaggi seguenti si applicano agli ambienti seguenti:

- Motore AKS in Azure e Azure Stack
- OpenShift versione 4 e successive

1. Per identificare prima di tutto il monitoraggio di Azure per i contenitori Helm Chart versione installata nel cluster, eseguire il comando Helm seguente.

    ```
    helm list
    ```

    L'output sarà simile al seguente:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Azmon-Containers-Release-1* rappresenta la versione del grafico Helm per il monitoraggio di Azure per i contenitori.

2. Per eliminare la versione del grafico, eseguire il comando Helm seguente.

    `helm delete <releaseName>`

    Esempio:

    `helm delete azmon-containers-release-1`

    La versione verrà rimossa dal cluster. È possibile verificare eseguendo il `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Il completamento della modifica della configurazione può richiedere alcuni minuti. Poiché Helm tiene traccia dei rilasci anche dopo averli eliminati, è possibile controllare la cronologia di un cluster e persino annullare l'eliminazione di una versione con `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Come arrestare il monitoraggio su Arc abilitato Kubernetes

### <a name="using-powershell"></a>Utilizzo di PowerShell

1. Scaricare e salvare lo script in una cartella locale che configuri il cluster con il componente aggiuntivo monitoraggio usando i comandi seguenti:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Configurare la `$azureArcClusterResourceId` variabile impostando i valori corrispondenti per `subscriptionId` `resourceGroupName` e `clusterName` rappresentando l'ID risorsa della risorsa cluster Kubernetes abilitata per Azure Arc.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configurare la `$kubeContext` variabile con il **contesto Kube** del cluster eseguendo il comando `kubectl config get-contexts` . Se si desidera utilizzare il contesto corrente, impostare il valore su `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Eseguire il comando seguente per arrestare il monitoraggio del cluster.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>Uso dell'entità servizio
Lo script *disable-monitoring.ps1* usa l'accesso interattivo al dispositivo. Se si preferisce un accesso non interattivo, è possibile usare un'entità servizio esistente o crearne una nuova con le autorizzazioni necessarie, come descritto in [prerequisiti](container-insights-enable-arc-enabled-clusters.md#prerequisites). Per usare l'entità servizio, è necessario passare i parametri $servicePrincipalClientId, $servicePrincipalClientSecret e $tenantId con i valori dell'entità servizio che si intende usare per enable-monitoring.ps1 script.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Ad esempio:

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>Tramite Bash

1. Scaricare e salvare lo script in una cartella locale che configuri il cluster con il componente aggiuntivo monitoraggio usando i comandi seguenti:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Configurare la `azureArcClusterResourceId` variabile impostando i valori corrispondenti per `subscriptionId` `resourceGroupName` e `clusterName` rappresentando l'ID risorsa della risorsa cluster Kubernetes abilitata per Azure Arc.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configurare la `kubeContext` variabile con il **contesto Kube** del cluster eseguendo il comando `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Per arrestare il monitoraggio del cluster, sono disponibili diversi comandi basati sullo scenario di distribuzione.

    Eseguire il comando seguente per arrestare il monitoraggio del cluster usando il contesto corrente.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Eseguire il comando seguente per arrestare il monitoraggio del cluster specificando un contesto

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>Uso dell'entità servizio
Lo script bash *Disable-Monitoring.sh* usa l'accesso interattivo al dispositivo. Se si preferisce un accesso non interattivo, è possibile usare un'entità servizio esistente o crearne una nuova con le autorizzazioni necessarie, come descritto in [prerequisiti](container-insights-enable-arc-enabled-clusters.md#prerequisites). Per usare l'entità servizio, è necessario passare i valori--client-ID,--client-Secret e--tenant-ID dell'entità servizio che si vuole usare per *Enable-Monitoring.sh* script bash.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Ad esempio:

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>Passaggi successivi

Se l'area di lavoro Log Analytics è stata creata solo per supportare il monitoraggio del cluster e non è più necessaria, è necessario eliminarla manualmente. Se non si ha familiarità con la modalità di eliminazione di un'area di lavoro, vedere [eliminare un'area di lavoro di Azure log Analytics](../platform/delete-workspace.md).
