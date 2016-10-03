<properties
	pageTitle="Connettersi a una macchina virtuale di SQL Server (Gestione risorse) | Microsoft Azure"
	description="Informazioni su come connettersi a SQL Server in esecuzione su una macchina virtuale di Azure. In questo argomento viene usato il modello di distribuzione classica. Gli scenari variano a seconda della configurazione di rete e della posizione del client."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"    
	tags="azure-resource-manager"/> 
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/21/2016"
	ms.author="jroth" /> 

# Connettersi a una macchina virtuale di SQL Server in Azure (Gestione risorse)

> [AZURE.SELECTOR]
- [Gestione risorse](virtual-machines-windows-sql-connect.md)
- [Classico](virtual-machines-windows-classic-sql-connect.md)

## Overview

Questo argomento descrive la modalità di connessione all'istanza di SQL Server in esecuzione su una macchina virtuale di Azure. Illustra alcuni [scenari di connettività generali](#connection-scenarios) e quindi descrive la [procedura dettagliata per la configurazione della connettività di SQL Server in una macchina virtuale di Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] Modello di distribuzione classica. 
Per visualizzare la versione classica di questo articolo, vedere [Connettersi a una macchina virtuale di SQL Server in Azure (distribuzione classica)](virtual-machines-windows-classic-sql-connect.md).

Per una procedura dettagliata completa del provisioning e della connettività, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

## Scenari di connessione

La modalità di connessione di un client a SQL Server in esecuzione in una macchina virtuale varia a seconda della posizione del client e della configurazione tra il computer e la rete. Tali scenari includono:

