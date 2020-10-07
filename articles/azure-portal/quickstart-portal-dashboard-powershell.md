---
title: Creare un dashboard nel portale di Azure con PowerShell
description: Informazioni su come creare un dashboard nel portale di Azure con Azure PowerShell.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.author: mblythe
ms.date: 07/24/2020
ms.openlocfilehash: 6b7a4f6d4ad7f5e94d19b9d531992f54ff13fec0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87440724"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Avvio rapido: Creare un dashboard nel portale di Azure con PowerShell

Un dashboard nel portale di Azure offre una visualizzazione mirata e organizzata delle risorse cloud. Questo articolo riguarda la procedura di creazione di un dashboard con il modulo Az.Portal di PowerShell.
Il dashboard mostra le prestazioni di una macchina virtuale (VM), oltre ad alcune informazioni statiche e alcuni collegamenti.

## <a name="requirements"></a>Requisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Durante la fase di anteprima del modulo **Az.Portal** di PowerShell, è necessario installarlo separatamente dal modulo Az di PowerShell usando il cmdlet `Install-Module`. Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile in modalità nativa all'interno di Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Scegliere una sottoscrizione di Azure specifica

Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare una sottoscrizione specifica usando il cmdlet [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definire le variabili

Verranno usate più volte diverse informazioni. Creare variabili in cui archiviarle.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) con il cmdlet [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

L'esempio seguente crea un gruppo di risorse basato sul nome incluso nella variabile `$resourceGroupName` nell'area specificata nella variabile `$location`.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Il dashboard che si creerà nella parte successiva di questo avvio rapido necessita di una macchina virtuale esistente. Creare una VM seguendo questa procedura.

Archiviare le credenziali di accesso per la VM in una variabile. La password deve essere complessa. Devono essere un nome utente e una password nuovi e non, ad esempio, quelli dell'account usato per accedere ad Azure. Per altre informazioni, vedere i [requisiti relativi ai nomi utente](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) e i [requisiti relativi alle password](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

```azurepowershell-interactive
$Cred = Get-Credential
```

Creare la macchina virtuale

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

La distribuzione della VM viene avviata e in genere impiega alcuni minuti. Al termine della distribuzione, passare alla sezione successiva.

## <a name="download-the-dashboard-template"></a>Scaricare il modello di dashboard

Poiché i dashboard di Azure sono risorse, possono essere rappresentati come JSON. Il codice seguente scarica una rappresentazione JSON di un dashboard di esempio. Per altre informazioni, vedere [Struttura dei dashboard di Azure](/azure/azure-portal/azure-portal-dashboards-structure).

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>Personalizzare il modello

Personalizzare il modello scaricato eseguendo il codice seguente.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

Per altre informazioni, vedere [Informazioni di riferimento sui modelli di dashboard nel portale Microsoft](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Distribuire il modello di dashboard

È possibile usare il cmdlet `New-AzPortalDashboard` che fa parte del modulo Az.Portal per distribuire il modello direttamente da PowerShell.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>Esaminare le risorse distribuite

Verificare che il dashboard sia stato creato correttamente.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

Verificare che sia possibile visualizzare i dati relativi alla macchina virtuale dall'interno del portale di Azure.

1. Nel portale di Azure selezionare **Dashboard**.

   ![passaggio al dashboard nel portale di Azure](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. Nella pagina del dashboard selezionare **Simple VM Dashboard**.

   ![Passare a Simple VM Dashboard](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Esaminare il dashboard. Come si può notare, parte del contenuto è statico, ma sono presenti anche alcuni grafici che mostrano le prestazioni della VM.

   ![Esaminare il dashboard Simple VM Dashboard](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere la VM e il dashboard associato, eliminare il gruppo di risorse che li contiene.

> [!CAUTION]
> L'esempio seguente elimina il gruppo di risorse specificato e tutte le risorse al suo interno.
> Se nel gruppo di risorse specificato sono presenti anche risorse diverse da quelle usate in questo articolo, verranno eliminate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui cmdlet contenuti nel modulo Az.Portal di PowerShell, vedere:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: cmdlet del dashboard del portale](https://docs.microsoft.com/powershell/module/Az.Portal/)
