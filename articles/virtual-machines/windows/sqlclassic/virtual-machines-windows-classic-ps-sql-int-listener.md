---
title: "Configurare un listener ILB per gruppi di disponibilità AlwaysOn | Microsoft Docs"
description: "Questa esercitazione usa le risorse create con il modello di distribuzione classica e crea un listener del gruppo di disponibilità AlwaysOn in Azure usando un servizio di bilanciamento del carico interno (ILB, Internal Load Balancer)."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/01/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: 8e59988f24748a82d4e143295bab9bdaa65cf8e4
ms.lasthandoff: 01/11/2017


---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurare un listener ILB per gruppi di disponibilità AlwaysOn in Azure
> [!div class="op_single_selector"]
> * [Listener interno](virtual-machines-windows-classic-ps-sql-int-listener.md)
> * [Listener esterno](virtual-machines-windows-classic-ps-sql-ext-listener.md)
> 
> 

## <a name="overview"></a>Overview
Questo argomento illustra come configurare un listener per un gruppo di disponibilità AlwaysOn usando il **servizio di bilanciamento del carico interno (ILB, Internal Load Balancer)**.

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

Per configurare un listener ILB per un gruppo di disponibilità AlwaysOn nel modello di Azure Resource Manager, vedere [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Il gruppo di disponibilità può contenere repliche solo locali, solo di Azure oppure sia locali che di Azure per le configurazioni ibride. Le repliche di Azure possono trovarsi nella stessa area o in più aree grazie a più reti virtuali (VNet). I passaggi seguenti presuppongono che sia già stato [configurato un gruppo di disponibilità](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) ma che non sia stato configurato un listener.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Linee guida e limitazioni per listener interni
Tenere presente le linee guida seguenti per il listener del gruppo di disponibilità in Azure con ILB:

* Il listener del gruppo di disponibilità è supportato su Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* Per ogni servizio cloud è supportato un solo listener del gruppo di disponibilità interno, perché il listener è configurato solo per ILB, e c’è un solo ILB per ogni servizio cloud. Tuttavia, è possibile creare più listener esterni. Per altre informazioni, vedere [Configurare un listener esterno per i gruppi di disponibilità AlwaysOn in Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinare l'accessibilità del listener
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Questo articolo illustra la creazione di un listener che usa il **servizio di bilanciamento del carico interno**. Se è necessario un listener pubblico/esterno, vedere la versione di questo articolo che illustra la procedura per la configurazione di un [listener esterno](virtual-machines-windows-classic-ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Creare endpoint VM con bilanciamento del carico con Direct Server Return
Per ILB è necessario creare prima di tutto il servizio di bilanciamento del carico interno. Questa operazione viene eseguita nello script seguente.

È necessario creare un endpoint con carico bilanciato per ogni macchina virtuale che ospita una replica di Azure. Se si dispongono di repliche in più aree, ogni replica per tale area deve essere nello stesso servizio cloud nella stessa rete virtuale. Le repliche di creazione del gruppo di disponibilità che si estendono su più aree di Azure richiedono la configurazione di più reti virtuali. Per altre informazioni sulla configurazione della connettività tra reti virtuali, vedere [Configurare la connettività tra reti virtuali](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Nel portale di Azure, passare a ogni macchina virtuale che ospita una replica e visualizzare i dettagli.
2. Scegliere la scheda **Endpoint** per ognuna delle macchine virtuali.
3. Verificare che il **nome** e la **porta pubblica** specificati per l'endpoint del listener non siano già in uso. Nell'esempio seguente, il nome è "MyEndpoint" e la porta è "1433".
4. Nel client locale, scaricare e installare [l'ultimo modulo PowerShell](https://azure.microsoft.com/downloads/).
5. Avviare **Azure PowerShell**. Viene aperta una nuova sessione di PowerShell con i moduli amministrativi di Azure caricati.
6. Eseguire **Get-AzurePublishSettingsFile**. Questo cmdlet conduce a un browser per scaricare un file di impostazioni di pubblicazione in una directory locale. Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure.
7. Eseguire il comando **Import-AzurePublishSettingsFile** con il percorso del file di impostazioni di pubblicazione che si è scaricato:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Una volta importato il file di impostazioni di pubblicazione, è possibile gestire la sottoscrizione di Azure nella sessione di PowerShell.
    
1. È consigliabile assegnare un indirizzo IP statico a **ILB**. Esaminare prima di tutto la configurazione attuale della rete virtuale eseguendo il comando seguente:
   
        (Get-AzureVNetConfig).XMLConfiguration
2. Annotare il nome **Subnet** per la subnet contenente le VM che ospitano le repliche. Questo nome verrà usato nel parametro **$SubnetName** nello script.
3. Annotare quindi il valore **VirtualNetworkSite** e il valore iniziale **AddressPrefix** per la subnet contenente le VM che ospitano le repliche. Cercare un indirizzo IP disponibile passando entrambi i valori al comando **Test-AzureStaticVNetIP** ed esaminando il valore **AvailableAddresses**. Se, ad esempio, il nome della rete virtuale è *MyVNet* e l'inizio dell'intervallo di indirizzi della subnet corrisponde a *172.16.0.128*, il comando seguente elencherà gli indirizzi disponibili:
   
        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
4. Scegliere uno degli indirizzi disponibili e usarlo nel parametro **$ILBStaticIP** dello script seguente.
5. Copiare lo script di PowerShell seguente in un editor di testi e impostare i valori variabili in base all'ambiente. Si noti che sono stati forniti valori predefiniti per alcuni parametri. Si noti che le distribuzioni esistenti che usano i gruppi di affinità non potranno aggiungere il Bilanciamento del carico interno. Per altre informazioni sui requisiti per il servizio di bilanciamento del carico interno, vedere [Bilanciamento del carico interno](../../../load-balancer/load-balancer-internal-overview.md). Se il gruppo di disponibilità si estende tra diverse aree di Azure, è necessario eseguire lo script una volta in ogni data center per il servizio cloud e i nodi che risiedono nel data center.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value
   
        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP
   
        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }
6. Dopo aver impostato le variabili, copiare lo script dall'editor di testo nella sessione di Azure PowerShell per eseguirlo. Se nel prompt viene ancora visualizzato >>, digitare di nuovo ENTER per assicurarsi che l'esecuzione dello script sia stata avviata. Nota

> [!NOTE]
> Il portale di Azure classico non supporta attualmente il servizio di bilanciamento del carico interno, quindi il servizio di bilanciamento del carico interno o gli endpoint non saranno visualizzati nel portale di Azure classico. **Get-AzureEndpoint** restituisce tuttavia un indirizzo IP interno, se nel portale è in esecuzione il servizio di bilanciamento del carico. In caso contrario, restituirà Null.
> 
> 

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Se necessario, verificare che KB2854082 sia installato.
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Aprire le porte firewall nei nodi del gruppo di disponibilità in
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Creare il listener del gruppo di disponibilità

Creare il listener del gruppo di disponibilità in due passaggi. Creare prima di tutto la risorsa cluster del punto di accesso client e configurare le dipendenze. In secondo luogo configurare le risorse del cluster con PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Creare il punto di accesso client e configurare le dipendenze del cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurare le risorse del cluster in PowerShell
1. Per il servizio di bilanciamento del carico interno è necessario usare l'indirizzo IP del servizio di bilanciamento del carico interno creato in precedenza. Usare lo script seguente per ottenere questo indirizzo IP in PowerShell.
   
        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress
2. Su una delle VM copiare lo script di PowerShell del sistema operativo in un editor di testo e impostare le variabili sui valori annotati prima.
   
    Per Windows Server 2012 o versione successiva, usare lo script seguente:
   
        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
   
        Import-Module FailoverClusters
   
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   
    Per Windows Server 2008 R2, usare lo script seguente:
   
        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
   
        Import-Module FailoverClusters
   
        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
3. Dopo aver impostato le variabili, aprire una finestra con privilegi elevati di Windows PowerShell, quindi copiare lo script dall'editor di testo e incollarlo nella sessione di Azure PowerShell per eseguirlo. Se nel prompt viene ancora visualizzato >>, digitare di nuovo ENTER per assicurarsi che l'esecuzione dello script sia stata avviata.
4. Ripetere questo passaggio su ogni macchina virtuale. Questo script consente di configurare la risorsa Indirizzo IP con l'indirizzo IP del servizio cloud e di impostare altri parametri come la porta probe. Quando la risorsa Indirizzo IP viene portata online, può quindi rispondere al polling sulla porta probe dall'endpoint con carico bilanciato, creato in precedenza in questa esercitazione.

## <a name="bring-the-listener-online"></a>Portare online il listener
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Operazioni di completamento della procedura
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testare il listener del gruppo di disponibilità (entro la stessa rete virtuale)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]


