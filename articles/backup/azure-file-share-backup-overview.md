---
title: Informazioni sul backup della condivisione file di Azure
description: Informazioni su come eseguire il backup delle condivisioni file di Azure nell'insieme di credenziali dei servizi di ripristino
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: fd5bb51b2c7b5c09e9d859b69c3094eb50c205b5
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396272"
---
# <a name="about-azure-file-share-backup"></a>Informazioni sul backup della condivisione file di Azure

Il backup della condivisione file di Azure è una soluzione di backup nativa basata su cloud che protegge i dati nel cloud ed elimina ulteriori sovraccarichi di manutenzione delle soluzioni di backup locali. Il servizio Backup di Azure si integra perfettamente con la sincronizzazione dei file di Azure e consente di centralizzare i dati di condivisione file e i backup. Questa soluzione semplice, affidabile e sicura consente di configurare la protezione per le condivisioni file aziendali in pochi semplici passaggi con la certezza di poter ripristinare i dati in caso di emergenza.

## <a name="key-benefits-of-azure-file-share-backup"></a>Vantaggi principali del backup della condivisione file di AzureKey benefits of Azure file share backup

* Nessuna infrastruttura: non è necessaria alcuna distribuzione per configurare la protezione per le condivisioni file.
* Funzionalità di gestione integrate: è possibile pianificare i backup e specificare il periodo di conservazione desiderato senza l'overhead aggiuntivo dell'eliminazione dei dati.
* Ripristino immediato: il backup della condivisione file di Azure usa snapshot di condivisione file, pertanto è possibile selezionare immediatamente solo i file da ripristinare immediatamente.
* Avvisi e report: è possibile configurare avvisi per gli errori di backup e ripristino e usare la soluzione per la creazione di report fornita da Backup di Azure per ottenere informazioni dettagliate sui backup nelle condivisioni file.

## <a name="architecture"></a>Architecture

![Architettura di backup della condivisione file di AzureAzure file share backup architecture](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Funzionamento del processo di backup

1. Il primo passaggio nella configurazione del backup per le condivisioni file di Azure consiste nella creazione di un insieme di credenziali dei servizi di ripristino. L'insieme di credenziali offre una visualizzazione consolidata dei backup configurati in diversi carichi di lavoro.

2. Dopo aver creato un insieme di credenziali, il servizio Backup di Azure individua gli account di archiviazione che possono essere registrati con l'insieme di credenziali. È possibile selezionare l'account di archiviazione che ospita le condivisioni file da proteggere.

3. Dopo aver selezionato l'account di archiviazione, il servizio Backup di Azure elenca il set di condivisioni file presenti nell'account di archiviazione e archivia i nomi nel catalogo del livello di gestione.

4. È quindi possibile configurare i criteri di backup (pianificazione e conservazione) in base alle proprie esigenze e selezionare le condivisioni file di cui eseguire il backup. Il servizio Backup di Azure registra le pianificazioni nel piano di controllo per eseguire backup pianificati.

5. In base ai criteri specificati, l'utilità di pianificazione di Backup di Azure attiva i backup all'ora pianificata. Come parte di tale processo, lo snapshot di condivisione file viene creato utilizzando l'API di condivisione File. Solo l'URL dello snapshot viene archiviato nell'archivio dei metadati.

    >[!NOTE]
    >I dati della condivisione file non vengono trasferiti al servizio Backup, poiché il servizio Backup crea e gestisce snapshot che fanno parte dell'account di archiviazione.

6. È possibile ripristinare il contenuto della condivisione file di Azure (singoli file o la condivisione completa) dagli snapshot disponibili nella condivisione file di origine. Una volta attivata l'operazione, l'URL dello snapshot viene recuperato dall'archivio dei metadati e i dati vengono elencati e trasferiti dallo snapshot di origine alla condivisione file di destinazione desiderata.

7. I dati di monitoraggio dei processi di backup e ripristino vengono inviati al servizio monitoraggio di Backup di Azure.The backup and restore job monitoring data is pushed to the Azure Backup Monitoring service. In questo modo è possibile monitorare i backup cloud per le condivisioni file in un singolo dashboard. Inoltre, è anche possibile configurare avvisi o notifiche tramite posta elettronica quando l'integrità del backup è interessata. I messaggi di posta elettronica vengono inviati tramite il servizio di posta elettronica di Azure.Emails are sent via the Azure email service.

## <a name="backup-costs"></a>Costi di backup

Il backup della condivisione file di Azure è una soluzione basata su snapshot e gli addebiti di archiviazione sostenuti per gli snapshot vengono fatturati insieme a Utilizzo file di Azure in base ai dettagli sui prezzi indicati [di seguito.](https://azure.microsoft.com/pricing/details/storage/files/)

Tuttavia, la tariffa dell'istanza protetta per sfruttare la soluzione di backup è in base al modello di determinazione dei prezzi descritto nella sezione Backup per i file di Azure.However, the protected instance fee for leveraging the backup solution is according to the pricing model described under the [Backup For Azure Files](https://azure.microsoft.com/pricing/details/backup/) section. Attualmente, il prezzo effettivo è stato aggiornato solo per gli Stati Uniti centro-occidentali. Per altre regioni, i prezzi esatti verranno aggiornati presto con alcune variazioni regionali, ma utilizzando lo stesso modello di determinazione dei prezzi.

>[!NOTE]
>Durante l'anteprima, non vi è alcuna "tassa istanza protetta" e vi verrà addebitato solo per le istantanee in base al prezzo menzionato [qui](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il backup delle [condivisioni file di AzureLearn](backup-afs.md) how to Back up Azure file shares
* Risposte alle [domande sul backup dei file di AzureFind](backup-azure-files-faq.md) answers to Questions about backing up Azure Files
