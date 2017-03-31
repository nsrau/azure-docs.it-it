---
title: "Configurare un listener esterno per i gruppi di disponibilità AlwaysOn | Microsoft Docs"
description: "Questa esercitazione illustra in dettaglio la procedura per creare un listener del gruppo di disponibilità AlwaysOn in Azure accessibile dall&quot;esterno con l&quot;indirizzo IP virtuale pubblico del servizio cloud associato."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: d9138b17e54aa1c4bf1982b09d3d0ad10e936d4c
ms.lasthandoff: 03/25/2017


---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurare un listener esterno per i gruppi di disponibilità AlwaysOn in Azure
> [!div class="op_single_selector"]
> * [Listener interno](../classic/ps-sql-int-listener.md)
> * [Listener esterno](../classic/ps-sql-ext-listener.md)
> 
> 

Questo argomento illustra come configurare un listener per un gruppo di disponibilità AlwaysOn accessibile esternamente su Internet. Ciò è possibile tramite l'associazione dell'indirizzo **IP virtuale pubblico (indirizzo VIP)** del servizio cloud con il listener.

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

Il gruppo di disponibilità può contenere repliche solo locali, solo di Azure oppure sia locali che di Azure per le configurazioni ibride. Le repliche di Azure possono trovarsi nella stessa area o in più aree grazie a più reti virtuali (VNet). I passaggi seguenti presuppongono che sia già stato [configurato un gruppo di disponibilità](../classic/portal-sql-alwayson-availability-groups.md) ma che non sia stato configurato un listener.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Linee guida e limitazioni per listener esterni
Occorre notare le linee guida seguenti relative al listener del gruppo di disponibilità in Azure quando si esegue la distribuzione usando l'indirizzo IP virtuale pubblico del servizio cloud:

