<properties
	pageTitle="Definire le opzioni di ridondanza di archiviazione per Backup di Azure | Microsoft Azure"
	description="Informazioni sulla differenza tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale per determinare l'opzione di ridondanza di archiviazione per Backup di Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="jimpark; markgal"/>


# Opzioni di ridondanza di archiviazione per Backup di Azure

Le esigenze dell'azienda determineranno l'opzione di ridondanza di archiviazione appropriata per l'archiviazione di Backup di Azure. Se si usa Azure come endpoint primario di archiviazione dei backup (se ad esempio si esegue il backup in Azure da Windows Server), è consigliabile scegliere l'opzione predefinita di archiviazione con ridondanza geografica. Se si usa Azure come endpoint terziario di archiviazione dei backup (se ad esempio si usa SCDPM per avere una copia locale del backup e si usa Azure per la conservazione a lungo termine), è consigliabile scegliere l'archiviazione con ridondanza locale.

## Archiviazione con ridondanza geografica (GRS)

Con l'archiviazione con ridondanza geografica vengono conservate sei copie dei dati. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte in un'area secondaria situata a centinaia di chilometri di distanza dall'area primaria, fornendo il massimo livello di durabilità. In caso di errore nell'area primaria, grazie all'archiviazione con ridondanza geografica, Backup di Azure assicura la durabilità dei dati in due aree distinte.

## Archiviazione con ridondanza locale (LRS)

Con l'archiviazione con ridondanza locale vengono conservate tre copie dei dati. Tale tipo di archiviazione viene replicato per tre volte all'interno di una singola struttura di una singola area. Questo tipo di archiviazione protegge i dati dai comuni errori hardware, ma non dagli errori di un'intera struttura Azure. In questo modo vengono ridotti i costi di archiviazione dei dati in Azure e viene offerta una durabilità dei dati inferiore che può essere accettabile per le copie terziarie.

È possibile selezionare l'opzione appropriata in base alle proprie esigenze dall'opzione **Configura** dell'insieme di credenziali per il backup.

## Passaggi successivi

- Assicurarsi che l'ambiente sia [pronto per il backup di un computer client o Windows Server](backup-configure-vault.md)
- In caso di domande, vedere [Domande frequenti su Backup di Azure](backup-azure-backup-faq.md).
- Visitare il [forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0211_2016-->