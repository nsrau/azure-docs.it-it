---
title: Indirizzo IP privato interno statico - Macchina virtuale di Azure - Versione classica
description: Informazioni sugli indirizzi IP interni statici (DIP) e su come gestirli
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: cf9ee59ca4e44ed01836c2efb1f4df5f073bf6e0
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Come impostare un indirizzo IP privato interno statico tramite PowerShell (classico)
Nella maggior parte dei casi non è necessario specificare un indirizzo IP interno statico per la macchina virtuale. Le macchine virtuali in una rete virtuale infatti ricevono automaticamente un indirizzo IP interno da un intervallo specificato. In alcuni casi è tuttavia opportuno specificare un indirizzo IP statico per una determinata macchina virtuale, ad esempio se questa eseguirà DNS o sarà un controller di dominio. Un indirizzo IP interno statico resta associato alla macchina virtuale anche in caso di passaggio allo stato di arresto/deprovisioning. 

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di utilizzare il [modello di distribuzione di Resource Manager per le distribuzioni più recenti](virtual-networks-static-private-ip-arm-ps.md).
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Come verificare la disponibilità di uno specifico indirizzo IP
Per verificare se l'indirizzo IP *10.0.0.7* è disponibile in una rete virtuale denominata *TestVnet*, eseguire il comando PowerShell seguente e controllare il valore per *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Per testare il comando precedente in un ambiente sicuro, seguire le istruzioni in [Creare una rete virtuale (versione classica)](virtual-networks-create-vnet-classic-pportal.md) per creare una rete virtuale denominata *TestVnet* e assicurarsi che usi lo spazio degli indirizzi *10.0.0.0/8*.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Come specificare un indirizzo IP interno statico durante la creazione di una macchina virtuale
Lo script di PowerShell seguente crea un nuovo servizio cloud denominato *TestService*, quindi recupera un'immagine da Azure, crea una macchina virtuale denominata *TestVM* nel nuovo servizio cloud tramite l'immagine recuperata, imposta la macchina virtuale in modo da trovarsi in una subnet denominata *Subnet-1* e imposta *10.0.0.7* come indirizzo IP interno statico per la macchina virtuale:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Come recuperare le informazioni relative all'indirizzo IP interno statico per una macchina virtuale
Per visualizzare le informazioni relative all'indirizzo IP interno statico per la macchina virtuale creata con lo script precedente, eseguire il comando PowerShell seguente e osservare i valori per *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Come rimuovere un indirizzo IP interno statico da una macchina virtuale
Per rimuovere l'indirizzo IP interno statico aggiunto alla macchina virtuale nello script precedente, eseguire il comando PowerShell seguente:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Come aggiungere un indirizzo IP interno statico a una macchina virtuale esistente
Per aggiungere un indirizzo IP interno statico alla macchina virtuale creata usando lo script precedente, eseguire il comando seguente:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Passaggi successivi
[IP riservato](virtual-networks-reserved-public-ip.md)

[IP pubblico a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md)

[API REST di IP riservati](https://msdn.microsoft.com/library/azure/dn722420.aspx)


