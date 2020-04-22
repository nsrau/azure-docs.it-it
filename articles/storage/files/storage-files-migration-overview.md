---
title: Eseguire la migrazione a condivisioni file di Azure
description: Informazioni sulle migrazioni alle condivisioni file di Azure e nella guida alla migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685980"
---
# <a name="migrate-to-azure-file-shares"></a>Eseguire la migrazione a condivisioni file di Azure

Questo articolo illustra gli aspetti di base di una migrazione alle condivisioni file di Azure.This article covers the basic aspects of a migration to Azure file shares.

Questo articolo contiene le nozioni di base sulla migrazione e una tabella di guide alla migrazione. Queste guide consentono di spostare i file in condivisioni file di Azure.These guides help you move your files into Azure file shares. Le guide sono organizzate in base alla posizione dei dati e al modello di distribuzione (solo cloud o ibrido) in cui ci si sta spostando.

## <a name="migration-basics"></a>Nozioni di base sulla migrazioneMigration basics

Azure include più tipi di archiviazione cloud disponibili. Un aspetto fondamentale delle migrazioni di file in Azure è determinare l'opzione di archiviazione di Azure più appropriata per i dati.

[Le condivisioni file](storage-files-introduction.md) di Azure sono adatte per i dati dei file generici. Questi dati includono tutto ciò per cui si usa una condivisione SMB o NFS locale. Con [Sincronizzazione file](storage-sync-files-planning.md)di Azure è possibile memorizzare nella cache il contenuto di diverse condivisioni file di Azure nei server che eseguono Windows Server locale.

Per un'app attualmente in esecuzione in un server locale, l'archiviazione di file in una condivisione file di Azure potrebbe essere una buona scelta. È possibile spostare l'app in Azure e usare le condivisioni file di Azure come archiviazione condivisa. È anche possibile prendere in considerazione [dischi](../../virtual-machines/windows/managed-disks-overview.md) di Azure per questo scenario.

Alcune app cloud non dipendono da SMB o dall'accesso ai dati locali del computer o dall'accesso condiviso. Per queste app, l'archiviazione degli oggetti come [i BLOB](../blobs/storage-blobs-overview.md) di Azure è spesso la scelta migliore.

The key in any migration is to capture all the applicable file fidelity when moving your files from their current storage location to Azure. La fedeltà supportata dall'opzione di archiviazione di Azure e la quantità necessaria per lo scenario consentono di scegliere l'archiviazione di Azure corretta. I dati generali dei file dipendono tradizionalmente dai metadati dei file. I dati dell'app potrebbero non esserlo.

Di seguito sono riportati i due componenti di base di un file:

- **Flusso di dati**: Il flusso di dati di un file archivia il contenuto del file.
- **Metadati del**file : I metadati del file hanno i seguenti sottocomponenti:
   * Attributi di file come di sola lettura
   * Autorizzazioni per i file, che possono essere definite *autorizzazioni NTFS* o ACL di file *e cartelle*
   * Timestamp, in particolare la creazione e i timestamp dell'ultima modifica
   * Un flusso di dati alternativo, ovvero uno spazio per archiviare grandi quantità di proprietà non standard

La fedeltà dei file in una migrazione può essere definita come la capacità di:File fidelity in a migration can be defined as the ability to:

- Archiviare tutte le informazioni sui file applicabili nell'origine.
- Trasferire i file con lo strumento di migrazione.
- Archiviare i file nell'archivio di destinazione della migrazione.

