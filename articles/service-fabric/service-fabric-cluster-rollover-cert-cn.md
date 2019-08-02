---
title: Eseguire il rollover del certificato di un cluster di Azure Service Fabric | Microsoft Docs
description: Informazioni su come eseguire il rollover del certificato di un cluster di Service Fabric identificato dal nome comune del certificato.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: atsenthi
ms.openlocfilehash: 5d11054ca8eb684f1f25a25ddeac1b53e82b3775
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599911"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Eseguire manualmente il rollover del certificato di un cluster di Service Fabric
Quando il certificato di un cluster di Service Fabric è vicino alla scadenza, è necessario aggiornarlo.  Il rollover dei certificati è semplice se il cluster è stato [impostato per usare i certificati in base al nome comune](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (invece dell'identificazione personale).  Ottenere un nuovo certificato da un'autorità di certificazione con una nuova data di scadenza.  I certificati autofirmati non sono supportati per i cluster di Service Fabric di produzione, per includere certificati generati durante il flusso di lavoro di creazione del cluster del portale di Azure. Il nuovo certificato deve avere lo stesso nome comune del certificato precedente. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Un cluster di Service Fabric usa automaticamente il certificato dichiarato con la data di scadenza più distante nel futuro, se nell'host sono installati più certificati convalidati. Una procedura consigliata consiste nell'usare un modello di Resource Manager per eseguire il provisioning delle risorse di Azure. Per gli ambienti non di produzione è possibile usare lo script seguente per caricare un nuovo certificato in un insieme di credenziali delle chiavi e quindi installarlo nel set di scalabilità di macchine virtuali: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> I segreti del set di scalabilità di macchine virtuali non supportano lo stesso ID risorsa per due segreti separati, perché ogni segreto è una risorsa univoca con controllo delle versioni. 

Altre informazioni sono disponibili in:
* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* [Aggiornare e gestire i certificati dei cluster](service-fabric-cluster-security-update-certs-azure.md)

