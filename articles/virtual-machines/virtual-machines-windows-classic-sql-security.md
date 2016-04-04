<properties
	pageTitle="Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure | Microsoft Azure"
	description="Questo argomento fa riferimento alle risorse create con il modello di distribuzione classica e fornisce una guida generale per la protezione di SQL Server in esecuzione nelle macchine virtuali di Azure."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/04/2015"
	ms.author="jroth" />

# Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure

Questo argomento include le linee guida di sicurezza generali che consentono di stabilire l'accesso sicuro alle istanze di SQL Server in una macchina virtuale di Azure. Per garantire tuttavia la migliore protezione alle istanze del database SQL Server in Azure, è consigliabile implementare le tradizionali procedure di sicurezza locali oltre alle procedure consigliate per la sicurezza in Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Per altre informazioni sulle procedure di sicurezza di SQL Server, vedere: [Documento relativo alle procedure consigliate sulle attività operative e amministrative per la sicurezza di SQL Server 2008 R2](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure è conforme a molteplici normative e standard di settore che possono favorire la compilazione di una soluzione compatibile con SQL Server in esecuzione in una macchina virtuale. Per informazioni sulla conformità alle normative con Azure, vedere il [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/).

Di seguito è riportato un elenco di consigli relativi alla sicurezza da considerare durante la configurazione e la connessione all'istanza di SQL Server in una macchina virtuale di Azure.

## Considerazioni sulla gestione di account:

- Creare un account amministratore locale univoco non denominato **Amministratore**.

- Usare password complesse per tutti gli account. Per altre informazioni sulla creazione di password complesse, vedere l'articolo relativo alla [creazione di password complesse](http://go.microsoft.com/fwlink/?LinkId=293596) nel Centro sicurezza e protezione.

- Per impostazione predefinita, in Azure viene selezionata l'autenticazione di Windows durante l'installazione della macchina virtuale SQL Server. L'account di accesso **SA** è pertanto disabilitato e viene assegnata una password tramite il programma di installazione. È consigliabile non usare o abilitare l'account di accesso **SA**. Di seguito sono riportate strategie alternative nel caso si desiderasse usare un account di accesso SQL:
	- Creare un account SQL con autorizzazioni **CONTROLLO SERVER**.
	- Se è necessario usare un account di accesso **SA**, abilitarlo e rinominarlo, quindi assegnare una nuova password.
	- Entrambe le opzioni indicate in precedenza richiedono una modifica della modalità di autenticazione in **Autenticazione di SQL Server e di Windows**. Per altre informazioni, vedere [Modifica della modalità di autenticazione del server](https://msdn.microsoft.com/library/ms188670.aspx).

## Considerazioni sulla protezione delle connessioni alla macchina virtuale di Azure:

- Per amministrare le macchine virtuali, si consideri la possibilità di usare [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) anziché le porte RDP pubbliche.

- Rimuovere tutti gli endpoint inutilizzati nella macchina virtuale.

- Abilitare un'opzione di connessione crittografata per un'istanza del motore di database di SQL Server in Macchine virtuali di Azure. Configurare l'istanza di SQL server con un certificato firmato. Per altre informazioni, vedere [Abilitazione di connessioni crittografate al Motore di database](https://msdn.microsoft.com/library/ms191192.aspx) e [Sintassi della stringa di connessione](https://msdn.microsoft.com/library/ms254500.aspx).

- Se l'accesso alle macchine virtuali verrà eseguito solo da una rete specifica, usare Windows Firewall per limitare l'accesso a determinati indirizzi IP o subnet di rete. È inoltre possibile aggiungere un ACL all'endpoint per limitare il traffico ai soli client autorizzati. Per istruzioni sull'uso di ACL con gli endpoint, vedere [Gestire l'elenco di controllo di accesso su un endpoint](virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint)

## Passaggi successivi

Se si è interessati anche alle procedure consigliate relative alle prestazioni, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-classic-sql-perf.md).

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server nella panoramica delle Macchine virtuali di Azure](virtual-machines-windows-classic-sql-overview.md).

<!---HONumber=AcomDC_0323_2016-->