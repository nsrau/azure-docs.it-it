---
title: Eseguire la migrazione a condivisioni file di AzureMigrate to Azure file shares
description: Informazioni sulle migrazioni alle condivisioni file di Azure e nella guida alla migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247317"
---
# <a name="migrate-to-azure-file-shares"></a>Eseguire la migrazione a condivisioni file di AzureMigrate to Azure file shares

Questo articolo illustra gli aspetti di base di una migrazione alle condivisioni file di Azure.This article covers the basic aspects of a migration to Azure file shares.

Oltre alle nozioni di base sulla migrazione, questo articolo contiene un elenco di guide alla migrazione esistenti e individualizzate. Queste guide alla migrazione consentono di spostare i file in condivisioni file di Azure e sono organizzate in base alla posizione dei dati di oggi e al modello di distribuzione (solo cloud o ibrido) in cui si prevede di passare.

## <a name="migration-basics"></a>Nozioni di base sulla migrazioneMigration basics

In Azure sono disponibili più tipi diversi di archiviazione cloud. Un aspetto fondamentale della migrazione dei file in Azure consiste nel determinare l'opzione di archiviazione di Azure più appropriata per i dati.

Le condivisioni file di Azure sono ideali per i dati dei file generici. Davvero tutto ciò per cui si usa una condivisione SMB o NFS locale. Con [Sincronizzazione file](storage-sync-files-planning.md)di Azure è possibile memorizzare facoltativamente nella cache il contenuto di diverse condivisioni file di Azure in più server Windows in locale.

Se si dispone di un'applicazione attualmente in esecuzione in un server locale, l'archiviazione dei file nelle condivisioni file di Azure può essere più a punsi, a seconda dell'applicazione. È possibile sollevare l'applicazione per l'esecuzione in Azure e usare le condivisioni file di Azure come archiviazione condivisa. È anche possibile prendere in considerazione [dischi](../../virtual-machines/windows/managed-disks-overview.md) di Azure per questo scenario. Per le applicazioni in formato cloud che non dipendono dall'accesso SMB o locale al computer ai dati o all'accesso condiviso, l'archiviazione degli oggetti, ad esempio i BLOB di [Azure,](../blobs/storage-blobs-overview.md)è spesso la scelta migliore.

The key in any migration is to capture all the applicable file fidelity when migrating your files from their current storage location to Azure. Un aiuto per scegliere l'archiviazione di Azure corretta è anche l'aspetto della fedeltà supportata dall'opzione di archiviazione di Azure ed è richiesta dallo scenario. I dati dei file generici dipendono tradizionalmente dai metadati dei file. I dati dell'applicazione potrebbero non esserlo. Esistono due componenti di base per un file:

- **Flusso di dati**: Il flusso di dati di un file archivia il contenuto del file.
- **Metadati del**file : I metadati dei file hanno diversi sottocomponenti:
   * Attributi del file: di sola lettura, ad esempio.
   * Autorizzazioni file: denominate *autorizzazioni NTFS* o ACL di *file e cartelle*.
   * Timestamp: in particolare i timestamp *di creazione* e *ultima modifica.*
   * Flusso di dati alternativo: uno spazio per archiviare grandi quantità di proprietà non standard.

La fedeltà dei file, in una migrazione, può quindi essere definita come la possibilità di archiviare tutte le informazioni sui file applicabili nell'origine, la possibilità di trasferirli con lo strumento di migrazione e la possibilità di archiviarli nell'archivio di destinazione della migrazione.

