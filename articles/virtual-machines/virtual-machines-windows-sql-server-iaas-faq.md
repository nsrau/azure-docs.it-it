<properties
	pageTitle="Domande frequenti su SQL Server in macchine virtuali di Azure| Microsoft Azure"
	description="Questo articolo offre risposta ad alcune domande frequenti sull'esecuzione di SQL Server in macchine virtuali di Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/13/2016"
	ms.author="v-shysun"/>  

# Domande frequenti su SQL Server in macchine virtuali di Azure

Questo argomento offre risposta ad alcune delle domande più comuni sull'esecuzione di [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Domande frequenti

1. **Come si crea una macchina virtuale di Azure con SQL Server?**

	Per eseguire questa operazione è possibile procedere in due modi: La soluzione più semplice consiste nel creare una macchina virtuale che include SQL Server. Per un'esercitazione sulla registrazione in Azure e sulla creazione di una VM di SQL Server dal portale, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). È anche possibile installare manualmente SQL Server in una VM e usare di nuovo una licenza locale con [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Qual è la differenza tra VM di SQL Server e servizio Database SQL?**

	Dal punto di vista concettuale l'esecuzione di SQL Server in una macchina virtuale di Azure non è diversa dall'esecuzione di SQL Server in un centro dati remoto. Per contro, il servizio [Database SQL](../sql-database/sql-database-technical-overview.md) offre una soluzione DaaS (Database-as-a-service). Con Database SQL non si ha accesso ai computer che ospitano i database. Per un confronto completo, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Come si esegue la migrazione di un database SQL Server locale nel cloud?**

	Creare prima una macchina virtuale di Azure con un'istanza di SQL Server, quindi eseguire la migrazione dei database locali in tale istanza. Per alcune strategie di migrazione dei dati, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).

2. **È possibile modificare le funzionalità installate o installare una seconda istanza di SQL Server nella stessa VM?**

	Sì. Il supporto di installazione di SQL Server si trova in una cartella nell'unità **C**. Eseguire **Setup.exe** da tale percorso per aggiungere nuove istanze di SQL Server o per modificare altre funzionalità di SQL Server installate nella macchina virtuale.

3. **Come si esegue l'aggiornamento a una nuova versione o a una nuova edizione di SQL Server in una VM di Azure?**

	Attualmente non è disponibile alcun aggiornamento sul posto per SQL Server in esecuzione in una VM di Azure. Creare una nuova macchina virtuale di Azure con la versione o l'edizione di SQL Server voluta, quindi eseguire la migrazione dei database nel nuovo server tramite [tecniche di migrazione dati](virtual-machines-windows-migrate-sql.md) standard.

4. **Come si installa una copia di SQL Server con licenza in una VM di Azure?**

	Copiare il supporto di installazione di SQL Server nella VM Windows Server e quindi installare SQL Server nella VM. Per motivi di licenza, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **È necessario pagare i costi relativi a SQL Server per una VM se questa viene usata solo per standby o failover?**

	Se si vuole creare la VM di SQL Server tramite la raccolta, è necessario avere una licenza separata per la VM di SQL Server standby e il prezzo è lo stesso. Se si installa SQL Server manualmente in una macchina virtuale con [Mobilità delle licenze](https://azure.microsoft.com/pricing/license-mobility/), è disponibile l'opzione di un'istanza di SQL Server passiva gratuita per il failover. Verificare i requisiti e le restrizioni.

6. **Come si applicano gli aggiornamenti e i Service Pack a una VM di SQL Server?**

	Le macchine virtuali consentono di controllare il computer host e di decidere quindi quando e come applicare gli aggiornamenti. Per il sistema operativo, è possibile applicare manualmente gli aggiornamenti di Windows oppure abilitare un servizio di pianificazione definito [Applicazione automatica delle patch](virtual-machines-windows-classic-sql-automated-patching.md). Applicazione automatica delle patch installa tutti gli aggiornamenti contrassegnati come importanti, inclusi gli aggiornamenti di SQL Server in tale categoria. Gli aggiornamenti facoltativi di SQL Server devono essere installati manualmente.

7. **È possibile impostare configurazioni non visualizzate nella raccolta di macchine virtuali, ad esempio Windows 2008 R2 + SQL Server 2012?**

	No. Per le raccolte di macchine virtuali che includono SQL Server è necessario selezionare una delle immagini disponibili.

9. **Come si installa SQL Server Data Tools in una VM di Azure?**

	Scaricare e installare SQL Server Data Tools da [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2013](https://www.microsoft.com/it-IT/download/details.aspx?id=42313).

## Risorse

Per una panoramica di SQL Server nelle macchine virtuali di Azure, guardare il video [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016) (Macchine virtuali di Azure è la piattaforma ottimale per SQL Server 2016). Un'ottima introduzione è anche l'argomento [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Altre risorse:

- [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Migrazione di un database a SQL Server su una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md)
- [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md)
- [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md)
- [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

<!---HONumber=AcomDC_0914_2016-->