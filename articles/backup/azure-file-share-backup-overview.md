---
title: Informazioni sul backup di condivisioni file di Azure
description: Informazioni su come eseguire il backup delle condivisioni file di Azure nell'insieme di credenziali di servizi di ripristino
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 7960040df4208ad021bca3406f6472d8c3206d81
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892253"
---
# <a name="about-azure-file-share-backup"></a>Informazioni sul backup di condivisioni file di Azure

Il backup di condivisioni file di Azure è una soluzione di backup nativa basata sul cloud che protegge i dati nel cloud ed Elimina sovraccarichi di manutenzione aggiuntivi che coinvolgono le soluzioni di backup locali. Il servizio backup di Azure si integra perfettamente con Sincronizzazione file di Azure e consente di centralizzare i dati della condivisione file e i backup. Questa soluzione semplice, affidabile e sicura consente di configurare la protezione per le condivisioni file aziendali in pochi semplici passaggi con la garanzia che è possibile ripristinare i dati in qualsiasi scenario di emergenza.

## <a name="key-benefits-of-azure-file-share-backup"></a>Vantaggi principali del backup di condivisioni file di Azure

* **Infrastruttura zero**: non è necessaria alcuna distribuzione per configurare la protezione per le condivisioni file.
* **Conservazione personalizzata**: è possibile configurare i backup con conservazione giornaliera/settimanale/mensile/annuale in base ai requisiti.
* **Funzionalità di gestione predefinite**: è possibile pianificare i backup e specificare il periodo di memorizzazione desiderato senza l'overhead aggiuntivo di eliminazione dei dati.
* **Ripristino istantaneo**: il backup di condivisioni file di Azure usa snapshot di condivisione file, pertanto è possibile selezionare solo i file che si desidera ripristinare immediatamente.
* Avvisi **e report**: è possibile configurare avvisi per gli errori di backup e ripristino e usare la soluzione di creazione di report fornita da backup di Azure per ottenere informazioni dettagliate sui backup nelle condivisioni file.
* **Protezione da eliminazioni accidentali delle condivisioni file**: backup di Azure consente la [funzionalità di eliminazione](../storage/files/storage-files-prevent-file-share-deletion.md) temporanea a livello di account di archiviazione con un periodo di memorizzazione di 14 giorni. Anche se un attore malintenzionato Elimina la condivisione file, il contenuto e i punti di ripristino della condivisione file (snapshot) vengono conservati per un periodo di conservazione configurabile, consentendo il ripristino corretto e completo del contenuto di origine e degli snapshot senza perdita di dati.

## <a name="architecture"></a>Architecture

![Architettura di backup di condivisione file di Azure](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Funzionamento del processo di backup

1. Il primo passaggio nella configurazione del backup per le condivisioni file di Azure consiste nel creare un insieme di credenziali di servizi di ripristino. L'insieme di credenziali offre una visualizzazione consolidata dei backup configurati in carichi di lavoro diversi.

2. Dopo aver creato un insieme di credenziali, il servizio backup di Azure rileva gli account di archiviazione che possono essere registrati con l'insieme di credenziali. È possibile selezionare l'account di archiviazione che ospita le condivisioni file che si vuole proteggere.

3. Dopo aver selezionato l'account di archiviazione, il servizio backup di Azure elenca il set di condivisioni file presenti nell'account di archiviazione e archivia i relativi nomi nel catalogo del livello di gestione.

4. È quindi possibile configurare i criteri di backup (pianificazione e conservazione) in base alle proprie esigenze e selezionare le condivisioni file di cui eseguire il backup. Il servizio backup di Azure registra le pianificazioni nel piano di controllo per eseguire backup pianificati.

5. In base ai criteri specificati, l'utilità di pianificazione di backup di Azure attiva i backup all'ora pianificata. Come parte di tale processo, lo snapshot di condivisione file viene creato usando l'API della condivisione file. Solo l'URL dello snapshot viene archiviato nell'archivio di metadati.

    >[!NOTE]
    >I dati della condivisione file non vengono trasferiti al servizio di backup, perché il servizio di backup crea e gestisce gli snapshot che fanno parte dell'account di archiviazione e i backup non vengono trasferiti nell'insieme di credenziali.

6. È possibile ripristinare il contenuto della condivisione file di Azure (singoli file o la condivisione completa) dagli snapshot disponibili nella condivisione file di origine. Una volta attivata l'operazione, l'URL dello snapshot viene recuperato dall'archivio di metadati e i dati vengono elencati e trasferiti dallo snapshot di origine alla condivisione file di destinazione di propria scelta.

7. Se si utilizza Sincronizzazione file di Azure, il servizio di backup indica al servizio Sincronizzazione file di Azure i percorsi dei file da ripristinare, che attiva quindi un processo di rilevamento delle modifiche in background su questi file. Tutti i file che sono stati modificati vengono sincronizzati con l'endpoint server. Questo processo avviene in parallelo con il ripristino originale per la condivisione file di Azure.

8. Viene eseguito il push dei dati di monitoraggio del processo di backup e ripristino nel servizio di monitoraggio di backup di Azure. In questo modo è possibile monitorare i backup cloud per le condivisioni file in un unico dashboard. Inoltre, è possibile configurare avvisi o notifiche tramite posta elettronica quando l'integrità del backup è interessata. I messaggi vengono inviati tramite il servizio di posta elettronica di Azure.

## <a name="backup-costs"></a>Costi di backup

Attualmente verranno addebitati solo gli snapshot, poiché il backup della condivisione file di Azure è una soluzione basata su snapshot. Gli addebiti per lo spazio di archiviazione per gli snapshot vengono fatturati insieme all'utilizzo File di Azure in base ai dettagli sui prezzi indicati [qui](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [Eseguire il backup di condivisioni file di Azure](backup-afs.md)
* Trovare le risposte alle [domande sul backup file di Azure](backup-azure-files-faq.md)
