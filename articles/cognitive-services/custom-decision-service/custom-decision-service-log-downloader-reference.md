---
title: LogDownloader - Servizi cognitivi di Azure | Microsoft Docs
description: Scaricare i file di log generati dal Servizio decisionale personalizzato di Azure.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 783b534b3b3f4bb7f5d9f073f491690759edfea5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376964"
---
# <a name="logdownloader"></a>LogDownloader

Scaricare i file di log che vengono generati dal Servizio decisionale personalizzato di Azure e generare i file *.gz* da usare nelle sperimentazioni.

## <a name="prerequisites"></a>prerequisiti

- Python 3: installato e presente nel percorso. Si consiglia la versione a 64 bit per gestire file di grandi dimensioni.
- Il repository *Microsoft/mwt-ds*: [clonare il repository](https://github.com/Microsoft/mwt-ds).
- Il pacchetto *azure-storage-blob*: per informazioni dettagliate sull'installazione, passare alla [libreria di Archiviazione di Microsoft Azure per Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Immettere la stringa di connessione di archiviazione di Azure in *mwt-ds/DataScience/ds.config*: seguire il modello *my_app_id: my_connectionString*. È possibile specificare più `app_id`. Quando si esegue `LogDownloader.py`, se l'`app_id` di input non viene individuato in `ds.config`, `LogDownloader.py` usa la stringa di connessione `$Default`.

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
| `-a APP_ID`, `--app_id APP_ID` | L'ID app, vale a dire il nome del contenitore BLOB di Archiviazione di Azure. | Obbligatoria |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | La directory di base per il download dei dati: viene creata una sottocartella.  | Obbligatoria |
| `-s START_DATE`, `--start_date START_DATE` | La data di inizio del download, inclusa, nel formato *AAAA-MM-GG*. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | La data di fine del download, inclusa, nel formato *AAAA-MM-GG*. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | La modalità di sovrascrittura da usare. | |
| | `0`: non usare mai la sovrascrittura; chiedere all'utente se i BLOB sono attualmente in uso. | Predefinito | |
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