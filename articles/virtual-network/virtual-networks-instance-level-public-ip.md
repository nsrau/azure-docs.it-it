---
title: IP pubblico a livello di istanza (ILPIP) | Microsoft Docs
description: Informazioni su ILPIP (PIP) e come gestirli
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial

---
# Panoramica sugli indirizzi IP pubblici a livello di istanza
Un indirizzo IP pubblico a livello di istanza (ILPIP) è un indirizzo IP pubblico che è possibile assegnare direttamente all'istanza della VM o del ruolo anziché al servizio cloud in cui risiede l'istanza. Tale indirizzo non sostituisce l'indirizzo VIP (Virtual IP) assegnato al servizio cloud. Piuttosto, si tratta di un indirizzo IP aggiuntivo che è possibile usare per connettersi direttamente all'istanza della macchina virtuale o del ruolo.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Informazioni su come [eseguire questa procedura con il modello di Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Verificare di conoscere il funzionamento degli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) in Azure.

> [!NOTE]
> In passato, un ILPIP veniva definito PIP, acronimo di Public IP.
> 
> 

![Differenza tra ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Come illustrato nella figura 1, al servizio cloud si accede tramite un indirizzo VIP, mentre alle singole macchine virtuali in genere si accede tramite VIP:&lt;numero di porta &gt;. Assegnando un ILPIP a una macchina virtuale specifica, è possibile accedere a questa macchina virtuale direttamente tramite l’indirizzo IP.

Quando si crea un servizio cloud in Azure, i record A DNS corrispondenti vengono creati automaticamente per consentire l'accesso al servizio tramite un nome di dominio completo (FQDN) anziché tramite l'indirizzo VIP effettivo. Lo stesso processo si verifica per ILPIP, che consente l'accesso all'istanza della macchina virtuale o del ruolo mediante FQDN anziché ILPIP. Se ad esempio si crea un servizio cloud denominato *contosoadservice* e si configura un ruolo Web denominato *contosoweb* con due istanze, Azure registrerà i record A seguenti per le istanze:

* contosoweb\_IN\_0.contosoadservice.cloudapp.net
* contosoweb\_IN\_1.contosoadservice.cloudapp.net

> [!NOTE]
> È possibile assegnare un solo ILPIP per ogni istanza di macchina virtuale o ruolo. È possibile usare fino a 5 ILPIP per ogni sottoscrizione. Attualmente, ILPIP non è supportato per le macchine virtuali a più NIC.
> 
> 

## Perché è necessario richiedere un ILPIP?
Se si desidera connettersi all'istanza della VM o del ruolo tramite un indirizzo IP assegnato direttamente all'istanza, anziché usare il servizio cloud VIP:&lt;numero porta&gt;, richiedere un ILPIP per l'istanza della VM o del ruolo.

* **FTP passivo**: con un ILPIP sulla VM, è possibile ricevere traffico su qualsiasi porta, senza dover aprire un apposito endpoint. Questa operazione consente scenari quali FTP passivo dove le porte vengono scelte in modo dinamico.
* **IP in uscita**: il traffico in uscita proveniente dalla VM viene trasmesso con l'ILPIP come origine e identifica in modo univoco la VM sulle entità esterne.

## Come richiedere un ILPIP durante la creazione della macchina virtuale
Lo script di PowerShell di seguito crea un nuovo servizio cloud denominato *FTPService*, quindi recupera un'immagine da Azure e crea una macchina virtuale denominata *FTPInstance* utilizzando l'immagine recuperata, imposta la macchina virtuale per usare un ILPIP e aggiunge la macchina virtuale al nuovo servizio:

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## Come recuperare informazioni su ILPIP per una macchina virtuale
Per visualizzare le informazioni su ILPIP per la macchina virtuale creata con lo script precedente, eseguire il comando PowerShell e osservare i valori per *PublicIPAddress* e *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

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
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## Come rimuovere un ILPIP da una macchina virtuale
Per rimuovere un ILPIP aggiunto alla macchina virtuale nello script precedente, eseguire il comando PowerShell seguente:

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
    | Remove-AzurePublicIP `
    | Update-AzureVM

## Come aggiungere un ILPIP a una macchina virtuale esistente
Per aggiungere un ILPIP alla VM creata usando lo script precedente, eseguire il comando seguente:

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
    | Set-AzurePublicIP -PublicIPName ftpip2 `
    | Update-AzureVM

## Come associare un ILPIP a una macchina virtuale usando un file di configurazione del servizio
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

## Passaggi successivi
* Informazioni sul funzionamento degli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) nel modello di distribuzione classica.
* Informazioni sugli [indirizzi IP riservati](virtual-networks-reserved-public-ip.md).

<!---HONumber=AcomDC_0810_2016-->