<properties
  pageTitle="Connessione dei ruoli dei Servizi cloud di Azure a un controller di dominio personalizzato di AD ospitato in Azure"
  description="Informazioni su come connettere i ruoli Web/di lavoro a un dominio personalizzato di AD usando Powershell e l'estensione di dominio di AD"
  services="cloud-services"
  documentationCenter=""
  authors="VMak"
  manager="MadhanA"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="3/5/2015"
    ms.author="vmaker"/>

# Connessione dei ruoli dei Servizi cloud di Azure a un controller di dominio personalizzato di AD ospitato in Azure

## Guida dettagliata per la connessione dei ruoli Web/di lavoro di Azure a un controller di dominio personalizzato ospitato in Azure

È prima di tutto necessario configurare una rete virtuale (VNET) in Azure e quindi aggiungere alla VNET un controller di dominio di Active Directory ospitato in una macchina virtuale di Azure. I ruoli del servizio cloud esistenti verranno successivamente aggiunti alla VNET appena creata e verranno connessi al controller di dominio.

Prima di iniziare è necessario notare quanto segue:
1.	Questa esercitazione usa Powershell ed è quindi necessario assicurarsi che Azure Powershell sia installato e pronto per l'uso. Per ottenere informazioni sulla configurazione di Azure Powershell, vedere [Come installare e configurare Azure PowerShell](install-configure-powershell.md).
2.	Il controller di dominio di AD e le istanze dei ruoli Web/di lavoro devono essere presenti nella VNET.

Seguire questa guida dettagliata e in caso di problemi inserire commenti alla fine dell'articolo. I commenti verranno letti e verrà fornita assistenza.

## Creare una rete virtuale

È possibile creare una rete virtuale in Azure usando il portale di Azure o Powershell. Per questa esercitazione verrà usato Powershell. Per creare una rete virtuale usando il portale di Azure, vedere [Creare una rete virtuale](create-virtual-network.md).

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
    Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath;

## Creare una macchina virtuale

Dopo il completamento della configurazione della rete virtuale, sarà necessario creare un controller di dominio di AD. Per questa esercitazione verrà configurato un controller di dominio di AD in una macchina virtuale di Azure.

A questo scopo, creare una macchina virtuale tramite Powershell usando i comandi seguenti.

    #Initialize variables

    $vnetname = '<your-vnet-name>'
    $subnetname = '<your-subnet-name>'
    $vmsvc1 = '<your-hosted-service>'
    $vm1 = '<your-vm-name>'
    $username = '<your-username>'
    $password = '<your-password>'
    $ affgrp = '<your- affgrp>'

    #Create a VM and add it to the Virtual Network

    New-AzureQuickVM -Windows -ServiceName $vmsvc1 -name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname


## Alzare la macchina virtuale al livello di controller di dominio
Per configurare la macchina virtuale come controller di dominio di AD, sarà necessario accedere alla macchina virtuale e configurarla.

Per accedere alla macchina virtuale, è possibile ottenere il file RDP tramite Powershell e usare i comandi seguenti.

    #Get RDP file

    Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>

Dopo avere effettuato l'accesso alla macchina virtuale, configurarla come controller di dominio di AD seguendo le istruzioni della guida dettagliata disponibili nella pagina relativa a [come configurare i controller di dominio personalizzati di AD](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## Aggiungere la distribuzione del servizio cloud alla rete virtuale

Sarà quindi necessario aggiungere la distribuzione del servizio cloud alla VNET appena creata. A questo scopo, modificare il file cscfg del servizio cloud aggiungendo le sezioni rilevanti mediante Visual Studio o l'editor preferito.

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

        <!--VNET settings-->
        <VirtualNetworkSite name="[virtual-network-name]" />
        <AddressAssignments>
          <InstanceAddress roleName="[role-name]">
            <Subnets>
              <Subnet name="[subnet-name]" />
            </Subnets>
          </InstanceAddress>
        </AddressAssignments>
        <!--VNET settings-->

      </NetworkConfiguration>
    </ServiceConfiguration>

Compilare quindi il progetto dei servizi cloud e distribuirlo in Azure. Per ottenere informazioni sulla distribuzione del pacchetto di servizi cloud in Azure, vedere [Come creare e distribuire un servizio Cloud](cloud-services-how-to-create-deploy.md#deploy)

## Connettere i ruoli Web/di lavoro al dominio personalizzato usando l'estensione di dominio di AD

Dopo la distribuzione del progetto di servizi cloud in Azure, connettere le istanze del ruolo al dominio personalizzato di AD usando l'estensione del dominio di AD. Per aggiungere l'estensione di dominio di AD alla distribuzione esistente dei servizi cloud e aggiungere il dominio personalizzato, eseguire i comandi seguenti in Powershell:

    #Initialize domain variables

    $domain = '<your-domain-name>';
    $dmuser = '$domain\<your-username>';
    $dmpswd = '<your-domain-password>';
    $dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force;
    $dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd);

    #Add AD Domain Extension to the cloud service roles

    Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35;

E questo è tutto.

I servizi cloud dovrebbero essere stati aggiunti al controller di dominio personalizzato. Per altre informazioni sulle diverse opzioni disponibili per la configurazione dell'estensione di dominio di AD, usare la Guida di Powershell, come illustrato di seguito.

    help Set-AzureServiceADDomainExtension;
    help New-AzureServiceADDomainExtensionConfig;

È anche possibile inserire commenti sulla possibile utilità di un'estensione che permette di alzare una macchina virtuale al livello di un controller di dominio. Fornire eventuali informazioni nella sezione relativa ai commenti.

Ci auguriamo che questa guida sia risultata utile.

<!--HONumber=52-->