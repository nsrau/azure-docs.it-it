---
title: IP pubblico a livello di istanza (classico) con PowerShell | Documentazione Microsoft
description: Informazioni sugli ILPIP (PIP) e su come gestirli con PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: f1919d84cf912e184d87a5eeb462355e8ee3da07


---
# <a name="instance-level-public-ip-classic-overview"></a>Panoramica sugli indirizzi IP pubblici (classici) a livello di istanza
Un indirizzo IP pubblico a livello di istanza (ILPIP) è un indirizzo IP pubblico che è possibile assegnare direttamente all'istanza della VM o del ruolo anziché al servizio cloud in cui risiede l'istanza. Tale indirizzo non sostituisce l'indirizzo VIP (Virtual IP) assegnato al servizio cloud. Piuttosto, si tratta di un indirizzo IP aggiuntivo che è possibile usare per connettersi direttamente all'istanza della macchina virtuale o del ruolo.

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare Gestione risorse per la maggior parte delle distribuzioni più recenti. Verificare di conoscere il funzionamento degli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) in Azure.

![Differenza tra ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Come illustrato nella figura 1, al servizio cloud si accede tramite un indirizzo VIP, mentre alle singole macchine virtuali si accede in genere tramite VIP:&lt;numero di porta&gt;. Assegnando un ILPIP a una macchina virtuale specifica, è possibile accedere a questa macchina virtuale direttamente tramite l’indirizzo IP.

Quando si crea un servizio cloud in Azure, i record A DNS corrispondenti vengono creati automaticamente per consentire l'accesso al servizio tramite un nome di dominio completo (FQDN) anziché tramite l'indirizzo VIP effettivo. Lo stesso processo si verifica per ILPIP, che consente l'accesso all'istanza della macchina virtuale o del ruolo mediante FQDN anziché ILPIP. Se ad esempio si crea un servizio cloud denominato *contosoadservice* e si configura un ruolo Web denominato *contosoweb* con due istanze, Azure registrerà per le istanze i record A seguenti:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> È possibile assegnare un solo ILPIP per ogni istanza di macchina virtuale o ruolo. È possibile usare fino a 5 ILPIP per ogni sottoscrizione. Attualmente, ILPIP non è supportato per le macchine virtuali a più NIC.
> 
> 

## <a name="why-should-i-request-an-ilpip"></a>Perché è necessario richiedere un ILPIP?
Se si desidera connettersi all'istanza della VM o del ruolo tramite un indirizzo IP assegnato direttamente all'istanza, anziché usare il servizio cloud VIP:&lt;numero porta&gt;, richiedere un ILPIP per l'istanza della VM o del ruolo.

* **FTP passivo** : con un ILPIP sulla VM, è possibile ricevere traffico su qualsiasi porta, senza dover aprire un apposito endpoint. Questa operazione consente scenari quali FTP passivo dove le porte vengono scelte in modo dinamico.
* **IP in uscita** : il traffico in uscita proveniente dalla VM viene trasmesso con l'ILPIP come origine e identifica in modo univoco la VM sulle entità esterne.

> [!NOTE]
> In passato, un ILPIP veniva definito PIP, acronimo di Public IP.
> 

## <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Come richiedere un ILPIP durante la creazione della macchina virtuale mediante PowerShell
Lo script di PowerShell riportato di seguito crea un nuovo servizio cloud denominato *FTPService*. Recupera quindi un'immagine da Azure e crea una macchina virtuale denominata *FTPInstance* usando l'immagine recuperata, imposta la macchina virtuale per usare un ILPIP e aggiunge la macchina virtuale al nuovo servizio:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Come recuperare informazioni su ILPIP per una macchina virtuale
Per visualizzare le informazioni su ILPIP per la macchina virtuale creata con lo script precedente, eseguire il comando PowerShell e osservare i valori per *PublicIPAddress* e *PublicIPName*:

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

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Come rimuovere un ILPIP da una macchina virtuale
Per rimuovere un ILPIP aggiunto alla macchina virtuale nello script precedente, eseguire il comando PowerShell seguente:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Come aggiungere un ILPIP a una macchina virtuale esistente
Per aggiungere un ILPIP alla VM creata usando lo script precedente, eseguire il comando seguente:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>Come associare un ILPIP a una macchina virtuale usando un file di configurazione del servizio
È possibile anche associare un ILPIP a una macchina virtuale usando un file di configurazione (CSCFG) del servizio. Il file XML di esempio riportato di seguito illustra come configurare un servizio cloud per l'uso di un ILPIP denominato *MyPublicIP* per un'istanza del ruolo: 

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sul funzionamento degli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) nel modello di distribuzione classica.
* Informazioni sugli [indirizzi IP riservati](virtual-networks-reserved-public-ip.md).




<!--HONumber=Jan17_HO1-->