Per garantire che la migrazione proceda senza problemi, identificare lo strumento di [copia migliore per le proprie esigenze](#migration-toolbox) e associare una destinazione di archiviazione all'origine.

Tenendo conto delle informazioni precedenti, è possibile vedere che l'archiviazione di destinazione per i file generici in Azure è [condivisioni file](storage-files-introduction.md)di Azure.Taking the previous information in account, you can see that the target storage for general-purpose files in Azure is Azure file shares .

A differenza dell'archiviazione di oggetti nei BLOB di Azure, una condivisione file di Azure può archiviare in modo nativo i metadati dei file. Le condivisioni file di Azure mantengono inoltre la gerarchia, gli attributi e le autorizzazioni di file e cartelle. Le autorizzazioni NTFS possono essere archiviate in file e cartelle perché sono locali.

Un utente di Active Directory, ovvero il controller di dominio locale, può accedere in modo nativo a una condivisione file di Azure.A user of Active Directory, which is their on-premises domain controller, can natively access an Azure file share. Così può un utente di Servizi di dominio Azure Active Directory (Azure AD DS). Ognuno utilizza la propria identità corrente per ottenere l'accesso in base alle autorizzazioni di condivisione e agli ACL di file e cartelle. Questo comportamento è simile a quello di un utente che si connette a una condivisione file locale.

Il flusso di dati alternativo è l'aspetto principale della fedeltà dei file che attualmente non può essere archiviato in un file in una condivisione file di Azure.The alternative data stream is the primary aspect of file fidelity that currently can't be stored on a file in an Azure file share. Viene mantenuto in locale quando viene usata sincronizzazione file di Azure.It's preserved on-premises when Azure File Sync is used.

Altre informazioni [sull'autenticazione](storage-files-identity-auth-active-directory-enable.md) di Azure AD e [sull'autenticazione](storage-files-identity-auth-active-directory-domain-service-enable.md) di Servizi di dominio Active Directory per le condivisioni file di Azure.Learn more about Azure AD authentication and Azure AD DS authentication for Azure file shares.

## <a name="migration-guides"></a>Guide alla migrazione

Nella tabella seguente sono elencate guide dettagliate sulla migrazione.

Come utilizzare la tabella:

1. Individuare la riga per il sistema di origine in cui sono attualmente archiviati i file.

1. Scegli una di queste destinazioni:

   - Una distribuzione ibrida che usa Sincronizzazione file di Azure per memorizzare nella cache il contenuto delle condivisioni file di Azure in locale
   - Condivisioni file di Azure nel cloudAzure file shares in the cloud

   Selezionare la colonna di destinazione che corrisponde a quella scelta.

1. All'interno dell'intersezione tra origine e destinazione, una cella di tabella elenca gli scenari di migrazione disponibili. Selezionarne uno per collegarne direttamente la guida dettagliata alla migrazione.

Uno scenario senza un collegamento non dispone ancora di una guida alla migrazione pubblicata. Controllare questa tabella occasionalmente per gli aggiornamenti. Le nuove guide verranno pubblicate quando saranno disponibili.

| Source (Sorgente) | Destinazione: </br>Distribuzione ibrida | Destinazione: </br>Distribuzione solo cloud |
|:---|:--|:--|
| | Combinazione di utensili:| Combinazione di utensili: |
| Windows Server 2012 R2 e versioni successive | <ul><li>[Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync and Azure Data Box](storage-sync-offline-data-transfer.md)</li><li>Servizio di migrazione di archiviazione e sincronizzazione file di AzureAzure File Sync and Storage Migration Service</li></ul> | <ul><li>Sincronizzazione file di Azure</li><li>Casella di dati e sincronizzazione file di AzureAzure File Sync and Data Box</li><li>Servizio di migrazione di archiviazione e sincronizzazione file di AzureAzure File Sync and Storage Migration Service</li><li>Robocopy</li></ul> |
| Windows Server 2012 e versioni precedenti | <ul><li>Casella di dati e sincronizzazione file di AzureAzure File Sync and Data Box</li><li>Servizio di migrazione di archiviazione e sincronizzazione file di AzureAzure File Sync and Storage Migration Service</li></ul> | <ul><li>Servizio di migrazione di archiviazione e sincronizzazione file di AzureAzure File Sync and Storage Migration Service</li><li>Robocopy</li></ul> |
| Archiviazione collegata alla rete (NAS) | <ul><li>[Azure File Sync and RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux o Samba | <ul><li>[Azure File Sync and RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 o StorSimple Cloud Appliance 8600 | <ul><li>[Sincronizzazione file di Azure e StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Sincronizzazione file di Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Strumenti di migrazione

### <a name="file-copy-tools"></a>Strumenti di copia dei file

Ci sono diversi strumenti di copia di file disponibili da Microsoft e altri. Per selezionare lo strumento corretto per lo scenario di migrazione, è necessario considerare le domande fondamentali seguenti:To select the right tool for your migration scenario, you must consider these fundamental questions:

* Lo strumento supporta i percorsi di origine e di destinazione per la copia dei file?

* Lo strumento supporta il percorso di rete o i protocolli disponibili (ad esempio REST, SMB o NFS) tra i percorsi di archiviazione di origine e di destinazione?

* Lo strumento mantiene la fedeltà dei file necessaria supportata dai percorsi di origine e di destinazione?

    In alcuni casi, l'archiviazione di destinazione non supporta la stessa fedeltà dell'origine. Se l'archiviazione di destinazione è sufficiente per le proprie esigenze, lo strumento deve corrispondere solo alle funzionalità di fedeltà dei file della destinazione.

* Lo strumento dispone di funzionalità che consentono di adattarlo alla strategia di migrazione?

    Ad esempio, valutare se lo strumento consente di ridurre al minimo i tempi di inattività.
    
    Quando uno strumento supporta un'opzione per eseguire il mirroring di un'origine in una destinazione, è spesso possibile eseguirlo più volte nella stessa origine e destinazione mentre l'origine rimane accessibile.

    La prima volta che si esegue lo strumento, viene copiata la maggior parte dei dati. Questa esecuzione iniziale potrebbe durare un po'. Spesso dura più a lungo di quanto si desidera per portare l'origine dati non in linea per i processi aziendali.

    Eseguendo il mirroring di un'origine in una destinazione (come con **robocopy /MIR**), è possibile eseguire nuovamente lo strumento sulla stessa origine e destinazione. L'esecuzione è molto più veloce perché deve trasportare solo le modifiche di origine che si verificano dopo l'esecuzione precedente. La riesecuzione di uno strumento di copia in questo modo può ridurre significativamente i tempi di inattività.

La tabella seguente classifica gli strumenti Microsoft e la relativa idoneità corrente per le condivisioni file di Azure:The following table classifies Microsoft tools and their current suitability for Azure file shares:

| Consigliato | Strumento | Supporto per le condivisioni file di AzureSupport for Azure file shares | Conservazione della fedeltà dei file |
| :-: | :-- | :---- | :---- |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Supportato. Le condivisioni file di Azure possono essere montate come unità di rete. | Piena fedeltà. |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| Sincronizzazione file di Azure | Integrato in modo nativo nelle condivisioni file di Azure.Natively integrated into Azure file shares. | Piena fedeltà. |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| Servizio di migrazione della risorsa di archiviazione | Supportato indirettamente. Le condivisioni file di Azure possono essere montate come unità di rete nei server di destinazione di SMS. | Piena fedeltà. |
|![Non completamente raccomandato](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | Supportato. | Non copia i metadati. [Data Box può essere usato con Sincronizzazione file](storage-sync-offline-data-transfer.md)di Azure. |
|![Non consigliata](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Supportato. | Non copia i metadati. |
|![Non consigliata](media/storage-files-migration-overview/circle-red-x.png)| Esplora archivi Azure | Supportato. | Non copia i metadati. |
|![Non consigliata](media/storage-files-migration-overview/circle-red-x.png)| Data factory di Azure | Supportato. | Non copia i metadati. |
|||||

*\*Fedeltà completa: soddisfa o supera le funzionalità di condivisione file di Azure.Full fidelity: meets or exceeds Azure file-share capabilities.*

### <a name="migration-helper-tools"></a>Strumenti helper per la migrazione

In questa sezione vengono descritti gli strumenti che consentono di pianificare ed eseguire le migrazioni.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy di Microsoft Corporation

RoboCopy è uno degli strumenti più applicabili alle migrazioni di file. Viene fornito come parte di Windows. La documentazione principale di [RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) è una risorsa utile per le numerose opzioni di questo strumento.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize da JAM Software GmbH

Sincronizzazione file di Azure viene ridimensionato principalmente in base al numero di elementi (file e cartelle) e non con la quantità di spazio di archiviazione totale. Lo strumento TreeSize consente di determinare il numero di elementi nei volumi di Windows Server.

È possibile usare lo strumento per creare una prospettiva prima di una distribuzione di [Sincronizzazione file](storage-sync-files-deployment-guide.md)di Azure.You can use the tool to create a perspective before an Azure File Sync deployment . È anche possibile usarlo quando la suddivisione in livelli cloud è impegnata dopo la distribuzione. In questo scenario, viene visualizzato il numero di elementi e le directory che utilizzano maggiormente la cache del server.

La versione testata dello strumento è la versione 4.4.1. È compatibile con i file a livello cloud. Lo strumento non causerà il richiamo dei file a livelli durante il normale funzionamento.

## <a name="next-steps"></a>Passaggi successivi

1. Creare un piano per il quale la distribuzione delle condivisioni file di Azure (solo cloud o ibrida) si vuole.
1. Esaminare l'elenco delle guide alla migrazione disponibili per trovare la guida dettagliata corrispondente all'origine e alla distribuzione delle condivisioni file di Azure.Review the list of available migration guides to find the detailed guide that matches your source and deployment of Azure file shares.

Ecco altre informazioni sulle tecnologie File di Azure menzionate in questo articolo:Here is more information about the Azure Files technologies mentioned in this article:

* [Panoramica della condivisione file di AzureAzure file share overview](storage-files-introduction.md)
* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
* [Sincronizzazione file di Azure: livelli cloudAzure File Sync: Cloud tiering](storage-sync-cloud-tiering.md)