* Il listener del gruppo di disponibilità è supportato su Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* L'applicazione client deve trovarsi in un servizio cloud diverso rispetto a quello che contiene le VM del gruppo di disponibilità. Azure non supporta Direct Server Return con client e server residenti nello stesso servizio cloud.
* Per impostazione predefinita, la procedura descritta in questo articolo descrive come configurare un listener per usare l'indirizzo IP virtuale (VIP) del servizio cloud. Tuttavia, è possibile riservare e creare più indirizzi VIP per il servizio cloud. In questo modo è possibile usare la procedura di questo articolo per creare più listener, ciascuno di essi associato a un indirizzo IP virtuale diverso. Per informazioni su come creare più indirizzi VIP, vedere [più indirizzi IP virtuali per il servizio cloud](../../../load-balancer/load-balancer-multivip.md).
* Se si sta creando un listener per un ambiente ibrido, la rete locale deve disporre di connettività a Internet pubblico oltre che al VPN da sito a sito con la rete virtuale di Azure. Quando nella subnet di Azure, il listener del gruppo di disponibilità è raggiungibile solo tramite indirizzo IP pubblico del rispettivo servizio cloud.
* Non è supportata la creazione di un listener esterno nello stesso servizio cloud in cui è presente anche un listener interno utilizzando il servizio di bilanciamento del carico interno (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinare l'accessibilità del listener
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Questo articolo illustra la creazione di un listener che usa il **bilanciamento del carico esterno**. Se si vuole un listener privato per la rete virtuale, vedere la versione di questo articolo che illustra la procedura per la configurazione di un [listener con ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Creare endpoint VM con bilanciamento del carico con Direct Server Return
Il servizio di bilanciamento del carico esterno usa l'indirizzo IP virtuale pubblico del servizio cloud che ospita le VM. Non è quindi necessario creare o configurare il servizio di bilanciamento del carico in questo caso.

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
    
1. Copiare lo script di PowerShell seguente in un editor di testi e impostare i valori variabili in base all'ambiente. Sono stati forniti valori predefiniti per alcuni parametri. Si noti che se il gruppo di disponibilità si estende tra diverse aree di Azure, è necessario eseguire lo script una volta in ogni data center per il servizio cloud e i nodi che risiedono nel data center.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Dopo aver impostato le variabili, copiare lo script dall'editor di testo nella sessione di Azure PowerShell per eseguirlo. Se nel prompt viene ancora visualizzato >>, digitare di nuovo ENTER per assicurarsi che l'esecuzione dello script sia stata avviata.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Se necessario, verificare che KB2854082 sia installato.
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Aprire le porte firewall nei nodi del gruppo di disponibilità in
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Creare il listener del gruppo di disponibilità

Creare il listener del gruppo di disponibilità in due passaggi. Creare prima di tutto la risorsa cluster del punto di accesso client e configurare le dipendenze. In secondo luogo configurare le risorse del cluster con PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Creare il punto di accesso client e configurare le dipendenze del cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurare le risorse del cluster in PowerShell
1. Per il servizio di bilanciamento del carico esterno, è necessario ottenere l'indirizzo IP virtuale pubblico del servizio cloud contenente le repliche. Accedere al portale di Azure classico. Passare al servizio cloud che contiene la macchina virtuale del gruppo di disponibilità. Aprire la visualizzazione **Dashboard** .
2. Annotare l'indirizzo visualizzato sotto **Indirizzo IP virtuale pubblico (VIP)**. Se la soluzione interessa più reti virtuali, ripetere questo passaggio per ogni servizio cloud che contiene una macchina virtuale che ospita una replica.
3. Su una delle macchine virtuali copiare lo script di PowerShell seguente in un editor di testo e impostare le variabili sui valori annotati prima.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Dopo aver impostato le variabili, aprire una finestra con privilegi elevati di Windows PowerShell, quindi copiare lo script dall'editor di testo e incollarlo nella sessione di Azure PowerShell per eseguirlo. Se nel prompt viene ancora visualizzato >>, digitare di nuovo ENTER per assicurarsi che l'esecuzione dello script sia stata avviata.
5. Ripetere questo passaggio su ogni macchina virtuale. Questo script consente di configurare la risorsa Indirizzo IP con l'indirizzo IP del servizio cloud e di impostare altri parametri come la porta probe. Quando la risorsa Indirizzo IP viene portata online, può quindi rispondere al polling sulla porta probe dall'endpoint con carico bilanciato, creato in precedenza in questa esercitazione.

## <a name="bring-the-listener-online"></a>Portare online il listener
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Operazioni di completamento della procedura
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testare il listener del gruppo di disponibilità (entro la stessa rete virtuale)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testare il listener del gruppo di disponibilità (su Internet)
Per accedere al listener dall'esterno della rete virtuale, è necessario usare il servizio di bilanciamento del carico esterno/pubblico (illustrato in questo argomento) invece del servizio di bilanciamento del carico interno, che è accessibile solo entro la stessa rete virtuale. Nella stringa di connessione specificare il nome del servizio cloud. Ad esempio, se è disponibile un servizio cloud con nome *mycloudservice*, l'istruzione sqlcmd sarà analoga alla seguente:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

A differenza dell'esempio precedente, è necessario usare l'autenticazione SQL, perché il chiamante non può usare l'autenticazione di Windows su Internet. Per altre informazioni, vedere il blog relativo a un [gruppo di disponibilità AlwaysOn nelle VM di Azure: scenari di connettività client](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Quando si usa l'autenticazione SQL, assicurarsi di creare lo stesso account di accesso in entrambe le repliche. Per altre informazioni sulla risoluzione dei problemi degli account di accesso con gruppi di disponibilità, vedere il post di blog relativo a [come mappare gli account di accesso o usare un utente di database SQL contenuto per connettersi ad altre repliche ed eseguire il mapping ai database di disponibilità](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se le repliche AlwaysOn si trovano in subnet diverse, i client devono specificare **MultisubnetFailover=True** nella stringa di connessione. Di conseguenza, vengono eseguiti tentativi di connessione paralleli alle repliche nelle diverse subnet. Si noti che questo scenario include la distribuzione di un gruppo di disponibilità AlwaysOn tra più aree.

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]


