---
title: Eseguire la migrazione a condivisioni file di Azure
description: Informazioni sulle migrazioni per le condivisioni file di Azure e su come trovare la guida alla migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685980"
---
# <a name="migrate-to-azure-file-shares"></a>Eseguire la migrazione a condivisioni file di Azure

Questo articolo descrive gli aspetti di base di una migrazione alle condivisioni file di Azure.

Questo articolo contiene le nozioni di base della migrazione e una tabella delle guide alla migrazione. Queste guide consentono di spostare i file in condivisioni file di Azure. Le guide sono organizzate in base alla posizione dei dati e al modello di distribuzione (solo cloud o ibrido) a cui si sta eseguendo il passaggio.

## <a name="migration-basics"></a>Nozioni fondamentali sulla migrazione

Azure dispone di più tipi disponibili di archiviazione cloud. Un aspetto fondamentale delle migrazioni di file in Azure è determinare l'opzione di archiviazione di Azure più adatta ai propri dati.

Le [condivisioni file di Azure](storage-files-introduction.md) sono adatte per i dati di file per utilizzo generico. Questi dati includono tutto ciò che si usa una condivisione SMB o NFS locale per. Con [sincronizzazione file di Azure](storage-sync-files-planning.md), è possibile memorizzare nella cache il contenuto di più condivisioni file di Azure in server che eseguono Windows Server in locale.

Per un'app che attualmente viene eseguita in un server locale, l'archiviazione dei file in una condivisione file di Azure può essere una scelta ottimale. È possibile spostare l'app in Azure e usare le condivisioni file di Azure come risorsa di archiviazione condivisa. È anche possibile prendere in considerazione i [dischi di Azure](../../virtual-machines/windows/managed-disks-overview.md) per questo scenario.

Alcune app Cloud non dipendono da SMB o dall'accesso ai dati locale del computer o dall'accesso condiviso. Per queste app, l'archiviazione di oggetti come i [BLOB di Azure](../blobs/storage-blobs-overview.md) è spesso la scelta migliore.

La chiave in ogni migrazione consiste nell'acquisire tutte le fedeltà dei file applicabili quando si spostano i file dal percorso di archiviazione corrente in Azure. La fedeltà supportata dall'opzione di archiviazione di Azure e il modo in cui lo scenario richiede consente anche di scegliere il sistema di archiviazione di Azure appropriato. I dati di file per utilizzo generico dipendono tradizionalmente dai metadati del file. I dati dell'app potrebbero non essere.

Ecco i due componenti di base di un file:

- **Flusso di dati**: il flusso di dati di un file archivia il contenuto del file.
- **Metadati del file**: i metadati del file hanno questi sottocomponenti:
   * Attributi di file come di sola lettura
   * Autorizzazioni per i file, che possono essere denominate *autorizzazioni NTFS* o *ACL di file e cartelle*
   * Timestamp, in particolare la creazione e i timestamp dell'Ultima modifica
   * Un flusso di dati alternativo, ovvero uno spazio per archiviare grandi quantità di proprietà non standard

La fedeltà dei file in una migrazione può essere definita come la possibilità di:

- Archiviare tutte le informazioni sui file applicabili nell'origine.
- Trasferire i file con lo strumento di migrazione.
- Archiviare i file nella risorsa di archiviazione di destinazione della migrazione.

