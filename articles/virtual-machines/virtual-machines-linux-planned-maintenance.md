<properties
	pageTitle="Manutenzione pianificata per macchine virtuali Linux | Microsoft Azure"
	description="Informazioni sulla manutenzione pianificata di Azure e sul relativo impatto sulle macchine virtuali Linux eseguite in Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="drewm"/>

# Manutenzione pianificata per macchine virtuali Linux in Azure

Informazioni sulla manutenzione pianificata di Azure e su come può influire sulla disponibilità delle macchine virtuali Linux. È anche possibile leggere informazioni sulla [manutenzione pianificata per macchine virtuali Windows](virtual-machines-windows-planned-maintenance.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Perché Azure esegue la manutenzione pianificata

Microsoft Azure esegue periodicamente aggiornamenti a livello globale per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host sottostante alle macchine virtuali. Molti di questi aggiornamenti vengono eseguiti senza alcun impatto sulle macchine virtuali o sui servizi cloud, inclusi gli aggiornamenti con mantenimento della memoria.

Tuttavia, alcuni aggiornamenti richiedono un riavvio delle macchine virtuali per applicare gli aggiornamenti necessari all'infrastruttura. Le macchine virtuali vengono arrestate durante l'applicazione delle patch all'infrastruttura e quindi riavviate.

Esistono due tipi di manutenzione che possono influire sulla disponibilità delle macchine virtuali: pianificata e non pianificata. Questa pagina descrive come Microsoft Azure esegue la manutenzione pianificata. Per altre informazioni sulla manutenzione non pianificata, vedere la pagina relativa al [confronto tra manutenzione pianificata e non pianificata](virtual-machines-linux-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0330_2016-->