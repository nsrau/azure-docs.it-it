---
title: Conservazione degli ACL, degli attributi e dei timestamp dei file con Azure Data Box
description: Gli ACL, i timestamp e gli attributi conservati durante la copia dei dati tramite SMB Azure Data Box. Copia di metadati con strumenti per la copia di dati Windows e Linux.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950313"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Conservazione degli ACL, degli attributi e dei timestamp dei file con Azure Data Box

Azure Data Box consente di mantenere gli elenchi di controllo di accesso (ACL), i timestamp e gli attributi del file quando si inviano dati ad Azure. Questo articolo descrive i metadati che è possibile trasferire durante la copia dei dati in Data Box tramite Server Message Block (SMB) per caricarli File di Azure. 

Sono disponibili passaggi specifici per la copia di metadati con gli strumenti di copia dei dati di Windows e Linux. I metadati non vengono conservati durante il trasferimento dei dati nell'archivio BLOB.

In questo articolo gli ACL, i timestamp e gli attributi di file trasferiti vengono definiti collettivamente come *metadati*.

## <a name="transferred-metadata"></a>Metadati trasferiti

I metadati seguenti vengono trasferiti quando i dati del Data Box vengono caricati File di Azure.

#### <a name="timestamps"></a>Timestamp

Vengono trasferiti i timestamp seguenti:
- CreationTime
- LastWriteTime

Il timestamp seguente non viene trasferito:
- LastAccessTime
  
#### <a name="file-attributes"></a>Attributi di file

Gli attributi di file in entrambi i file e le directory vengono trasferiti se non diversamente specificato.

Vengono trasferiti gli attributi di file seguenti:
- FILE_ATTRIBUTE_READONLY (solo file)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (solo directory)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (solo file)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

Gli attributi di file seguenti non vengono trasferiti:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Gli attributi di sola lettura nelle directory non vengono trasferiti.

#### <a name="acls"></a>ACL

Tutti gli ACL per le directory e i file copiati nella Data Box tramite SMB vengono copiati e trasferiti. I trasferimenti includono elenchi ACL discrezionali (DACL) e ACL di sistema (SACL). Per Linux vengono trasferiti solo gli ACL di Windows NT.

Gli ACL non vengono trasferiti durante le copie dei dati nel file System di rete (NTS) e quando si usa il servizio di copia dei dati per trasferire i dati. Il servizio copia dati legge i dati direttamente dalle condivisioni e non è in grado di leggere gli ACL.

Anche se lo strumento per la copia dei dati non copia gli ACL, gli ACL predefiniti per le directory e i file vengono trasferiti in File di Azure. Gli ACL predefiniti dispongono delle autorizzazioni per l'account amministratore predefinito, l'account di sistema e l'account utente di condivisione SMB utilizzato per montare e copiare i dati nel Data Box.

Gli ACL contengono descrittori di sicurezza con le proprietà seguenti: ACL, proprietario, gruppo, SACL.

Il trasferimento degli ACL è abilitato per impostazione predefinita. Potrebbe essere necessario disabilitare questa impostazione nell'interfaccia utente Web locale del Data Box. Per altre informazioni, vedere [usare l'interfaccia utente Web locale per amministrare il data box e data box Heavy](./data-box-local-web-ui-admin.md).

> [!NOTE]
> I file con ACL contenenti stringhe ACE (Conditional Access Control Entry) non vengono copiati. Si tratta di un problema noto. Per risolvere il problema, copiare i file nella condivisione File di Azure manualmente montando la condivisione e quindi usando uno strumento di copia che supporta la copia degli ACL.

## <a name="copying-data-and-metadata"></a>Copia di dati e metadati

Per trasferire gli ACL, i timestamp e gli attributi dei dati, utilizzare le procedure seguenti per copiare i dati nel Data Box. 

### <a name="windows-data-copy-tool"></a>Strumento per la copia dei dati di Windows

Per copiare i dati nella Data Box tramite SMB, usare uno strumento di copia di file compatibile con SMB, ad esempio `robocopy` . Il comando di esempio seguente copia tutti i file e le directory, trasferendo i metadati insieme ai dati.

Quando si usa `/copyall` l' `/dcopy:DAT` opzione o, assicurarsi che i privilegi necessari per l'operatore di backup non siano disabilitati. Per altre informazioni, vedere [usare l'interfaccia utente Web locale per amministrare il data box e data box Heavy](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

where

|Opzione |Descrizione |
|------------------- | ----- |
|`/copyall` |Copia tutti gli attributi.|
|`/e`      |Copia le sottodirectory, incluse le directory vuote.         |
|`/dcopy:DAT`  |Copia i dati, gli attributi e i timestamp. Nota: per trasferire le directory, è necessario usare l'opzione/DCOPY: DAT `CreationTime` . |
|`/r:3`    |Specifica 3 tentativi per le copie non riuscite.         |
|`/w:60`   |Specifica un tempo di attesa di 60 secondi tra i tentativi.         |
|`/is`     |Include gli stessi file.         |
|`/nfl`    |Non registra i nomi dei file.         |
|`/ndl`    |Non registra i nomi di directory.        |
|`/np`     |Non Visualizza lo stato di avanzamento dell'operazione di copia.         |
|`/MT:32 or 64`  |Usa il multithreading, con thread 32 o 64.           |
|`/fft`    |Riduce la granularità dei timestamp per qualsiasi file system.        |
|`/log+:<LogFile>`  |Accoda l'output al file di log esistente.|

Per altre informazioni su questi `robocopy` parametri, vedere [esercitazione: copiare dati in Azure Data Box tramite SMB](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Strumento per la copia dei dati di Linux

Il trasferimento dei metadati in Linux è un processo in due passaggi. Prima di tutto, copiare i dati di origine usando uno strumento come `rsync` , che non copia i metadati. Dopo aver copiato i dati, è possibile copiare i metadati usando uno strumento come `smbcacls` o `cifsacl` . 

I comandi di esempio seguenti eseguono il primo passaggio, copiando i dati usando `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Passaggi successivi

- [Copiare dati in Azure Data Box tramite SMB](./data-box-deploy-copy-data.md)