Per assicurarsi che la migrazione proceda senza problemi, identificare [lo strumento di copia ottimale per le proprie esigenze](#migration-toolbox) e associare una destinazione di archiviazione all'origine.

Tenendo conto delle informazioni precedenti, è possibile osservare che la risorsa di archiviazione di destinazione per i file di uso generico in Azure è [condivisioni file di Azure](storage-files-introduction.md).

A differenza dell'archiviazione di oggetti nei BLOB di Azure, una condivisione file di Azure può archiviare in modo nativo i metadati del file. Le condivisioni file di Azure mantengono anche la gerarchia di file e cartelle, gli attributi e le autorizzazioni. Le autorizzazioni NTFS possono essere archiviate in file e cartelle perché si trovano in locale.

Un utente di Active Directory, ovvero il controller di dominio locale, può accedere in modo nativo a una condivisione file di Azure. Quindi, è possibile che un utente di Azure Active Directory Domain Services (Azure AD DS). Ogni utilizza l'identità corrente per ottenere l'accesso in base alle autorizzazioni di condivisione e agli ACL di file e cartelle. Questo comportamento è simile a un utente che si connette a una condivisione file locale.

Il flusso di dati alternativo è l'aspetto principale della fedeltà dei file che attualmente non può essere archiviato in un file in una condivisione file di Azure. Viene mantenuto in locale quando viene usato Sincronizzazione file di Azure.

Altre informazioni sull' [autenticazione Azure ad](storage-files-identity-auth-active-directory-enable.md) e sull' [autenticazione Azure AD DS](storage-files-identity-auth-active-directory-domain-service-enable.md) per le condivisioni file di Azure.

## <a name="migration-guides"></a>Guide alla migrazione

Nella tabella seguente sono elencate le guide dettagliate per la migrazione.

Come usare la tabella:

1. Individuare la riga del sistema di origine in cui sono attualmente archiviati i file.

1. Scegliere una delle destinazioni seguenti:

   - Una distribuzione ibrida che usa Sincronizzazione file di Azure per memorizzare nella cache il contenuto delle condivisioni file di Azure in locale
   - Condivisioni file di Azure nel cloud

   Selezionare la colonna di destinazione corrispondente a quella scelta.

1. All'interno dell'intersezione tra origine e destinazione, una cella della tabella elenca gli scenari di migrazione disponibili. Selezionarne uno per collegarsi direttamente alla guida dettagliata alla migrazione.

Uno scenario senza collegamento non dispone ancora di una guida alla migrazione pubblicata. Controllare occasionalmente questa tabella per gli aggiornamenti. Le nuove guide verranno pubblicate quando sono disponibili.

| Source (Sorgente) | Destinazione: </br>Distribuzione ibrida | Destinazione: </br>Distribuzione solo cloud |
|:---|:--|:--|
| | Combinazione di strumenti:| Combinazione di strumenti: |
| Windows Server 2012 R2 e versioni successive | <ul><li>[Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)</li><li>[Sincronizzazione file di Azure e Azure Data Box](storage-sync-offline-data-transfer.md)</li><li>Servizio migrazione Sincronizzazione file di Azure e archiviazione</li></ul> | <ul><li>Sincronizzazione file di Azure</li><li>Sincronizzazione file di Azure e Data Box</li><li>Servizio migrazione Sincronizzazione file di Azure e archiviazione</li><li>RoboCopy</li></ul> |
| Windows Server 2012 e versioni precedenti | <ul><li>Sincronizzazione file di Azure e Data Box</li><li>Servizio migrazione Sincronizzazione file di Azure e archiviazione</li></ul> | <ul><li>Servizio migrazione Sincronizzazione file di Azure e archiviazione</li><li>RoboCopy</li></ul> |
| Archiviazione collegata alla rete (NAS) | <ul><li>[Sincronizzazione file di Azure e RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux o Samba | <ul><li>[Sincronizzazione file di Azure e RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Appliance cloud Microsoft Azure StorSimple 8100 o Appliance cloud StorSimple 8600 | <ul><li>[Appliance cloud Sincronizzazione file di Azure e StorSimple 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| Appliance cloud StorSimple 1200 | <ul><li>[Sincronizzazione file di Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Casella degli strumenti migrazione

### <a name="file-copy-tools"></a>Strumenti per la copia di file

Sono disponibili diversi strumenti per la copia di file da Microsoft e da altri. Per selezionare lo strumento appropriato per lo scenario di migrazione, è necessario prendere in considerazione le domande fondamentali seguenti:

* Lo strumento supporta i percorsi di origine e di destinazione per la copia del file?

* Lo strumento supporta il percorso di rete o i protocolli disponibili (ad esempio REST, SMB o NFS) tra i percorsi di archiviazione di origine e di destinazione?

* Lo strumento mantiene la fedeltà dei file necessaria supportata dai percorsi di origine e di destinazione?

    In alcuni casi, l'archiviazione di destinazione non supporta la stessa fedeltà dell'origine. Se lo spazio di archiviazione di destinazione è sufficiente per le proprie esigenze, lo strumento deve corrispondere solo alle funzionalità di fedeltà dei file di destinazione.

* Lo strumento include funzionalità che consentono di adattarle alla strategia di migrazione?

    Si consideri, ad esempio, se lo strumento consente di ridurre al minimo il tempo di inattività.
    
    Quando uno strumento supporta un'opzione per il mirroring di un'origine a una destinazione, è spesso possibile eseguirlo più volte nella stessa origine e nella stessa destinazione mentre l'origine rimane accessibile.

    La prima volta che si esegue lo strumento, viene copiata la maggior parte dei dati. Questa esecuzione iniziale potrebbe durare un po'. Spesso dura più a lungo del necessario per portare l'origine dati offline per i processi aziendali.

    Eseguendo il mirroring di un'origine a una destinazione (come con **Robocopy/Mir**), è possibile eseguire nuovamente lo strumento sulla stessa origine e destinazione. L'esecuzione è molto più rapida perché è necessario trasportare solo le modifiche di origine che si verificano dopo l'esecuzione precedente. La riesecuzione di uno strumento di copia in questo modo può ridurre i tempi di inattività.

La tabella seguente classifica gli strumenti Microsoft e la relativa idoneità corrente per le condivisioni file di Azure:

| Consigliato | Strumento | Supporto per le condivisioni file di Azure | Conservazione della fedeltà dei file |
| :-: | :-- | :---- | :---- |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Supportato. Le condivisioni file di Azure possono essere montate come unità di rete. | Fedeltà completa * |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| Sincronizzazione file di Azure | Integrato in modo nativo nelle condivisioni file di Azure. | Fedeltà completa * |
|![Sì, consigliato](media/storage-files-migration-overview/circle-green-checkmark.png)| Servizio di migrazione della risorsa di archiviazione | Supportato indirettamente. Le condivisioni file di Azure possono essere montate come unità di rete nei server di destinazione SMS. | Fedeltà completa * |
|![Non completamente consigliato](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | Supportato. | Non copia i metadati. [Data Box può essere utilizzato con sincronizzazione file di Azure](storage-sync-offline-data-transfer.md). |
|![Non consigliata](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Supportato. | Non copia i metadati. |
|![Non consigliata](media/storage-files-migration-overview/circle-red-x.png)| Esplora archivi Azure | Supportato. | Non copia i metadati. |
|![Non consigliata](media/storage-files-migration-overview/circle-red-x.png)| Data factory di Azure | Supportato. | Non copia i metadati. |
|||||

*\*Fedeltà completa: soddisfa o supera le funzionalità di condivisione file di Azure.*

### <a name="migration-helper-tools"></a>Strumenti di supporto per la migrazione

In questa sezione vengono descritti gli strumenti che consentono di pianificare ed eseguire le migrazioni.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy da Microsoft Corporation

RoboCopy è uno degli strumenti più applicabili alle migrazioni di file. È incluso in Windows. La documentazione principale di [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) è una risorsa utile per le numerose opzioni di questo strumento.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize di JAM Software GmbH

Sincronizzazione file di Azure viene scalato principalmente con il numero di elementi (file e cartelle) e non con la quantità totale di spazio di archiviazione. Lo strumento TreeSize consente di determinare il numero di elementi nei volumi di Windows Server.

È possibile utilizzare lo strumento per creare una prospettiva prima di una [distribuzione di sincronizzazione file di Azure](storage-sync-files-deployment-guide.md). È anche possibile usarlo quando la suddivisione in livelli cloud è impegnata dopo la distribuzione. In questo scenario viene visualizzato il numero di elementi e le directory che usano la cache del server.

La versione testata dello strumento è la versione 4.4.1. È compatibile con i file a livelli cloud. Lo strumento non causerà il richiamo dei file a livelli durante il normale funzionamento.

## <a name="next-steps"></a>Passaggi successivi

1. Creare un piano per la distribuzione di condivisioni file di Azure (solo cloud o ibrido) desiderato.
1. Esaminare l'elenco delle guide alla migrazione disponibili per trovare la guida dettagliata corrispondente all'origine e alla distribuzione delle condivisioni file di Azure.

Di seguito sono riportate altre informazioni sulle tecnologie File di Azure citate in questo articolo:

* [Panoramica della condivisione file di Azure](storage-files-introduction.md)
* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
* [Sincronizzazione file di Azure: suddivisione in livelli nel cloud](storage-sync-cloud-tiering.md)
