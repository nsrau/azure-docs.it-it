---
title: Risolvere i problemi relativi alla Azure Data Box, Azure Data Box Heavy | Microsoft Docs
description: Viene descritto come risolvere i problemi riscontrati in Azure Data Box e Azure Data Box Heavy durante la copia dei dati in tali dispositivi.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 83f6f7c7f8cd5155669f12fd6e426f86ef1c7baa
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848502"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Risolvere i problemi relativi a Azure Data Box e Azure Data Box Heavy

In questo articolo vengono fornite informazioni dettagliate su come risolvere i problemi che possono verificarsi quando si utilizza il Azure Data Box o Azure Data Box Heavy. L'articolo include l'elenco dei possibili errori visualizzati quando i dati vengono copiati nel Data Box o quando i dati vengono caricati da Data Box.

## <a name="error-classes"></a>Classi di errore

Gli errori in Data Box e Data Box Heavy sono riepilogati come segue:

| Categoria di errore *        | Descrizione        | Azione consigliata    |
|----------------------------------------------|---------|--------------------------------------|
| Nomi di contenitori o condivisioni | I nomi di contenitore o condivisione non seguono le regole di denominazione di Azure.  |Scaricare gli elenchi degli errori. <br> Rinominare i contenitori o le condivisioni. [Altre informazioni](#container-or-share-name-errors)  |
| Limite dimensioni contenitore o condivisione | Il totale dei dati in contenitori o condivisioni supera il limite di Azure.   |Scaricare gli elenchi degli errori. <br> Ridurre i dati complessivi nel contenitore o nella condivisione. [Altre informazioni](#container-or-share-size-limit-errors)|
| Limite dimensioni oggetti o file | L'oggetto o i file in contenitori o condivisioni superano il limite di Azure.|Scaricare gli elenchi degli errori. <br> Ridurre le dimensioni del file nel contenitore o nella condivisione. [Altre informazioni](#object-or-file-size-limit-errors) |    
| Tipo di dati o di file | Il formato dei dati o il tipo di file non è supportato. |Scaricare gli elenchi degli errori. <br> Per i BLOB di pagine o i dischi gestiti, assicurarsi che i dati siano allineati a 512 byte e che vengano copiati nelle cartelle create in precedenza. [Altre informazioni](#data-or-file-type-errors) |
| Errori di BLOB o file non critici  | I nomi di BLOB o file non seguono le regole di denominazione di Azure o il tipo di file non è supportato. | È possibile che questi BLOB o file non vengano copiati o che i nomi vengano modificati. [Informazioni su come risolvere questi errori](#non-critical-blob-or-file-errors). |

\*Le prime quattro categorie di errore sono errori critici e devono essere corrette prima di procedere alla preparazione per la spedizione.


## <a name="container-or-share-name-errors"></a>Errori del nome del contenitore o della condivisione

Si tratta di errori relativi ai nomi di contenitori e condivisioni.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrizione dell'errore:** Il nome del contenitore o della condivisione deve avere una lunghezza compresa tra 3 e 63 caratteri. 

**Risoluzione suggerita:** La cartella sotto la Data Box o la condivisione Data Box Heavy (SMB/NFS) in cui sono stati copiati i dati diventa un contenitore di Azure nell'account di archiviazione. 

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale del dispositivo scaricare ed esaminare i file di errore per identificare i nomi di cartella con i problemi.
- Modificare il nome della cartella nella Data Box o nella condivisione Data Box Heavy per assicurarsi che:

    - Il nome ha una lunghezza compresa tra 3 e 63 caratteri.
    - I nomi possono contenere solo lettere, numeri e trattini.
    - I nomi non possono iniziare o terminare con trattini.
    - I nomi non possono avere trattini consecutivi.
    - Esempi di nomi validi: `my-folder-1`,`my-really-extra-long-folder-111`
    - Esempi di nomi non validi `my-folder_1`:, `my`, `--myfolder`, `myfolder--`,`myfolder!`

    Per altre informazioni, vedere convenzioni di denominazione di Azure per i [nomi dei contenitori](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e le [condivisioni](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrizione dell'errore:** Il nome del contenitore o della condivisione deve contenere solo lettere, numeri o trattini.

**Risoluzione suggerita:** La cartella sotto la Data Box o la condivisione Data Box Heavy (SMB/NFS) in cui sono stati copiati i dati diventa un contenitore di Azure nell'account di archiviazione. 

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale del dispositivo scaricare ed esaminare i file di errore per identificare i nomi di cartella con i problemi.
- Modificare il nome della cartella nella Data Box o nella condivisione Data Box Heavy per assicurarsi che:

    - Il nome ha una lunghezza compresa tra 3 e 63 caratteri.
    - I nomi possono contenere solo lettere, numeri e trattini.
    - I nomi non possono iniziare o terminare con trattini.
    - I nomi non possono avere trattini consecutivi.
    - Esempi di nomi validi: `my-folder-1`,`my-really-extra-long-folder-111`
    - Esempi di nomi non validi `my-folder_1`:, `my`, `--myfolder`, `myfolder--`,`myfolder!`

    Per altre informazioni, vedere convenzioni di denominazione di Azure per i [nomi dei contenitori](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e le [condivisioni](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrizione dell'errore:** I nomi di contenitore e di condivisione non possono iniziare o terminare con trattini e non possono avere trattini consecutivi.

**Risoluzione suggerita:** La cartella sotto la Data Box o la condivisione Data Box Heavy (SMB/NFS) in cui sono stati copiati i dati diventa un contenitore di Azure nell'account di archiviazione. 

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale del dispositivo scaricare ed esaminare i file di errore per identificare i nomi di cartella con i problemi.
- Modificare il nome della cartella nella Data Box o nella condivisione Data Box Heavy per assicurarsi che:

    - Il nome ha una lunghezza compresa tra 3 e 63 caratteri.
    - I nomi possono contenere solo lettere, numeri e trattini.
    - I nomi non possono iniziare o terminare con trattini.
    - I nomi non possono avere trattini consecutivi.
    - Esempi di nomi validi: `my-folder-1`,`my-really-extra-long-folder-111`
    - Esempi di nomi non validi `my-folder_1`:, `my`, `--myfolder`, `myfolder--`,`myfolder!`

    Per altre informazioni, vedere convenzioni di denominazione di Azure per i [nomi dei contenitori](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e le [condivisioni](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Errori limite dimensioni contenitore o condivisione

Si tratta di errori relativi ai dati che superano le dimensioni dei dati consentiti in un contenitore o in una condivisione.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrizione dell'errore:** La condivisione file di Azure limita una condivisione a 5 TB di dati. Questo limite è stato superato per alcune condivisioni.

**Risoluzione suggerita:** Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file degli errori.

Identificare le cartelle che presentano questo problema nei log degli errori e verificare che i file in tale cartella siano di 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Errori limite dimensioni oggetti o file

Si tratta di errori relativi ai dati che superano le dimensioni massime dell'oggetto o del file consentito in Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrizione dell'errore:** Le dimensioni del file superano quelle massime consentite per il caricamento.

**Risoluzione suggerita:** Il BLOB o le dimensioni del file superano il limite massimo consentito per il caricamento.

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file degli errori.
- Assicurarsi che le dimensioni del BLOB e del file non superino i limiti delle dimensioni degli oggetti di Azure.

## <a name="data-or-file-type-errors"></a>Errori relativi ai dati o ai tipi di file

Si tratta di errori correlati al tipo di file non supportato o al tipo di dati trovato nel contenitore o nella condivisione. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrizione dell'errore:** Il blob o file è allineato in modo errato.

**Risoluzione suggerita:** La condivisione BLOB di pagine in Data Box o Data Box Heavy supporta solo file di 512 byte allineati, ad esempio VHD/VHDX. Tutti i dati copiati nella condivisione BLOB di pagine vengono caricati in Azure come BLOB di pagine.

Rimuovere tutti i dati non VHD/VHDX dalla condivisione BLOB di pagine. È possibile usare le condivisioni per i BLOB in blocchi o i file di Azure per i dati generici.

Per altre informazioni, vedere [Panoramica dei BLOB di pagine](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrizione dell'errore:** Un tipo di file non supportato è presente in una condivisione disco gestita. Sono consentiti solo dischi rigidi virtuali fissi.

**Risoluzione suggerita:**

- Assicurarsi di caricare solo i VHD fissi per creare dischi gestiti.
- I file VHDX o i dischi rigidi virtuali **dinamici** e **differenze** non sono supportati.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrizione dell'errore:** Una directory non è consentita in alcuna cartella preesistente per i dischi gestiti. In queste cartelle sono consentiti solo dischi rigidi virtuali fissi.

**Risoluzione suggerita:** Per i dischi gestiti, all'interno di ogni condivisione vengono create le tre cartelle seguenti che corrispondono ai contenitori nell'account di archiviazione: SSD Premium, HDD Standard e SDD Standard. Queste cartelle corrispondono al livello di prestazioni per il disco gestito.

- Assicurarsi di copiare i dati dei BLOB di pagine (VHD) in una di queste cartelle esistenti.
- Una cartella o una directory non è consentita in queste cartelle esistenti. Rimuovere tutte le cartelle create all'interno delle cartelle preesistenti.

Per altre informazioni, vedere [Copy to Managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Descrizione dell'errore:** I collegamenti simbolici non sono consentiti in Linux. 

**Risoluzione suggerita:** I collegamenti simbolici sono in genere collegamenti, pipe e altri file di questo tipo. Rimuovere i collegamenti o risolvere i collegamenti e copiare i dati.


## <a name="non-critical-blob-or-file-errors"></a>Errori di BLOB o file non critici

Tutti gli errori non critici relativi ai nomi di BLOB, file o contenitori visualizzati durante la copia dei dati sono riepilogati nella sezione seguente. Se questi errori sono presenti, i nomi verranno modificati in modo da essere conformi alle convenzioni di denominazione di Azure. Lo stato dell'ordine corrispondente per il caricamento dei dati verrà **completato con avvisi**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrizione dell'errore:** I nomi di BLOB o file contengono caratteri di controllo non supportati.

**Risoluzione suggerita:** I BLOB o i file copiati contengono nomi con caratteri non supportati.

Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file degli errori.
Rimuovere o rinominare i file per rimuovere i caratteri non supportati.

Per altre informazioni, vedere convenzioni di denominazione di Azure per i [nomi di BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i [nomi di file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrizione dell'errore:** I nomi di BLOB o file contengono caratteri non validi.

**Risoluzione suggerita:** I BLOB o i file copiati contengono nomi con caratteri non supportati.

Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file degli errori.
Rimuovere o rinominare i file per rimuovere i caratteri non supportati.

Per altre informazioni, vedere convenzioni di denominazione di Azure per i [nomi di BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i [nomi di file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrizione dell'errore:** I nomi di BLOB o file terminano con caratteri non validi.

**Risoluzione suggerita:** I BLOB o i file copiati contengono nomi con caratteri non supportati.

Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file degli errori.
Rimuovere o rinominare i file per rimuovere i caratteri non supportati.

Per altre informazioni, vedere convenzioni di denominazione di Azure per i [nomi di BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i [nomi di file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrizione dell'errore:** Il nome del BLOB o del file contiene troppi segmenti di percorso.

**Risoluzione suggerita:** I BLOB o i file copiati superano il numero massimo di segmenti di percorso. Un segmento di percorso è la stringa tra i caratteri delimitatori consecutivi, ad esempio la barra/.

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file degli errori.
- Verificare che i [nomi dei BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i [nomi dei file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) siano conformi alle convenzioni di denominazione di Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrizione dell'errore:** Il nome del BLOB o del file è troppo lungo.

**Risoluzione suggerita:** Il BLOB o i nomi dei file superano la lunghezza massima.

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file degli errori.
- Il nome del BLOB non deve superare i 1.024 caratteri.
- Rimuovere o rinominare i BLOB o i file in modo che i nomi non superino 1024 caratteri.

Per altre informazioni, vedere convenzioni di denominazione di Azure per i nomi di BLOB e i nomi di file.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrizione dell'errore:** Uno dei segmenti nel nome del blob o del file è troppo lungo.

**Risoluzione suggerita:** Uno dei segmenti di percorso nel BLOB o nel nome file supera il numero massimo di caratteri. Un segmento di percorso è la stringa tra i caratteri delimitatori consecutivi, ad esempio la barra/.

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file degli errori.
- Verificare che i [nomi dei BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i [nomi dei file](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) siano conformi alle convenzioni di denominazione di Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrizione dell'errore:** Per le condivisioni disco gestite sono specificati nomi di contenitore non corretti.

**Risoluzione suggerita:** Per i dischi gestiti, all'interno di ogni condivisione vengono create le cartelle seguenti che corrispondono ai contenitori nell'account di archiviazione: SSD Premium, HDD Standard e SDD Standard. Queste cartelle corrispondono al livello di prestazioni per il disco gestito.

- Assicurarsi di copiare i dati dei BLOB di pagine (VHD) in una di queste cartelle esistenti. Solo i dati di questi contenitori esistenti vengono caricati in Azure.
- Qualsiasi altra cartella creata allo stesso livello di SSD Premium, HDD Standard e SDD Standard non corrisponde a un livello di prestazioni valido e non può essere utilizzata.
- Rimuovere i file o le cartelle creati all'esterno dei livelli di prestazioni.

Per altre informazioni, vedere [Copy to Managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui requisiti di sistema per l' [archiviazione Blob Data Box](data-box-system-requirements-rest.md).
