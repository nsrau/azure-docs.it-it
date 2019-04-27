---
title: LogDownloader - Servizio decisionale personalizzato
titlesuffix: Azure Cognitive Services
description: Scaricare i file di log generati dal Servizio decisionale personalizzato di Azure.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 8a8f669c33f40fb80dc826ec04203880dee74d82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829991"
---
# <a name="logdownloader"></a>LogDownloader

Scaricare i file di log che vengono generati dal Servizio decisionale personalizzato di Azure e generare i file *.gz* da usare nelle sperimentazioni.

## <a name="prerequisites"></a>Prerequisiti

- Python 3: installato e presente nel percorso. Si consiglia la versione a 64 bit per gestire file di grandi dimensioni.
- Repository *Microsoft/mwt-ds*: [clonare il repository](https://github.com/Microsoft/mwt-ds).
- Pacchetto *azure-storage-blob*: per informazioni dettagliate sull'installazione, passare alla [libreria di Archiviazione di Microsoft Azure per Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Immettere la stringa di connessione di Archiviazione di Azure in *mwt-ds/DataScience/ds.config*: seguire il modello *ID_app: stringa_di_connessione*. È possibile specificare più `app_id`. Quando si esegue `LogDownloader.py`, se l'`app_id` di input non viene individuato in `ds.config`, `LogDownloader.py` usa la stringa di connessione `$Default`.

## <a name="usage"></a>Uso

Passare a `mwt-ds/DataScience` ed eseguire `LogDownloader.py` con gli argomenti pertinenti, come indicato in dettaglio nel codice seguente:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parametri

| Input | DESCRIZIONE | Predefinito |
| --- | --- | --- |
| `-h`, `--help` | Mostrare il messaggio della Guida e uscire. | |
| `-a APP_ID`, `--app_id APP_ID` | L'ID app, vale a dire il nome del contenitore BLOB di Archiviazione di Azure. | Obbligatorio |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | La directory di base per il download dei dati: viene creata una sottocartella.  | Obbligatorio |
| `-s START_DATE`, `--start_date START_DATE` | La data di inizio del download, inclusa, nel formato *AAAA-MM-GG*. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | La data di fine del download, inclusa, nel formato *AAAA-MM-GG*. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | La modalità di sovrascrittura da usare. | |
| | `0`: non usare mai la sovrascrittura; chiedere all'utente se i BLOB sono attualmente in uso. | Predefinito |
| | `1`: chiedere all'utente come procedere quando i file hanno dimensioni diverse o quando i BLOB sono attualmente in uso. | |
| | `2`: usare sempre la sovrascrittura; scaricare i BLOB attualmente in uso. | |
| | `3`: non usare mai la sovrascrittura e accodare se le dimensioni sono maggiori, senza chiedere; scaricare i BLOB attualmente in uso. | |
| | `4`: non usare mai la sovrascrittura e accodare se le dimensioni sono maggiori, senza chiedere; saltare i BLOB attualmente in uso. | |
| `--dry_run` | Stampare quali BLOB si sarebbero dovuti scaricare, senza scaricarli. | `False` |
| `--create_gzip` | Creare un file *gzip* per Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | L'intervallo di tempo, espresso in secondi, per rilevare se un file è attualmente in uso. | `3600` sec (`1` ore) |
| `--verbose` | Stampare altri dettagli. | `False` |
| `-v VERSION`, `--version VERSION` | La versione del downloader di log da usare. | |
| | `1`: per i log uncooked, solo per la compatibilità con le versioni precedenti. | Funzionalità deprecate |
| | `2`: per i log cooked. | Predefinito |

### <a name="examples"></a>Esempi

Per una prova del download di tutti i dati nel contenitore BLOB di Archiviazione di Azure, usare il codice seguente:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Per scaricare solo i log creati dopo il 1° gennaio 2018 con `overwrite_mode=4`, usare il codice seguente:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Per creare un file *gzip* che unisce tutti i file scaricati, usare il codice seguente:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
