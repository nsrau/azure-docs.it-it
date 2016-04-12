<properties
	pageTitle="Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure (Gestione risorse)"
	description="Informazioni su come automatizzare la configurazione della crittografia di SQL Server per l'uso con l'insieme di credenziali delle chiavi di Azure. Questo argomento illustra come usare l'integrazione dell'insieme di credenziali delle chiavi di Azure con le macchine virtuali di SQL Server create con Gestione risorse."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="03/24/2016"
	ms.author="jroth"/>

# Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure (Gestione risorse)

> [AZURE.SELECTOR]
- [Gestione risorse](virtual-machines-windows-ps-sql-keyvault.md)
- [Classico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## Panoramica
Esistono più funzionalità di crittografia di SQL Server, ad esempio [transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [crittografia a livello di colonna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) e [crittografia di backup](https://msdn.microsoft.com/library/dn449489.aspx). Queste modalità di crittografia richiedono la gestione e l'archiviazione delle chiavi usate per la crittografia. Il servizio dell'insieme di credenziali delle chiavi di Azure (AKV) è progettato per migliorare la sicurezza e la gestione di queste chiavi in una posizione sicura e a elevata disponibilità. Il [connettore di SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) consente a SQL Server di usare queste chiavi dall'insieme di credenziali delle chiavi di Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

Se si esegue SQL Server con computer locali, sono disponibili [passaggi per accedere all'insieme di credenziali delle chiavi di Azure dal computer locale con SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Se si esegue SQL Server in macchine virtuali di Azure, è possibile risparmiare tempo usando la funzionalità di *Integrazione dell'insieme di credenziali delle chiavi di Azure*.

Quando questa funzionalità è abilitata, installa automaticamente il connettore di SQL Server, configura il provider EKM per accedere all'insieme di credenziali delle chiavi di Azure e crea le credenziali per consentire l'accesso all'insieme di credenziali. Se sono stati esaminati i passaggi nella documentazione locale menzionati in precedenza, si noterà che questa funzionalità consente di automatizzare i passaggi 2 e 3. L'unica attività che è comunque necessario eseguire manualmente è la creazione delle chiavi e dell'insieme di credenziali delle chiavi. Una volta completata questa operazione, l'intera installazione della macchina virtuale di SQL è automatizzata. Quando la funzionalità ha completato l'installazione, è possibile eseguire istruzioni T-SQL per iniziare la crittografia dei database o del backup regolarmente.

[AZURE.INCLUDE [Preparare l'integrazione di AKV](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## Abilitazione dell'integrazione di AKV
Se si esegue il provisioning di una nuova macchina virtuale SQL Server con Resource Manager, il portale di Azure offre una procedura per abilitare l'integrazione dell'insieme di credenziali delle chiavi di Azure.

![Integrazione dell'insieme di credenziali delle chiavi di Gestione risorse di Azure per SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Per una procedura dettagliata del provisioning, vedere l'articolo relativo al [provisioning di una macchina virtuale SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).

Per abilitare l'integrazione di AKV su una macchina virtuale esistente è possibile usare un modello. Per altre informazioni, vedere l'articolo relativo al [modello di avvio rapido di Azure per l'integrazione dell'insieme di credenziali delle chiavi di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-keyvault-setup).


[AZURE.INCLUDE [Passaggi successivi integrazione AKV](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!---HONumber=AcomDC_0330_2016-->