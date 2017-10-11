---
title: "Gruppi di disponibilità di SQL Server: Macchine virtuali di Azure: ripristino di emergenza | Documentazione Microsoft"
description: "Questo articolo illustra come configurare un gruppo di disponibilità SQL Server nelle macchine virtuali di Azure con una replica in un'area diversa."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 1ce90cf4bae66bfd6387a2698fd9b1ba7fc64595
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Configurare un gruppo di disponibilità AlwaysOn in macchine virtuali di Azure in aree diverse

Questo articolo descrive come configurare la replica di un gruppo di disponibilità SQL Server AlwaysOn in macchine virtuali di Azure in una località di Azure remota. Usare questa configurazione per supportare il ripristino di emergenza.

Questo articolo si applica a Macchine virtuali di Azure in modalità Resource Manager.

L'immagine seguente illustra una distribuzione comune di un gruppo di disponibilità nelle macchine virtuali di Azure:

   ![Gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

In questa distribuzione tutte le macchine virtuali sono in un'unica area di Azure. Le repliche del gruppo di disponibilità possono avere il commit sincrono con failover automatico in SQL-1 e SQL-2. Per creare questa architettura, vedere [Availability Group template or tutorial](virtual-machines-windows-portal-sql-availability-group-overview.md) (Modello o esercitazione per i gruppi di disponibilità).

Questa architettura è soggetta a tempi di inattività se l'area di Azure diventa inaccessibile. Per ovviare a questa vulnerabilità, aggiungere una replica in un'area di Azure diversa. Il diagramma seguente illustra l'aspetto della nuova architettura:

   ![Ripristino di emergenza del gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Il diagramma precedente illustra una nuova macchina virtuale denominata SQL-3. SQL-3 è in un'area di Azure diversa. SQL-3 viene aggiunta al cluster di failover di Windows Server. SQL-3 può ospitare una replica del gruppo di disponibilità. Si noti infine che l'area di Azure per SQL-3 ha un nuovo servizio di bilanciamento del carico di Azure.

>[!NOTE]
> Un set di disponibilità di Azure è necessario quando più macchine virtuali sono nella stessa area. Se una sola macchina virtuale è nell'area, il set di disponibilità non è necessario. È possibile inserire una macchina virtuale in un set di disponibilità solo al momento della creazione. Se la macchina virtuale è già in un set di disponibilità, è possibile aggiungere una macchina virtuale per un'altra replica in seguito.

In questa architettura la replica nell'area remota viene in genere configurata con la modalità di disponibilità commit asincrono e la modalità di failover manuale.

Quando le repliche del gruppo di disponibilità sono nelle macchine virtuali di Azure in aree di Azure diverse, ogni area richiede:

* Un gateway di rete virtuale
* Una connessione gateway di rete virtuale

Il diagramma seguente illustra come le reti comunicano tra data center.

   ![Gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Questa architettura è soggetta a costi per i dati in uscita replicati tra le aree di Azure. Vedere [Prezzi per la larghezza di banda](http://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Creare una replica remota

Per creare una replica in un data center remoto, seguire questa procedura:

1. [Creare una rete virtuale nella nuova area](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

1. [Configurare una connessione da rete virtuale a rete virtuale con il portale di Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >In alcuni casi, potrebbe essere necessario usare PowerShell per creare la connessione da rete virtuale a rete virtuale. Se, ad esempio, si usano account Azure diversi, non è possibile configurare la connessione nel portale. In questi casi, vedere [Configurare una connessione da rete virtuale a rete virtuale con il portale di Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Creare un controller di dominio nella nuova area](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Questo controller di dominio fornisce l'autenticazione se il controller di dominio nel sito primario non è disponibile.

1. [Creare una macchina virtuale SQL Server nella nuova area](virtual-machines-windows-portal-sql-server-provision.md).

1. [Creare un servizio di bilanciamento del carico di Azure in rete nella nuova area](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Questo servizio di bilanciamento del carico deve:

   - Essere nella stessa rete e subnet della nuova macchina virtuale.
   - Avere un indirizzo IP statico per il listener del gruppo di disponibilità.
   - Includere un pool back-end costituito solo dalle macchine virtuali nella stessa area del servizio di bilanciamento del carico.
   - Usare il probe di una porta TCP specifico dell'indirizzo IP.
   - Avere una regola di bilanciamento del carico specifica dell'istanza di SQL Server nella stessa area.  

1. [Aggiungere la funzionalità di clustering di failover alla nuova istanza di SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Aggiungere la nuova istanza di SQL Server al dominio](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Impostare il nuovo account del servizio SQL Server per l'uso di un account di dominio](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Aggiungere la nuova istanza di SQL Server al cluster di failover di Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Creare una risorsa indirizzo IP nel cluster.

   È possibile creare la risorsa indirizzo IP in Gestione cluster di failover. Fare clic con il pulsante destro del mouse sul ruolo del gruppo di disponibilità, scegliere **Aggiungi risorsa**, **Altre risorse** e fare clic su **Indirizzo IP**.

   ![Creare un indirizzo IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Configurare l'indirizzo IP come segue:

   - Usare la rete dal data center remoto.
   - Assegnare l'indirizzo IP dal nuovo servizio di bilanciamento del carico di Azure. 

1. Nella nuova istanza di SQL Server in Gestione configurazione SQL Server [abilitare i gruppi di disponibilità AlwaysOn](http://msdn.microsoft.com/library/ff878259.aspx).

1. [Aprire le porte del firewall nella nuova istanza di SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   I numeri di porta che è necessario aprire dipendono dall'ambiente. Aprire le porte per l'endpoint del mirroring e il probe di integrità di bilanciamento del carico di Azure.

1. [Aggiungere una replica al gruppo di disponibilità nella nuova istanza di SQL Server](http://msdn.microsoft.com/library/hh213239.aspx).

   Per una replica in un'area di Azure remota, impostarla per la replica asincrona con il failover manuale.  

1. Aggiungere la risorsa indirizzo IP come dipendenza per il cluster del punto di accesso client listener (nome della rete).

   Lo screenshot seguente illustra una risorsa cluster di tipo indirizzo IP configurata correttamente:

   ![Gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Il gruppo di risorse cluster include entrambi gli indirizzi IP. Entrambi gli indirizzi IP sono dipendenze per il punto di accesso client listener. Usare l'operatore **OR** nella configurazione delle dipendenze del cluster.

1. [Impostare i parametri del cluster in PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Eseguire lo script PowerShell con il nome di rete cluster, l'indirizzo IP e la porta probe configurati nel servizio di bilanciamento del carico nella nuova area.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Impostare la connessione per più subnet

La replica nel data center remoto fa parte del gruppo di disponibilità, ma si trova in un'altra subnet. Se questa replica diventa quella primaria, possono verificarsi timeout della connessione dell'applicazione. Questo comportamento è lo stesso di un gruppo di disponibilità locale in una distribuzione su più subnet. Per consentire le connessioni dalle applicazioni client, aggiornare la connessione client o configurare la memorizzazione nella cache della risoluzione dei nomi nella risorsa nome di rete del cluster.

È preferibile aggiornare le stringhe di connessione client per impostare `MultiSubnetFailover=Yes`. Vedere [Connessione con MultiSubnetFailover](http://msdn.microsoft.com/library/gg471494#Anchor_0).

Se non è possibile modificare le stringhe di connessione, è possibile configurare la memorizzazione nella cache della risoluzione dei nomi. Vedere [Connection Timeouts in Multi-subnet Availability Group](http://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/) (Timeout connessione in un gruppo di disponibilità su più subnet).

## <a name="fail-over-to-remote-region"></a>Effettuare il failover in un'area remota

Per testare la connettività del listener all'area remota, è possibile effettuare il failover della replica nell'area remota. Mentre la replica è asincrona, il failover è soggetto a perdita potenziale di dati. Per effettuare il failover senza perdita di dati, impostare la modalità di disponibilità come sincrona e la modalità di failover come automatica. Seguire questa procedura:

1. In **Esplora oggetti** connettersi all'istanza di SQL Server che ospita la replica primaria.
1. In **Gruppi di disponibilità AlwaysOn**, **Gruppi di disponibilità** fare clic con il pulsante destro del mouse sul gruppo di disponibilità e scegliere **Proprietà**.
1. Nella pagina **Generale** in **Repliche di disponibilità** impostare la replica secondaria nel sito di ripristino di emergenza per l'uso della modalità di disponibilità **Commit sincrono** e della modalità di failover **Automatico**.
1. Se è presente una replica secondaria nello stesso sito della replica primaria per la disponibilità elevata, impostare questa replica su **Commit asincrono** e **Manuale**.
1. Fare clic su OK.
1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul gruppo di disponibilità e scegliere **Mostra dashboard**.
1. Nel dashboard verificare che la replica nel sito di ripristino di emergenza sia sincronizzata.
1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul gruppo di disponibilità e scegliere **Failover**. SQL Server Management Studio apre una procedura guidata per il failover di SQL Server.  
1. Fare clic su **Avanti** e selezionare l'istanza di SQL Server nel sito del ripristino di emergenza. Fare di nuovo clic su **Avanti** .
1. Connettersi all'istanza di SQL Server nel sito di ripristino di emergenza e fare clic su **Avanti**.
1. Nella pagina **Riepilogo** verificare le impostazioni e fare clic su **Fine**.

Dopo avere testato la connettività, spostare di nuovo la replica primaria nel data center primario e configurare di nuovo la modalità di disponibilità sulle normali impostazioni operative. La tabella seguente indica le normali impostazioni operative per l'architettura descritta in questo documento:

| Percorso | Istanza del server | Ruolo | Modalità di disponibilità | Modalità di failover
| ----- | ----- | ----- | ----- | -----
| Data center primario | SQL-1 | Primario | Sincrono | Automatico
| Data center primario | SQL-2 | Secondario | Sincrono | Automatico
| Data center secondario o remoto | SQL-3 | Secondario | Asincrono | Manuale


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Altre informazioni sul failover manuale forzato e pianificato

Per ulteriori informazioni, vedere gli argomenti seguenti:

- [Eseguire un failover manuale pianificato di un gruppo di disponibilità (SQL Server)](http://msdn.microsoft.com/library/hh231018.aspx)
- [Eseguire un failover manuale forzato di un gruppo di disponibilità (SQL Server)](http://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Altri collegamenti

* [Gruppi di disponibilità AlwaysOn](http://msdn.microsoft.com/library/hh510230.aspx)
* [Macchine virtuali di Azure](http://docs.microsoft.com/azure/virtual-machines/windows/)
* [Servizi di bilanciamento del carico di Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Set di disponibilità di Azure](../manage-availability.md)
