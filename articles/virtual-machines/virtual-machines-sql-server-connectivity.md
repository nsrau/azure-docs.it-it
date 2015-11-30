<properties 
	pageTitle="Collegamento a una macchina virtuale di SQL Server | Microsoft Azure"
	description="Questo argomento utilizza le risorse create con il modello di distribuzione classica e descrive come connettersi a SQL Server in esecuzione in una macchina virtuale in Azure. Gli scenari variano a seconda della configurazione di rete e della posizione del client."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"    
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/12/2015"
	ms.author="jroth" />

# Connettersi a una macchina virtuale di SQL Server in Azure

## Panoramica

La configurazione della connettività a SQL Server in esecuzione in una macchina virtuale di Azure non è molto diversa dalla procedura necessaria per un'istanza di SQL Server locale. È comunque necessario seguire la procedura di configurazione che riguarda il firewall, l'autenticazione e gli account di accesso al database.

Esistono tuttavia alcuni aspetti relativi alla connettività di SQL Server che sono specifici per le macchine virtuali di Azure. Questo articolo illustra alcuni [scenari di connettività generali](#connection-scenarios) e quindi descrive la [procedura dettagliata per la configurazione della connettività di SQL Server in una macchina virtuale di Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

Questo articolo è incentrato sulla connettività. Per una procedura dettagliata completa del provisioning e della connettività, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-provision-sql-server.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

## Scenari di connessione

La modalità di connessione di un client a SQL Server in esecuzione in una macchina virtuale varia a seconda della posizione del client e della configurazione tra il computer e la rete. Tali scenari includono:

- [Configurare e connettersi a più macchine virtuali di SQL Server nello stesso servizio cloud di Azure](#connect-to-sql-server-in-the-same-cloud-service)
- [Connettersi a SQL Server tramite Internet](#connect-to-sql-server-over-the-internet)
- [Connettersi a SQL Server nella stessa rete virtuale](#connect-to-sql-server-in-the-same-virtual-network)

### Configurare e connettersi a più macchine virtuali di SQL Server nello stesso servizio cloud di Azure

È possibile creare più macchine virtuali nello stesso servizio cloud. Per comprendere meglio questo scenario, vedere [Come connettere le macchine virtuali con una rete virtuale o un servizio cloud](cloud-services-connect-virtual-machine.md).

Seguire innanzitutto la [procedura descritta in questo articolo per configurare la connettività](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Si noti che non è necessario configurare un endpoint pubblico se si intende connettere computer nello stesso servizio cloud.

È possibile usare la macchina virtuale **nomehost** nella stringa di connessione al client. Il nome host è il nome assegnato alla macchina virtuale durante la creazione. Ad esempio, se la macchina virtuale SQL è denominata **mysqlvm** e il nome DNS del servizio cloud è **mycloudservice.cloudapp.net**, una macchina virtuale client nello stesso servizio cloud potrebbe connettersi usando la stringa di connessione seguente:

	"Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### Connettersi a SQL Server tramite Internet

Se si desidera connettersi al motore di database di SQL Server tramite Internet, è necessario creare un endpoint della macchina virtuale per le comunicazioni TCP in ingresso. In questo passaggio di configurazione di Azure, il traffico della porta TCP in ingresso viene indirizzato a una porta TCP accessibile alla macchina virtuale.

Seguire innanzitutto la [procedura descritta in questo articolo per configurare la connettività](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Qualsiasi client con accesso a Internet potrà quindi connettersi all'istanza di SQL Server specificando il nome DNS del servizio cloud (ad esempio **mycloudservice.cloudapp.net**) e l'endpoint della macchina virtuale (ad esempio **57500**).

	"Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Sebbene in questo modo venga abilitata la connettività per i client tramite Internet, ciò non significa che chiunque può connettersi all'istanza di SQL Server. I client esterni dovranno disporre del nome utente e della password corretti. Per una maggiore sicurezza, non usare la nota porta 1433 per l'endpoint pubblico della macchina virtuale. Se possibile, è consigliabile aggiungere un ACL all'endpoint per limitare il traffico ai soli client autorizzati. Per istruzioni sull'uso di ACL con gli endpoint, vedere [Gestire l'elenco di controllo di accesso su un endpoint](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint)

>[AZURE.NOTE]È importante tenere presente che quando si usa questa tecnica per comunicare con SQL Server, tutti i dati restituiti vengono considerati come traffico in uscita dal data center. Questa tecnica è soggetta ai normali [prezzi dei trasferimenti di dati in uscita](http://azure.microsoft.com/pricing/details/data-transfers). Ciò vale anche se si usa questa tecnica da un altro computer o da un servizio cloud nello stesso data center di Azure, poiché il traffico passa comunque attraverso il servizio pubblico di bilanciamento del carico di Azure.

### Connettersi a SQL Server nella stessa rete virtuale

La [rete virtuale](..\virtual-network\virtual-networks-overview.md) supporta scenari aggiuntivi. È possibile connettere le macchine virtuali nella stessa rete virtuale, anche se si trovano in servizi cloud diversi. Con una [VPN da sito a sito](../vpn-gateway/vpn-gateway-site-to-site-create.md) è possibile creare un'architettura ibrida che connette le macchine virtuali a computer e reti locali.

Le reti virtuali consentono inoltre di aggiungere le macchine virtuali di Azure a un dominio. Si tratta dell'unico modo per usare l'autenticazione di Windows per SQL Server. Gli altri scenari di connessione richiedono l'autenticazione SQL con nomi utente e password.

Seguire innanzitutto la [procedura descritta in questo articolo per configurare la connettività](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Se si desidera configurare un ambiente di dominio e l'autenticazione di Windows, non è necessario seguire la procedura descritta in questo articolo per configurare l'autenticazione SQL e gli account di accesso. Inoltre, questo scenario non richiede un endpoint pubblico.

Presupponendo che il DNS sia stato configurato, è possibile connettersi all'istanza di SQL Server specificando il nome host della macchina virtuale SQL Server nella stringa di connessione. Nell'esempio seguente si presuppone che sia stata configurata anche l'autenticazione di Windows e che all'utente sia stato concesso l'accesso all'istanza di SQL Server.

	"Server=mysqlvm;Integrated Security=true" 

Si noti che in questo scenario è possibile anche specificare l'indirizzo IP della macchina virtuale.

## Procedura per la configurazione della connettività di SQL Server in una macchina virtuale di Azure

[AZURE.INCLUDE [Connettersi a SQL Server in una macchina virtuale](../../includes/virtual-machines-sql-server-connection-steps.md)]

## Passaggi successivi

Per la procedura di configurazione della connettività e del provisioning, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-provision-sql-server.md).

Se si intende anche usare gruppi di disponibilità AlwaysOn per la disponibilità elevata e il ripristino di emergenza, è consigliabile implementare un listener. I client del database si connettono al listener anziché connettersi direttamente a una delle istanze di SQL Server. Il listener indirizza i client alla replica primaria nel gruppo di disponibilità. Per altre informazioni, vedere l'articolo relativo alla [configurazione di un listener di ILB per gruppi di disponibilità AlwaysOn in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

È importante esaminare tutte le procedure consigliate sulla sicurezza per SQL Server in esecuzione in una macchina virtuale di Azure. Per altre informazioni, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-security-considerations.md).

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Nov15_HO4-->