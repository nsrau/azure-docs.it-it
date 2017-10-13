---
title: Indirizzi IP pubblici a livello di istanza di Azure (classico) | Microsoft Docs
description: Informazioni sugli IP pubblici a livello di istanza (ILPIP) e su come gestirli tramite PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
ms.openlocfilehash: 773043f2841ec7539b0d49357dec6bcb9f4f78a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="instance-level-public-ip-classic-overview"></a>Panoramica sugli indirizzi IP pubblici (classici) a livello di istanza
Un indirizzo IP pubblico a livello di istanza (ILPIP) è un indirizzo IP pubblico che è possibile assegnare direttamente all'istanza del ruolo della macchina virtuale o dei servizi cloud in cui risiede l'istanza del ruolo o la macchina virtuale. Un ILPIP non sostituisce l'indirizzo IP virtuale (VIP) assegnato al servizio cloud. Piuttosto, si tratta di un indirizzo IP aggiuntivo che è possibile usare per connettersi direttamente all'istanza della macchina virtuale o del ruolo.

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Questo articolo illustra l'uso del modello di distribuzione classica. Si consiglia di creare macchine virtuali tramite Resource Manager. Verificare di conoscere il funzionamento degli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) in Azure.

![Differenza tra ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Come illustrato nella figura 1, al servizio cloud si accede tramite un indirizzo VIP, mentre alle singole macchine virtuali si accede in genere tramite VIP:&lt;numero di porta&gt;. Assegnando un ILPIP a una macchina virtuale specifica, è possibile accedere a questa macchina virtuale direttamente tramite l’indirizzo IP.

Quando si crea un servizio cloud in Azure, i record A DNS corrispondenti vengono creati automaticamente per consentire l'accesso al servizio tramite un nome di dominio completo (FQDN) anziché tramite l'indirizzo VIP effettivo. Lo stesso processo si verifica per un ILPIP, che consente l'accesso all'istanza della macchina virtuale o del ruolo mediante FQDN anziché ILPIP. Se ad esempio si crea un servizio cloud denominato *contosoadservice* e si configura un ruolo Web denominato *contosoweb* con due istanze, Azure registra per le istanze i record A seguenti:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> È possibile assegnare un solo ILPIP per ogni istanza di macchina virtuale o ruolo. È possibile usare fino a 5 ILPIP per ogni sottoscrizione. Gli ILPIP non sono supportati per le macchine virtuali a più NIC.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Perché è necessario richiedere un ILPIP?
Se si desidera connettersi all'istanza della VM o del ruolo tramite un indirizzo IP assegnato direttamente all'istanza, anziché usare il servizio cloud VIP:&lt;numero porta&gt;, richiedere un ILPIP per l'istanza della VM o del ruolo.

* **FTP attivo**: assegnando un ILPIP a una macchina virtuale è possibile che questa riceva il traffico su qualsiasi porta. Gli endpoint non sono necessari per la macchina virtuale affinché questa riceva il traffico.  Per informazioni dettagliate sul protocollo FTP, vedere la (https://it.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview)[panoramica del protocollo FTP].
* **IP in uscita**: viene eseguito il mapping del traffico in uscita proveniente dalla macchina virtuale all'ILPIP come origine e l'ILPIP identifica in modo univoco la macchina virtuale sulle entità esterne.

> [!NOTE]
> In passato, un indirizzo ILPIP veniva definito indirizzo IP pubblico (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Gestire un ILPIP per una macchina virtuale
Le attività seguenti consentono di creare, assegnare e rimuovere gli ILPIP dalle macchine virtuali:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Come richiedere un ILPIP durante la creazione della macchina virtuale mediante PowerShell
Lo script di PowerShell riportato di seguito crea un nuovo servizio cloud denominato *FTPService*, recupera un'immagine da Azure, crea una macchina virtuale denominata *FTPInstance* usando l'immagine recuperata, imposta la macchina virtuale per usare un ILPIP e aggiunge la macchina virtuale al nuovo servizio:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Come recuperare informazioni su ILPIP per una macchina virtuale
Per visualizzare le informazioni su ILPIP per la macchina virtuale creata con lo script precedente, eseguire il comando di PowerShell seguente e osservare i valori per *PublicIPAddress* e *PublicIPName*:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Output previsto:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Come rimuovere un ILPIP da una macchina virtuale
Per rimuovere un ILPIP aggiunto alla macchina virtuale nello script precedente, eseguire il comando PowerShell seguente:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Come aggiungere un ILPIP a una macchina virtuale esistente
Per aggiungere un ILPIP alla macchina virtuale creata usando lo script precedente, eseguire il comando seguente:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Gestire un ILPIP per un'istanza del ruolo del servizi cloud

Per aggiungere un ILPIP a un'istanza del ruolo dei servizi cloud, completare i passaggi seguenti:

1. Scaricare il file con estensione CSCFG per il servizio cloud completando la procedura nell'articolo [Come configurare i servizi cloud](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).
2. Aggiornare il file con estensione CSCFG aggiungendo l'elemento `InstanceAddress`. Nell'esempio seguente viene aggiunto un ILPIP denominato *MyPublicIP* a un'istanza di ruolo denominata *WebRole1*: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Caricare il file con estensione CSCFG per il servizio cloud completando la procedura nell'articolo [Come configurare i servizi cloud](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sul funzionamento degli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) nel modello di distribuzione classica.
* Informazioni sugli [indirizzi IP riservati](virtual-networks-reserved-public-ip.md).
