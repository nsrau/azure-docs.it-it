---
title: Raccogliere i dettagli su tutte le VM in una sottoscrizione con PowerShell
description: Raccogliere i dettagli su tutte le VM in una sottoscrizione con PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: 8d61da9c3a3d575fcbce0b77e1f8b2684df58106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87171686"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Raccogliere i dettagli su tutte le VM in una sottoscrizione con PowerShell

Questo script crea un CSV che contiene il nome della macchina virtuale, il nome del gruppo di risorse, l'area, le dimensioni della macchina virtuale, la rete virtuale, la subnet, l'indirizzo IP privato, il tipo di sistema operativo e l'indirizzo IP pubblico delle macchine virtuali nella sottoscrizione fornita.

Se non si ha una [sottoscrizione di Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="sample-script"></a>Script di esempio

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VmSize, VirtualNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress, NicName, ApplicationSecurityGroup 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VmSize = $vm.HardwareProfile.VmSize
        $info.VirtualNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $info.NicName = $nic.Name 
        $info.ApplicationSecurityGroup = $nic.IpConfigurations.ApplicationSecurityGroups.Id 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VmSize, VirtualNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress, NicName, ApplicationSecurityGroup 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i seguenti comandi per creare un'esportazione CSV dei dettagli delle macchine virtuali in una sottoscrizione. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

|Comando|Note|
|-|-|
|[Select-AzSubscription](/powershell/module/az.accounts/set-azcontext)|Imposta il tenant, la sottoscrizione e l'ambiente per i cmdlet da utilizzare nella sessione corrente.|
|[Get-AzVM](/powershell/module/az.compute/get-azvm)|Ottiene le proprietà di una macchina virtuale.|
|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)|Ottiene un indirizzo IP pubblico.|
|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface)|Ottiene un'interfaccia di rete.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script PowerShell della macchina virtuale sono reperibili nella [documentazione della VM Windows di Azure](../windows/powershell-samples.md?toc=/azure/virtual-machines/windows/toc.json).
