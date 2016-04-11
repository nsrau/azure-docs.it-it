<properties
	pageTitle="Connettere le macchine virtuali Linux in un servizio cloud | Microsoft Azure"
	description="Connettere le macchine virtuali Linux create con il modello di distribuzione classica a un servizio cloud di Azure o a una rete virtuale."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2016"
	ms.author="cynthn"/>

# Connettere le macchine virtuali Linux create con il modello di distribuzione classica con un servizio cloud o rete virtuale

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

Le macchine virtuali Linux create con il modello di distribuzione classica vengono sempre inserite in un servizio cloud. Il servizio cloud funge da contenitore e fornisce un nome DNS pubblico univoco, un indirizzo IP pubblico e un set di endpoint per accedere alla macchina virtuale su Internet. Il servizio cloud può essere in una rete virtuale, ma questo non è un requisito. È inoltre possibile [connettere le macchine virtuali Windows con una rete virtuale o un servizio cloud](virtual-machines-windows-classic-connect-vms.md).

Se un servizio cloud non si trova in una rete virtuale, è detto servizio cloud *autonomo*. Le macchine virtuali in un servizio cloud autonomo possono comunicare solo con altre macchine virtuali usando i nomi DNS pubblici delle altre macchine virtuali e il traffico verrà trasmesso attraverso Internet. Se un servizio cloud si trova in una rete virtuale, le macchine virtuali in tale servizio cloud possono comunicare con tutte le altre macchine virtuali nella rete virtuale senza inviare traffico su Internet.

Se si inseriscono le macchine virtuali nello stesso servizio cloud autonomo, si potranno continuare a usare il bilanciamento del carico e i set di disponibilità. Per altre informazioni, vedere [Bilanciamento del carico delle macchine virtuali](virtual-machines-linux-load-balance.md) e [Gestire la disponibilità delle macchine virtuali](virtual-machines-linux-manage-availability.md). Tuttavia, non è possibile organizzare le macchine virtuali nelle subnet o connettere un servizio cloud autonomo alla rete locale. Ad esempio:

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## Passaggi successivi

Dopo avere creato una macchina virtuale, è consigliabile [aggiungere un disco dati](virtual-machines-linux-classic-attach-disk.md), in modo che i servizi e i carichi di lavoro possano usarlo per archiviare i dati.

<!---HONumber=AcomDC_0330_2016-->