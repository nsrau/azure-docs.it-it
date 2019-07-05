---
title: Risolvere i problemi nella casella di dati di Azure, Azure dati casella pesante | Microsoft Docs
description: Viene descritto come risolvere gli errori riscontrati in Azure Data Box e Azure dati casella elevato quando si copiano dati da questi dispositivi.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: bc0681a8ea15f736a7b253d6bd7ba2f7928d2a32
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439407"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Risolvere i problemi relativi a Azure Data Box e Azure dati casella pesante

Questo articolo illustra in dettaglio le informazioni su come risolvere i problemi venga visualizzato quando si usa Azure Data finestra ingenti o Azure Data Box. L'articolo include l'elenco dei possibili errori visualizzati quando si copiano dati nel Data box o quando i dati vengono caricati dal Data Box.

## <a name="error-classes"></a>Classi di errore

Gli errori nel Data Box e complessa di finestra di dati sono riepilogati come segue:

| Categoria di errore *        | Descrizione        | Azione consigliata    |
|----------------------------------------------|---------|--------------------------------------|
| Nomi di contenitore o una condivisione | I nomi di contenitore o una condivisione non seguono le regole di denominazione di Azure.  |Scaricare gli elenchi di errore. <br> Rinominare i contenitori o le condivisioni. [Altre informazioni](#container-or-share-name-errors)  |
| Limite di dimensioni contenitore o una condivisione | I dati in contenitori o le condivisioni totali superano il limite di Azure.   |Scaricare gli elenchi di errore. <br> Ridurre i dati complessivi nel contenitore o nella condivisione. [Altre informazioni](#container-or-share-size-limit-errors)|
| File oggetto o limite di dimensioni | L'oggetto o file in contenitori o le condivisioni supera il limite di Azure.|Scaricare gli elenchi di errore. <br> Ridurre le dimensioni del file nel contenitore o nella condivisione. [Altre informazioni](#object-or-file-size-limit-errors) |    
| Tipo di dati o file | Il formato dei dati o il tipo di file non è supportato. |Scaricare gli elenchi di errore. <br> Per i BLOB di pagine o i dischi gestiti, assicurarsi che i dati sono 512 byte allineato e copiati nelle cartelle create in precedenza. [Altre informazioni](#data-or-file-type-errors) |
| Errori di blob o file non critico  | I nomi di blob o file non seguono le regole di denominazione di Azure o il tipo di file non è supportato. | Questi blob o file non possono essere copiati o i nomi possono essere modificati. [Informazioni su come risolvere questi errori](#non-critical-blob-or-file-errors). |

\* Categorie di quattro errore prima sono gli errori critici e devono essere corretti prima di continuare a prepararsi alla distribuzione.


## <a name="container-or-share-name-errors"></a>Errori di nome contenitore o una condivisione

Si tratta di errori relativi ai nomi di contenitore e la condivisione.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrizione dell'errore:** Il nome del contenitore o della condivisione deve avere una lunghezza compresa tra 3 e 63 caratteri. 

**Risoluzione suggerita:** Nella cartella sotto la share(SMB/NFS) ingenti di finestra di dati o Data Box in cui è stato copiato dati diventa un contenitore di Azure nell'account di archiviazione. 

- Nel **Connetti e copia** pagina l'interfaccia utente web locale dispositivo, scaricare ed esaminare i file degli errori per identificare la cartella denomina con problemi.
- Modificare il nome della cartella nella condivisione di dati finestra ingenti o Data Box per assicurarsi che:

    - Il nome ha lunghezza compresa tra 3 e 63 caratteri.
    - I nomi possono contenere solo lettere, numeri e trattini.
    - I nomi non possono iniziare o terminare con trattini.
    - I nomi non possono contenere trattini consecutivi.
    - Esempi di nomi validi: `my-folder-1`, `my-really-extra-long-folder-111`
    - Esempi di nomi che non sono validi: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Per altre informazioni, vedere le convenzioni di denominazione di Azure per [i nomi dei contenitori](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [condividono nomi](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrizione dell'errore:** Il nome del contenitore o della condivisione deve contenere solo lettere, numeri o trattini.

**Risoluzione suggerita:** Nella cartella sotto la share(SMB/NFS) ingenti di finestra di dati o Data Box in cui è stato copiato dati diventa un contenitore di Azure nell'account di archiviazione. 

- Nel **Connetti e copia** pagina l'interfaccia utente web locale dispositivo, scaricare ed esaminare i file degli errori per identificare la cartella denomina con problemi.
- Modificare il nome della cartella nella condivisione di dati finestra ingenti o Data Box per assicurarsi che:

    - Il nome ha lunghezza compresa tra 3 e 63 caratteri.
    - I nomi possono contenere solo lettere, numeri e trattini.
    - I nomi non possono iniziare o terminare con trattini.
    - I nomi non possono contenere trattini consecutivi.
    - Esempi di nomi validi: `my-folder-1`, `my-really-extra-long-folder-111`
    - Esempi di nomi che non sono validi: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Per altre informazioni, vedere le convenzioni di denominazione di Azure per [i nomi dei contenitori](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [condividono nomi](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrizione dell'errore:** I nomi dei contenitori e i nomi delle condivisioni non può iniziare o terminare con trattini e non può contenere trattini consecutivi.

**Risoluzione suggerita:** Nella cartella sotto la share(SMB/NFS) ingenti di finestra di dati o Data Box in cui è stato copiato dati diventa un contenitore di Azure nell'account di archiviazione. 

- Nel **Connetti e copia** pagina l'interfaccia utente web locale dispositivo, scaricare ed esaminare i file degli errori per identificare la cartella denomina con problemi.
- Modificare il nome della cartella nella condivisione di dati finestra ingenti o Data Box per assicurarsi che:

    - Il nome ha lunghezza compresa tra 3 e 63 caratteri.
    - I nomi possono contenere solo lettere, numeri e trattini.
    - I nomi non possono iniziare o terminare con trattini.
    - I nomi non possono contenere trattini consecutivi.
    - Esempi di nomi validi: `my-folder-1`, `my-really-extra-long-folder-111`
    - Esempi di nomi che non sono validi: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Per altre informazioni, vedere le convenzioni di denominazione di Azure per [i nomi dei contenitori](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [condividono nomi](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Errori di limite dimensioni contenitore o una condivisione

Si tratta di errori correlati a dati che superano le dimensioni dei dati in un contenitore o una condivisione è consentiti.

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrizione dell'errore:** Condivisione file di Azure limita una condivisione di 5 TB di dati. Questo limite ha superato alcune condivisioni.

**Risoluzione suggerita:** Nel **Connetti e copia** pagina del web locale dell'interfaccia utente, scaricare ed esaminare i file di errore.

Identificare le cartelle che hanno questo problema dai log di errore e assicurarsi che i file in tale cartella siano di dimensioni inferiori a 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Oggetto o file gli errori di limite dimensioni

Si tratta di errori correlati a dati che superano le dimensioni massime dell'oggetto o il file che è consentito in Azure. 

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrizione dell'errore:** Le dimensioni del file superano quelle massime consentite per il caricamento.

**Risoluzione suggerita:** Il blob o le dimensioni dei file superano il limite massimo consentito per il caricamento.

- Nel **Connetti e copia** pagina del web locale dell'interfaccia utente, scaricare ed esaminare i file di errore.
- Assicurarsi che le dimensioni dei blob e file non superino i limiti delle dimensioni di oggetto di Azure.

## <a name="data-or-file-type-errors"></a>File di dati o errori di tipo

Si tratta di errori correlati al tipo di file non supportato o tipo di dati disponibili nel contenitore o nella condivisione. 

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrizione dell'errore:** Il blob o file è allineato in modo errato.

**Risoluzione suggerita:** La condivisione di blob di pagina nel Data Box o elevato di finestra di dati solo supporta i file che sono 512 byte allineato (ad esempio, file VHD/VHDX). Tutti i dati copiati nella condivisione di blob di pagina viene caricati in Azure come BLOB di pagine.

Rimuovere tutti i dati di file VHD/VHDX dalla condivisione di blob di pagina. È possibile usare le condivisioni per blob in blocchi o i file di Azure per dati generici.

Per altre informazioni, vedere [pagina della panoramica di BLOB](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrizione dell'errore:** Un tipo non supportato è presente in una condivisione di dischi gestiti. Sono consentiti solo i dischi rigidi virtuali fissi.

**Risoluzione suggerita:**

- Assicurarsi di caricare solo i dischi rigidi virtuali fissi per creare dischi gestiti.
- I file VHDX o **dinamici** e **differenziazione** dischi rigidi virtuali non sono supportati.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrizione dell'errore:** Una directory non è consentita in una qualsiasi delle cartelle esistenti per i dischi gestiti. In queste cartelle sono consentiti solo i dischi rigidi virtuali fissi.

**Risoluzione suggerita:** Per i dischi gestiti, all'interno di ogni condivisione, vengono create i seguenti tre cartelle che corrispondono ai contenitori nell'account di archiviazione: Unità SSD Premium Standard HDD e SSD Standard. Queste cartelle corrispondono al livello di prestazioni per il disco gestito.

- Assicurarsi di copiare i dati di blob di pagina (VHD) in una di queste cartelle esistenti.
- Una cartella o la directory non è consentito in queste cartelle esistenti. Rimuovere le cartelle creati all'interno delle cartelle preesistente.

Per altre informazioni, vedere [copia al servizio managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparsepointerror"></a>REPARSE_POINT_ERROR

**Descrizione dell'errore:** I collegamenti simbolici non sono consentiti in Linux. 

**Risoluzione suggerita:** I collegamenti simbolici sono in genere collegamenti, pipe e altri file di questo tipo. Rimuovere i collegamenti, o risolvere i collegamenti e copiare i dati.


## <a name="non-critical-blob-or-file-errors"></a>Errori di blob o file non critico

Tutti gli errori che sono visibili durante la copia dei dati sono riepilogati nelle sezioni seguenti.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrizione dell'errore:** I nomi di blob o file contengono caratteri di controllo non supportato.

**Risoluzione suggerita:** I BLOB o i file che è stata copiata contengono i nomi con caratteri non supportati.

Nel **Connetti e copia** pagina del web locale dell'interfaccia utente, scaricare ed esaminare i file di errore.
Rimuovere o rinominare i file per rimuovere caratteri non supportati.

Per altre informazioni, vedere le convenzioni di denominazione di Azure per [nomi di blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomi file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrizione dell'errore:** I nomi di blob o file contengono caratteri non validi.

**Risoluzione suggerita:** I BLOB o i file che è stata copiata contengono i nomi con caratteri non supportati.

Nel **Connetti e copia** pagina del web locale dell'interfaccia utente, scaricare ed esaminare i file di errore.
Rimuovere o rinominare i file per rimuovere caratteri non supportati.

Per altre informazioni, vedere le convenzioni di denominazione di Azure per [nomi di blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomi file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrizione dell'errore:** I nomi di blob o file termina con caratteri non validi.

**Risoluzione suggerita:** I BLOB o i file che è stata copiata contengono i nomi con caratteri non supportati.

Nel **Connetti e copia** pagina del web locale dell'interfaccia utente, scaricare ed esaminare i file di errore.
Rimuovere o rinominare i file per rimuovere caratteri non supportati.

Per altre informazioni, vedere le convenzioni di denominazione di Azure per [nomi di blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomi file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrizione dell'errore:** Il nome del blob o il file contiene troppi segmenti di percorso.

**Risoluzione suggerita:** I BLOB o i file che è stata copiata superano il numero massimo di segmenti di percorso. Un segmento di percorso è la stringa di caratteri compreso tra delimitatori consecutivi, ad esempio, la barra rovesciata /.

- Nel **Connetti e copia** pagina del web locale dell'interfaccia utente, scaricare ed esaminare i file di errore.
- Assicurarsi che il [nomi di blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomi file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) conforme alle convenzioni di denominazione di Azure.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrizione dell'errore:** Il nome del BLOB o del file è troppo lungo.

**Risoluzione suggerita:** Il blob o i nomi dei file superano la lunghezza massima consentita.

- Nel **Connetti e copia** pagina del web locale dell'interfaccia utente, scaricare ed esaminare i file di errore.
- Il nome del blob non può superare 1024 caratteri.
- Rimuovere o rinominare il blob o file in modo che i nomi non superare i 1024 caratteri.

Per altre informazioni, vedere le convenzioni di denominazione di Azure per i nomi di blob e i nomi di file.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrizione dell'errore:** Uno dei segmenti nel nome del blob o del file è troppo lungo.

**Risoluzione suggerita:** Uno dei segmenti del percorso nel nome del blob o file supera il numero massimo di caratteri. Un segmento di percorso è la stringa di caratteri compreso tra delimitatori consecutivi, ad esempio, la barra rovesciata /.

- Nel **Connetti e copia** pagina del web locale dell'interfaccia utente, scaricare ed esaminare i file di errore.
- Assicurarsi che il [nomi di blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomi file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) conforme alle convenzioni di denominazione di Azure.


### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrizione dell'errore:** Vengono specificati i nomi dei contenitori non corretta per le condivisioni di disco gestito.

**Risoluzione suggerita:** Per i dischi gestiti, all'interno di ogni condivisione, vengono create le cartelle seguenti che corrispondono ai contenitori nell'account di archiviazione: Unità SSD Premium Standard HDD e SSD Standard. Queste cartelle corrispondono al livello di prestazioni per il disco gestito.

- Assicurarsi di copiare i dati di blob di pagina (VHD) in una di queste cartelle esistenti. Solo i dati da questi contenitori esistenti viene caricati in Azure.
- Qualsiasi altra cartella che viene creata allo stesso livello Premium SSD Standard HDD e SSD Standard non corrisponde a un livello di prestazioni valido e non può essere utilizzata.
- Rimuovere i file o cartelle creati di fuori i livelli di prestazioni.

Per altre informazioni, vedere [copia al servizio managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Passaggi successivi

- Scopri le [requisiti di sistema di archiviazione Blob di dati finestra](data-box-system-requirements-rest.md).
