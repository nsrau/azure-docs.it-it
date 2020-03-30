---
title: Risolvere i problemi in Azure Data Box, Azure Data Box HeavyTroubleshoot issues on your Azure Data Box, Azure Data Box Heavy
description: Viene descritto come risolvere i problemi riscontrati in Azure Data Box e Azure Data Box Heavy durante la copia dei dati in questi dispositivi.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560066"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Risolvere i problemi relativi ad Azure Data Box e Azure Data Box HeavyTroubleshoot issues related to Azure Data Box and Azure Data Box Heavy

Questo articolo descrive in dettaglio le informazioni su come risolvere i problemi che possono essere visualizzati quando si usa Azure Data Box o Azure Data Box Heavy.This article details information on how to troubleshoot issues you may see when using the Azure Data Box or Azure Data Box Heavy. L'articolo include l'elenco dei possibili errori visualizzati quando i dati vengono copiati nella casella dati o quando i dati vengono caricati da Data Box.

## <a name="error-classes"></a>Classi di errore

Gli errori in Data Box e Data Box Heavy sono riepilogati come segue:

| Categoria di errore        | Descrizione        | Azione consigliata    |
|----------------------------------------------|---------|--------------------------------------|
| Nomi di contenitori o condivisioni | I nomi di contenitore o condivisione non seguono le regole di denominazione di Azure.The container or share names do not follow the Azure naming rules.  |Scaricare gli elenchi di errori. <br> Rinominare i contenitori o le condivisioni. [Scopri di più](#container-or-share-name-errors).  |
| Limite di dimensione del contenitore o della condivisione | I dati totali in contenitori o condivisioni superano il limite di Azure.The total data in containers or shares exceeds the Azure limit.   |Scaricare gli elenchi di errori. <br> Ridurre i dati complessivi nel contenitore o nella condivisione. [Scopri di più](#container-or-share-size-limit-errors).|
| Limite di dimensione dell'oggetto o del file | L'oggetto o i file in contenitori o condivisioni superano il limite di Azure.The object or files in containers or shares exceeds the Azure limit.|Scaricare gli elenchi di errori. <br> Ridurre le dimensioni del file nel contenitore o nella condivisione. [Scopri di più](#object-or-file-size-limit-errors). |    
| Tipo di dati o file | Il formato dei dati o il tipo di file non è supportato. |Scaricare gli elenchi di errori. <br> Per i BLOB di pagine o i dischi gestiti, verificare che i dati siano allineati a 512 byte e copiati nelle cartelle create in precedenazione. [Scopri di più](#data-or-file-type-errors). |
| Errori di file o BLOB non critici  | I nomi BLOB o file non seguono le regole di denominazione di Azure o il tipo di file non è supportato. | Questi BLOB o file potrebbero non essere copiati o i nomi potrebbero essere modificati. [Informazioni su come correggere questi errori](#non-critical-blob-or-file-errors). |

\*Le prime quattro categorie di errore sono errori critici e devono essere corrette prima di poter procedere alla preparazione della spedizione.


## <a name="container-or-share-name-errors"></a>Errori relativi al nome del contenitore o della condivisione

Si tratta di errori correlati ai nomi di contenitori e condivisioni.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrizione errore:** Il nome del contenitore o della condivisione deve essere compreso tra 3 e 63 caratteri. 

**Risoluzione suggerita:** La cartella nella condivisione Data Box o Data Box Heavy (SMB/NFS) in cui sono stati copiati i dati diventa un contenitore di Azure nell'account di archiviazione. 

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale del dispositivo scaricare ed esaminare i file di errore per identificare i nomi delle cartelle con problemi.
- Modificare il nome della cartella nella casella dati o nella condivisione Data Box Heavy per assicurarsi che:

    - Il nome ha un numero di caratteri compreso tra 3 e 63 caratteri.
    - I nomi possono avere solo lettere, numeri e trattini.
    - I nomi non possono iniziare o terminare con i trattini.
    - I nomi non possono avere trattini consecutivi.
    - Esempi di nomi `my-folder-1`validi: ,`my-really-extra-long-folder-111`
    - Esempi di nomi non `my-folder_1`validi: `my` `--myfolder`, `myfolder--`, , , ,`myfolder!`

    Per altre informazioni, vedere Convenzioni di denominazione di Azure per i nomi dei [contenitori](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e i nomi di [condivisione.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrizione errore:** Il nome del contenitore o della condivisione deve essere costituito solo da lettere, numeri o trattini.

**Risoluzione suggerita:** La cartella nella condivisione Data Box o Data Box Heavy (SMB/NFS) in cui sono stati copiati i dati diventa un contenitore di Azure nell'account di archiviazione. 

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale del dispositivo scaricare ed esaminare i file di errore per identificare i nomi delle cartelle con problemi.
- Modificare il nome della cartella nella casella dati o nella condivisione Data Box Heavy per assicurarsi che:

    - Il nome ha un numero di caratteri compreso tra 3 e 63 caratteri.
    - I nomi possono avere solo lettere, numeri e trattini.
    - I nomi non possono iniziare o terminare con i trattini.
    - I nomi non possono avere trattini consecutivi.
    - Esempi di nomi `my-folder-1`validi: ,`my-really-extra-long-folder-111`
    - Esempi di nomi non `my-folder_1`validi: `my` `--myfolder`, `myfolder--`, , , ,`myfolder!`

    Per altre informazioni, vedere Convenzioni di denominazione di Azure per i nomi dei [contenitori](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e i nomi di [condivisione.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrizione errore:** I nomi dei contenitori e delle condivisioni non possono iniziare o terminare con trattini e non possono avere trattini consecutivi.

**Risoluzione suggerita:** La cartella nella condivisione Data Box o Data Box Heavy (SMB/NFS) in cui sono stati copiati i dati diventa un contenitore di Azure nell'account di archiviazione. 

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale del dispositivo scaricare ed esaminare i file di errore per identificare i nomi delle cartelle con problemi.
- Modificare il nome della cartella nella casella dati o nella condivisione Data Box Heavy per assicurarsi che:

    - Il nome ha un numero di caratteri compreso tra 3 e 63 caratteri.
    - I nomi possono avere solo lettere, numeri e trattini.
    - I nomi non possono iniziare o terminare con i trattini.
    - I nomi non possono avere trattini consecutivi.
    - Esempi di nomi `my-folder-1`validi: ,`my-really-extra-long-folder-111`
    - Esempi di nomi non `my-folder_1`validi: `my` `--myfolder`, `myfolder--`, , , ,`myfolder!`

    Per altre informazioni, vedere Convenzioni di denominazione di Azure per i nomi dei [contenitori](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e i nomi di [condivisione.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)

## <a name="container-or-share-size-limit-errors"></a>Errori di limite di dimensione del contenitore o della condivisione

Si tratta di errori correlati a dati superiori alle dimensioni dei dati consentiti in un contenitore o in una condivisione.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrizione errore:** La condivisione file di Azure limita una condivisione a 5 TB di dati. Questo limite è stato superato per alcune azioni.

**Risoluzione suggerita:** Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file di errore.

Identificare le cartelle che presentano questo problema dai log degli errori e assicurarsi che i file in tale cartella sono sotto 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Errori di limite di dimensioni di oggetti o file

Si tratta di errori correlati adati relativi a dati che superano le dimensioni massime dell'oggetto o del file consentito in Azure.These are errors related to data exceeding the maximum size of object or the file that is allowed in Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrizione errore:** La dimensione del file supera la dimensione massima del file per il caricamento.

**Risoluzione suggerita:** Il BLOB o le dimensioni del file superano il limite massimo consentito per il caricamento.

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file di errore.
- Assicurarsi che il BLOB e le dimensioni dei file non superino i limiti delle dimensioni degli oggetti di Azure.Make sure that the blob and file sizes do not exceed the Azure object size limits.

## <a name="data-or-file-type-errors"></a>Errori relativi ai dati o ai tipi di file

Si tratta di errori relativi al tipo di file o al tipo di dati non supportato trovato nel contenitore o nella condivisione. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrizione errore:** Il BLOB o il file non è allineato correttamente.

**Risoluzione suggerita:** La condivisione BLOB di pagine in Data Box o Data Box Heavy supporta solo file allineati a 512 byte, ad esempio VHD/VHDX. Tutti i dati copiati nella condivisione BLOB di pagine vengono caricati in Azure come BLOB di pagine.

Rimuovere tutti i dati non VHD/VHDX dalla condivisione BLOB di pagine. È possibile usare condivisioni per i file BLOB a blocchi o di Azure per i dati generici.

Per altre informazioni, vedere [Panoramica dei BLOB di pagine.](../storage/blobs/storage-blob-pageblob-overview.md)

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrizione errore:** Un tipo di file non supportato è presente in una condivisione su disco gestito. Sono consentiti solo dischi rigidi virtuali fissi.

**Risoluzione suggerita:**

- Assicurarsi di caricare solo i dischi rigidi fissi per creare dischi gestiti.
- I file VHDX o i dischi rigidi virtuali **dinamici** e **differenze** non sono supportati.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrizione errore:** Una directory non è consentita in nessuna delle cartelle preesistenti per i dischi gestiti. In queste cartelle sono consentiti solo dischi rigidi virtuali fissi.

**Risoluzione suggerita:** Per i dischi gestiti, all'interno di ogni condivisione, vengono create le tre cartelle seguenti che corrispondono ai contenitori nell'account di archiviazione: Premium SSD, Standard HDD e SSD standard. Queste cartelle corrispondono al livello di prestazioni per il disco gestito.

- Assicurarsi di copiare i dati BLOB di pagina (VHD) in una di queste cartelle esistenti.
- Una cartella o una directory non è consentita in queste cartelle esistenti. Rimuovere tutte le cartelle create all'interno delle cartelle preesistenti.

Per ulteriori informazioni, consultate Copiare su [dischi gestiti.](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Descrizione errore:** I collegamenti simbolici non sono consentiti in Linux.Symbolic links are not allowed in Linux. 

**Risoluzione suggerita:** I collegamenti simbolici sono in genere collegamenti, pipe e altri file di questo tipo. Rimuovere i collegamenti o risolvere i collegamenti e copiare i dati.


## <a name="non-critical-blob-or-file-errors"></a>Errori di file o BLOB non critici

Tutti gli errori non critici correlati ai nomi di BLOB, file o contenitori visualizzati durante la copia dei dati sono riepilogati nella sezione seguente. Se questi errori sono presenti, i nomi verranno modificati in modo conforme alle convenzioni di denominazione di Azure.If these errors are present, then the names will be modified to conform to the Azure naming conventions. Lo stato dell'ordine corrispondente per il caricamento dei dati sarà **Completato con avvisi**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrizione errore:** I nomi BLOB o file contengono caratteri di controllo non supportati.

**Risoluzione suggerita:** I BLOB o i file copiati contengono nomi con caratteri non supportati.

Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file di errore.
Rimuovere o rinominare i file per rimuovere i caratteri non supportati.

Per altre informazioni, vedere Convenzioni di denominazione di Azure per [i nomi BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i nomi di [file.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrizione errore:** I nomi BLOB o file contengono caratteri non validi.

**Risoluzione suggerita:** I BLOB o i file copiati contengono nomi con caratteri non supportati.

Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file di errore.
Rimuovere o rinominare i file per rimuovere i caratteri non supportati.

Per altre informazioni, vedere Convenzioni di denominazione di Azure per [i nomi BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i nomi di [file.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrizione errore:** I nomi BLOB o file terminano con caratteri non validi.

**Risoluzione suggerita:** I BLOB o i file copiati contengono nomi con caratteri non supportati.

Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file di errore.
Rimuovere o rinominare i file per rimuovere i caratteri non supportati.

Per altre informazioni, vedere Convenzioni di denominazione di Azure per [i nomi BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i nomi di [file.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrizione errore:** Il nome del BLOB o del file contiene troppi segmenti di percorso.

**Risoluzione suggerita:** I BLOB o i file copiati superano il numero massimo di segmenti di percorso. Un segmento di percorso è la stringa tra caratteri delimitatori consecutivi, ad esempio la barra /.

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file di errore.
- Assicurarsi che [i nomi dei BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i nomi di file siano conformi alle convenzioni di denominazione di Azure.Make sure that the blob names and file [names](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) conform to Azure naming conventions.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrizione errore:** Il BLOB o il nome del file è troppo lungo.

**Risoluzione suggerita:** Il BLOB o i nomi dei file superano la lunghezza massima.

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file di errore.
- Il nome del BLOB non deve superare i 1.024 caratteri.
- Rimuovere o rinominare il BLOB o i file in modo che i nomi non superino i 1024 caratteri.

Per altre informazioni, vedere Convenzioni di denominazione di Azure per i nomi BLOB e i nomi di file.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrizione errore:** Uno dei segmenti nel BLOB o nel nome del file è troppo lungo.

**Risoluzione suggerita:** Uno dei segmenti di percorso nel BLOB o nel nome file supera il numero massimo di caratteri. Un segmento di percorso è la stringa tra caratteri delimitatori consecutivi, ad esempio la barra /.

- Nella pagina **Connetti e copia** dell'interfaccia utente Web locale scaricare ed esaminare i file di errore.
- Assicurarsi che [i nomi dei BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e i nomi di file siano conformi alle convenzioni di denominazione di Azure.Make sure that the blob names and file [names](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) conform to Azure naming conventions.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrizione errore:** Per le condivisioni disco gestite vengono specificati nomi di contenitori non corretti.

**Risoluzione suggerita:** Per i dischi gestiti, all'interno di ogni condivisione, vengono create le cartelle seguenti che corrispondono ai contenitori nell'account di archiviazione: SSD Premium, HDD standard e SSD standard. Queste cartelle corrispondono al livello di prestazioni per il disco gestito.

- Assicurarsi di copiare i dati BLOB di pagina (VHD) in una di queste cartelle esistenti. Solo i dati di questi contenitori esistenti vengono caricati in Azure.Only data from these existing containers is uploaded to Azure.
- Qualsiasi altra cartella creata allo stesso livello di Premium SSD, Standard HDD e SSD Standard non corrisponde a un livello di prestazioni valido e non può essere utilizzata.
- Rimuovere file o cartelle creati all'esterno dei livelli di prestazioni.

Per ulteriori informazioni, consultate Copiare su [dischi gestiti.](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui requisiti di [sistema di archiviazione Data Box Blob](data-box-system-requirements-rest.md).