- [Connettersi a SQL Server tramite Internet](#connect-to-sql-server-over-the-internet)
- [Connettersi a SQL Server nella stessa rete virtuale](#connect-to-sql-server-in-the-same-virtual-network)

### Connettersi a SQL Server tramite Internet

Per connettersi al motore di database di SQL Server da Internet, sono necessari alcuni passaggi, ad esempio configurazione del firewall, abilitazione dell'autenticazione di SQL, nonché configurazione del gruppo di sicurezza di rete. Per consentire il traffico TCP sulla porta 1433, è necessario avere una regola del gruppo di sicurezza di rete.

Se si usa il portale per eseguire il provisioning di un'immagine di macchina virtuale di SQL Server con Resource Manager, questi passaggi vengono eseguiti automaticamente quando si seleziona **Pubblico** per l'opzione di connettività SQL:

![Opzione di connettività SQL pubblica durante il provisioning](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Se questa operazione non è stata eseguita durante il provisioning, è possibile configurare manualmente SQL Server e le macchine virtuali seguendo la [procedura per configurare manualmente la connettività in questo articolo](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] L'immagine della macchina virtuale per SQL Server Express Edition non abilita automaticamente il protocollo TCP/IP. Per Express Edition, è necessario usare Gestione configurazione SQL Server per [abilitare manualmente il protocollo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) dopo avere creato la VM.

Al termine, tutti i client con accesso a Internet potranno connettersi all'istanza di SQL Server specificando l'indirizzo IP pubblico della macchina virtuale o l'etichetta DNS assegnata a tale indirizzo IP. Se la porta di SQL Server è 1433, non è necessario specificarla nella stringa di connessione.

	"Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Sebbene in questo modo venga abilitata la connettività per i client tramite Internet, ciò non significa che chiunque può connettersi all'istanza di SQL Server. I client esterni dovranno disporre del nome utente e della password corretti. Per maggiore sicurezza, è possibile evitare la nota porta 1433. Ad esempio, se SQL Server è configurato per l'ascolto sulla porta 1500 e sono state stabilite regole del gruppo di sicurezza di rete e del firewall corrette, sarà possibile connettersi aggiungendo il numero di porta al nome del server, come nell'esempio seguente:

	"Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] È importante notare che quando si usa questa tecnica per comunicare con SQL Server, tutti i dati in uscita dai data center di Azure sono soggetti ai normali [prezzi di trasferimenti dei dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/).

### Connettersi a SQL Server nella stessa rete virtuale

La [rete virtuale](../virtual-network/virtual-networks-overview.md) supporta scenari aggiuntivi. È possibile connettere le VM nella stessa rete virtuale, anche se si trovano in gruppi di risorse diversi. Con una [VPN da sito a sito](../vpn-gateway/vpn-gateway-site-to-site-create.md) è possibile creare un'architettura ibrida che connette le macchine virtuali a computer e reti locali.

Le reti virtuali consentono inoltre di aggiungere le macchine virtuali di Azure a un dominio. Si tratta dell'unico modo per usare l'autenticazione di Windows per SQL Server. Gli altri scenari di connessione richiedono l'autenticazione SQL con nomi utente e password.

Se si usa il portale per eseguire il provisioning di un'immagine di macchina virtuale di SQL Server con Resource Manager, le regole del firewall corrette per la comunicazione sulla rete virtuale vengono impostate quando si seleziona **Privato** per l'opzione di connettività SQL. Se questa operazione non è stata eseguita durante il provisioning, è possibile configurare manualmente SQL Server e le macchine virtuali seguendo la [procedura per configurare manualmente la connettività in questo articolo](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Se però si prevede di configurare un ambiente di dominio e l'autenticazione di Windows, non è necessario seguire la procedura descritta in questo articolo per configurare l'autenticazione SQL e gli account di accesso. Non è nemmeno necessario configurare regole del gruppo di sicurezza di rete per l'accesso tramite Internet.

>[AZURE.NOTE] L'immagine della macchina virtuale per SQL Server Express Edition non abilita automaticamente il protocollo TCP/IP. Per Express Edition, è necessario usare Gestione configurazione SQL Server per [abilitare manualmente il protocollo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) dopo avere creato la VM.

Presupponendo che il DNS sia stato configurato nella rete virtuale, è possibile connettersi all'istanza di SQL Server specificando il nome computer della VM di SQL Server nella stringa di connessione. L'esempio seguente presuppone che sia stata configurata anche l'autenticazione di Windows e che all'utente sia stato concesso l'accesso all'istanza di SQL Server.

	"Server=mysqlvm;Integrated Security=true"

Si noti che in questo scenario è possibile anche specificare l'indirizzo IP della macchina virtuale.

## Procedura per la configurazione della connettività di SQL Server in una VM di Azure

La procedura seguente illustra come configurare manualmente la connettività all'istanza di SQL Server e quindi connettersi facoltativamente tramite Internet con SQL Server Management Studio (SSMS). Si noti che molte di queste operazioni vengono eseguite automaticamente quando si selezionano le opzioni di connettività di SQL Server appropriate nel portale.

Prima di poter eseguire la connessione all'istanza di SQL Server da un’altra VM o da Internet, è necessario completare le seguenti attività, come descritto nelle seguenti sezioni:

- [Aprire le porte TCP in Windows Firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurare SQL Server per l'ascolto sul protocollo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurare SQL Server per l'autenticazione in modalità mista](#configure-sql-server-for-mixed-mode-authentication)
- [Creare gli account di accesso di SQL Server](#create-sql-server-authentication-logins)
- [Configurare una regola in ingresso del gruppo di sicurezza di rete](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [Configurare un'etichetta DNS per l'indirizzo IP pubblico](#configure-a-dns-label-for-the-public-ip-address)
- [Eseguire la connessione al motore di database da un altro computer](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Connettersi a SQL Server in una macchina virtuale](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connettersi a SQL Server da una VM in Gestione risorse](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Connettersi a SQL Server da una VM in Gestione risorse](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## Passaggi successivi

Per la procedura di configurazione della connettività e del provisioning, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Esplorare il percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) per SQL Server in macchine virtuali di Azure.

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0921_2016-->