Per garantire che la migrazione proceda nel modo più semplice possibile, identificare lo strumento di [copia migliore per le proprie esigenze](#migration-toolbox) e associare una destinazione di archiviazione all'origine.

Tenendo conto delle informazioni precedenti, diventa chiaro quale sia l'archiviazione di destinazione per i file di uso generale in Azure: [condivisioni file](storage-files-introduction.md)di Azure. Rispetto all'archiviazione di oggetti nei BLOB di Azure, i metadati dei file possono essere archiviati in modo nativo nei file in una condivisione file di Azure.Compared to object storage in Azure blobs, file metadata can be natively stored on files in an Azure file share.

Le condivisioni file di Azure mantengono anche la gerarchia di file e cartelle. Inoltre:
* Le autorizzazioni NTFS possono essere archiviate in file e cartelle così come sono locali.
* Gli utenti di Active Directory (o gli utenti di Servizi di dominio Active Directory) possono accedere in modo nativo a una condivisione file di Azure.AD users (or Azure AD DS users) can natively access an Azure file share. 
    Usano la loro identità attuale e ottengono l'accesso in base alle autorizzazioni di condivisione, nonché agli ACL di file e cartelle. Comportamento non diverso dal momento in cui gli utenti si connettono a una condivisione file locale.
*  Il flusso di dati alternativo è l'aspetto principale della fedeltà dei file che attualmente non può essere archiviato in un file in una condivisione file di Azure.The alternative data stream is the primary aspect of file fidelity that currently cannot be stored on a file in an Azure file share.
   Viene mantenuto in locale quando è coinvolta sincronizzazione file di Azure.It is preserved on-premises when Azure File Sync is involved.

* [Altre informazioni sull'autenticazione di Active Directory per le condivisioni file di AzureLearn more about AD authentication for Azure file shares](storage-files-identity-auth-active-directory-enable.md)
* [Altre informazioni sull'autenticazione di Servizi di dominio Azure Active Directory (AAD DS) per le condivisioni file di AzureLearn more about Azure Active Directory Domain Services (AAD DS) authentication for Azure file shares](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Guide alla migrazione

Nella tabella seguente sono elencate guide dettagliate sulla migrazione.

Navigare da:
1. Individuare la riga per il sistema di origine in cui sono attualmente archiviati i file.
2. Decidere se si usa una distribuzione ibrida in cui si usa Sincronizzazione file di Azure per memorizzare nella cache il contenuto di una o più condivisioni file di Azure in locale o se si vuole usare le condivisioni file di Azure direttamente nel cloud. Selezionare la colonna di destinazione che riflette la decisione.
3. All'interno dell'intersezione tra origine e destinazione, una cella di tabella elenca gli scenari di migrazione disponibili. Selezionare uno di essi per collegarsi direttamente alla guida dettagliata alla migrazione.

Uno scenario senza un collegamento non dispone ancora di una guida alla migrazione pubblicata. Controllare questa tabella occasionalmente per gli aggiornamenti. Nuove guide saranno pubblicate quando disponibili.

| Source (Sorgente) | Destinazione: </br>Distribuzione ibrida | Destinazione: </br>Distribuzione solo cloud |
|:---|:--|:--|
| | Combinazione di utensili:| Combinazione di utensili: |
| Windows Server 2012 R2 e versioni successive | <ul><li>[Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)</li><li>[Sincronizzazione file di Azure - DataBox](storage-sync-offline-data-transfer.md)</li><li>Servizio di migrazione archiviazione - Sincronizzazione file di AzureStorage Migration Service - Azure File Sync</li></ul> | <ul><li>Sincronizzazione file di Azure</li><li>Sincronizzazione file di Azure - DataBox</li><li>Servizio di migrazione archiviazione - Sincronizzazione file di AzureStorage Migration Service - Azure File Sync</li><li>Robocopy</li></ul> |
| Windows Server 2012 e versioni precedenti | <ul><li>Sincronizzazione file di Azure - DataBox</li><li>Servizio di migrazione archiviazione - Sincronizzazione file di AzureStorage Migration Service - Azure File Sync</li></ul> | <ul><li>Servizio di migrazione archiviazione - Sincronizzazione file di AzureStorage Migration Service - Azure File Sync</li><li>Robocopy</li></ul> |
| NAS (Network Attached Storage) | <ul><li>[Sincronizzazione file di Azure - RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux / Samba | <ul><li>[RoboCopy - Sincronizzazione file di Azure](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| StorSimple 8100 / 8600 | <ul><li>[Sincronizzazione file di Azure - 8020 Appliance virtuale](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 1200 | <ul><li>[Sincronizzazione file di Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Strumenti di migrazione

### <a name="file-copy-tools"></a>Strumenti di copia dei file

Sono disponibili diversi strumenti di copia dei file Microsoft e non Microsoft. Per selezionare lo strumento di copia dei file corretto per lo scenario di migrazione, è necessario considerare tre domande fondamentali:

* Lo strumento di copia supporta l'origine e il percorso di destinazione per una determinata copia di file? 
    * Supporta il percorso di rete e/o i protocolli disponibili (ad esempio REST/SMB/NFS) da e verso i percorsi di archiviazione di origine e di destinazione?
* Lo strumento di copia mantiene la fedeltà dei file necessaria supportata dal percorso di origine/destinazione? In alcuni casi, l'archiviazione di destinazione non supporta la stessa fedeltà dell'origine. È già stata presa la decisione che l'archiviazione di destinazione è sufficiente per le proprie esigenze, pertanto lo strumento di copia deve solo corrispondere alle funzionalità di fedeltà dei file di destinazione.
* Lo strumento di copia dispone di feature che lo adattano alla mia strategia di migrazione? 
    * Si consideri, ad esempio, se dispone di opzioni che consentono di ridurre al minimo i tempi di inattività. Una buona domanda da porsi è: Posso eseguire questa copia più volte sullo stesso, dagli utenti a cui si accede attivamente posizione? In tal caso, è possibile ridurre in modo significativo il tempo di inattività. Confrontarlo con una situazione in cui è possibile avviare la copia solo quando l'origine smette di cambiare, per garantire una copia completa.

La tabella seguente classifica gli strumenti Microsoft e la relativa idoneità corrente per le condivisioni file di Azure:The following table classifies Microsoft tools and their current suitability for Azure file shares:

| Consigliato | Strumento | Supporta le condivisioni file di AzureSupports Azure file shares | Mantiene la fedeltà dei file |
| :-: | :-- | :---- | :---- |
|![Sì, consigliato.](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Supportato. Le condivisioni file di Azure possono essere montate come unità di rete. | Piena fedeltà |
|![Sì, consigliato.](media/storage-files-migration-overview/circle-green-checkmark.png)| Sincronizzazione file di Azure | Integrato in modo nativo nelle condivisioni file di Azure.Natively integrated into Azure file shares. | Piena fedeltà |
|![Sì, consigliato.](media/storage-files-migration-overview/circle-green-checkmark.png)| Servizio di migrazione archiviazione (SMS) | Supportato indirettamente. Le condivisioni file di Azure possono essere montate come unità di rete in un server di destinazione SMS. | Piena fedeltà |
|![Non completamente consigliato.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| DataBox di AzureAzure DataBox | Supportato. | Non copia i metadati. [Può essere usato in combinazione con Sincronizzazione file](storage-sync-offline-data-transfer.md)di Azure. |
|![Non consigliata.](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Supportato. | Non copia i metadati. |
|![Non consigliata.](media/storage-files-migration-overview/circle-red-x.png)| Esplora archivi Azure | Supportato. | Non copia i metadati. |
|![Non consigliata.](media/storage-files-migration-overview/circle-red-x.png)| Data factory di Azure | Supportato. | Non copia i metadati. |
|||||

*\*Fedeltà completa: soddisfa o supera le funzionalità di condivisione file di Azure.Full fidelity: meets or exceeds Azure file share capabilities.*

### <a name="migration-helper-tools"></a>Strumenti helper per la migrazione

In questa sezione sono elencati gli strumenti che consentono di pianificare ed eseguire le migrazioni.

* **RoboCopy, di Microsoft Corporation**

    Uno degli strumenti di copia più applicabili alle migrazioni di file, viene fornito come parte di Microsoft Windows. A causa delle numerose opzioni in questo strumento, la documentazione principale di [RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) è una fonte utile.

* **TreeSize, da JAM Software GmbH**

    Sincronizzazione file di Azure viene ridimensionato principalmente in base al numero di elementi (file e cartelle) e meno con l'importo TiB totale. Lo strumento può essere utilizzato per determinare il numero di file e cartelle nei volumi di Windows Server. Inoltre può essere usato per creare una prospettiva prima di una distribuzione di [Sincronizzazione file](storage-sync-files-deployment-guide.md) di Azure, ma anche dopo, quando è impegnata la suddivisione in livelli cloud e ti piace vedere non solo il numero di elementi, ma anche in quali directory viene usata di più la cache del server.
    Questo strumento (versione testata 4.4.1) è compatibile con i file a livelli cloud. Non causerà il richiamo dei file a livelli durante il suo normale funzionamento.


## <a name="next-steps"></a>Passaggi successivi

1. Creare un piano per il quale la distribuzione delle condivisioni file di Azure (solo cloud o ibrida) per cui ci si sforza.
2. Esaminare l'elenco delle guide alla migrazione disponibili per trovare la guida dettagliata corrispondente all'origine e alla distribuzione delle condivisioni file di Azure.Review the list of available migration guides to find the detailed guide that matches your source and deployment of Azure file shares.

Sono disponibili altre informazioni sulle tecnologie File di Azure menzionate in questo articolo:There is more information available about the Azure Files technologies mentioned in this article:

* [Panoramica della condivisione file di AzureAzure file share overview](storage-files-introduction.md)
* [Pianificazione di una distribuzione di Sincronizzazione file di AzurePlanning for an Azure File Sync deployment](storage-sync-files-planning.md)
* [Sincronizzazione file di Azure: livelli cloudAzure File Sync: Cloud tiering](storage-sync-cloud-tiering.md)
