---
title: Connettere un servizio cloud a un controller di dominio personalizzato | Microsoft Docs
description: Informazioni su come connettere i ruoli Web/di lavoro a un dominio personalizzato di AD usando PowerShell e l'estensione di dominio di AD
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 4a50ae5e19ff9bf79b7f5361e5a274a2aba350f5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Connessione dei ruoli dei Servizi cloud di Azure a un controller di dominio personalizzato di AD ospitato in Azure
È prima di tutto necessario configurare una rete virtuale (VNet) in Azure e quindi aggiungere a tale VNet un controller di dominio di Active Directory ospitato in una macchina virtuale di Azure. I ruoli del servizio cloud esistente verranno quindi aggiunti alla rete virtuale creata in precedenza e connessi al controller di dominio.

Prima di iniziare è necessario notare quanto segue:

1. Questa esercitazione usa PowerShell ed è quindi necessario assicurarsi che Azure PowerShell sia installato e pronto per l'uso. Per ottenere informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).
2. Il controller di dominio di AD e le istanze dei ruoli Web/di lavoro devono essere presenti nella VNet.

Seguire questa guida dettagliata e in caso di problemi inserire commenti alla fine dell'articolo. I commenti verranno letti e verrà fornita assistenza.

La rete a cui viene fatto riferimento dal servizio cloud deve essere una **rete virtuale classica**.

## <a name="create-a-virtual-network"></a>Creare una rete virtuale
È possibile creare una rete virtuale in Azure usando il portale di Azure o PowerShell. Per questa esercitazione viene usato PowerShell. Per creare una rete virtuale tramite il portale di Azure, vedere [Creare una rete virtuale](../virtual-network/quick-create-portal.md). L'articolo illustra la creazione di una rete virtuale di Resource Manager, ma è necessario creare una rete virtuale classica per i servizi cloud. A tale scopo, nel portale selezionare **Crea una risorsa**, digitare *rete virtuale* nella casella **Cerca** e quindi premere **INVIO**. Nei risultati della ricerca, in **Tutto** selezionare **Rete virtuale**. In **Selezionare un modello di distribuzione** scegliere **Versione classica** e quindi **Crea**. È quindi possibile seguire la procedura in questo articolo.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Dopo il completamento della configurazione della rete virtuale, sarà necessario creare un controller di dominio di AD. Per questa esercitazione verrà configurato un controller di dominio di AD in una macchina virtuale di Azure.

A questo scopo, creare una macchina virtuale tramite PowerShell usando i comandi seguenti:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Alzare la macchina virtuale al livello di controller di dominio
Per configurare la macchina virtuale come controller di dominio di AD, sarà necessario accedere alla macchina virtuale e configurarla.

Per accedere alla macchina virtuale, è possibile ottenere il file RDP tramite PowerShell e usare i comandi seguenti:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Dopo avere effettuato l'accesso alla macchina virtuale, configurarla come controller di dominio di AD seguendo la guida dettagliata che illustra [come configurare il controller di dominio personalizzato di AD](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Aggiungere il servizio cloud alla rete virtuale
Sarà quindi necessario aggiungere la distribuzione del servizio cloud alla nuova rete virtuale. A questo scopo, modificare il file cscfg del servizio cloud aggiungendo le sezioni rilevanti mediante Visual Studio o l'editor preferito.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Compilare quindi il progetto dei servizi cloud e distribuirlo in Azure. Per ottenere informazioni sulla distribuzione del pacchetto di servizi cloud in Azure, vedere [Come creare e distribuire un servizio cloud](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Connettere i ruoli Web/di lavoro al dominio
Dopo la distribuzione del progetto di servizi cloud in Azure, connettere le istanze del ruolo al dominio personalizzato di AD usando l'estensione del dominio di AD. Per aggiungere l'estensione di dominio di AD alla distribuzione esistente dei servizi cloud e aggiungere il dominio personalizzato, eseguire i comandi seguenti in PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

L'operazione è terminata.

I servizi cloud sono stati aggiunti al controller di dominio personalizzato. Per altre informazioni sulle diverse opzioni disponibili per la configurazione dell'estensione di dominio di AD, usare la Guida di PowerShell. Di seguito sono riportati alcuni esempi:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
