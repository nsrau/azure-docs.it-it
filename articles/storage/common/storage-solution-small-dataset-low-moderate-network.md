---
title: Opzioni di trasferimento dei dati di Azure per set di dati di piccole dimensioni con larghezza di banda di rete bassa o moderata | Microsoft Docs
description: Informazioni su come scegliere una soluzione di Azure per il trasferimento dei dati quando la larghezza di banda di rete è bassa o moderata nell'ambiente e si pianifica il trasferimento di set di dati di piccole dimensioni.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: b9c19c4cd85b1ab2bc3ea010d029361957de3943
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263656"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Trasferimento dei dati per set di dati di piccole dimensioni con larghezza di banda di rete da bassa a moderata
 
Questo articolo offre una panoramica delle soluzioni per il trasferimento dei dati quando la larghezza di banda di rete è bassa o moderata nell'ambiente e si pianifica il trasferimento di set di dati di piccole dimensioni. L'articolo descrive anche le opzioni di trasferimento dei dati consigliate e la rispettiva matrice di funzionalità chiave per questo scenario.

Per una panoramica di tutte le opzioni di trasferimento dei dati disponibili, vedere [Scegliere una soluzione di trasferimento dei dati di Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrizione dello scenario

Con set di dati di piccole dimensioni si intendono dati di dimensioni nell'ordine di GB o di pochi TB. La larghezza di banda di rete da bassa a moderata implica da 45 Mbps (connessione T3 nel data center) a 1 Gbps.

- Se si devono trasferire solo pochi file e non è necessario automatizzare il trasferimento dei dati, prendere in considerazione gli strumenti con un'interfaccia grafica.
- Se si conosce l'amministrazione del sistema, prendere in considerazione gli strumenti da riga di comando o quelli programmatici/di scripting.

## <a name="recommended-options"></a>Opzioni consigliate

Le opzioni consigliate in questo scenario sono:

- **Strumenti dell'interfaccia grafica**, ad esempio Azure Storage Explorer e Archiviazione di Azure nel portale di Azure, che consentono di visualizzare facilmente i dati e di trasferire rapidamente pochi file.

    - **Azure Storage Explorer**: questo strumento multipiattaforma consente di gestire il contenuto degli account di archiviazione di Azure. Con questo strumento è possibile caricare, scaricare e gestire BLOB, file, code, tabelle ed entità di Azure Cosmos DB. Usarlo con l'archiviazione BLOB per gestire BLOB e cartelle e anche per caricare e scaricare BLOB tra il file system locale e l'archiviazione BLOB o tra gli account di archiviazione.
    - **Portale di Azure**: Archiviazione di Azure nel portale di Azure fornisce un'interfaccia basata sul Web per esplorare i file e caricare i nuovi file uno alla volta. Si tratta di un'ottima scelta se non si vogliono installare strumenti o eseguire comandi per esplorare rapidamente i file o caricare contemporaneamente un certo numero di nuovi file.

- **Strumenti di scripting/programmatici**, ad esempio AzCopy/PowerShell/interfaccia della riga di comando di Azure e API REST di Archiviazione di Azure.

    - **AzCopy**: usare questo strumento da riga di comando per copiare facilmente i dati da e verso l'archivio BLOB, file e tabelle di Azure con prestazioni ottimali. AzCopy supporta la concorrenza e il parallelismo e consente di riprendere le operazioni di copia in caso di interruzione.
    - **Azure PowerShell**: gli utenti che conoscono l'amministrazione del sistema possono usare il modulo Archiviazione di Azure in Azure PowerShell per trasferire i dati.
    - **Interfaccia della riga di comando di Azure**: usare questo strumento multipiattaforma per gestire i servizi di Azure e caricare i dati in Archiviazione di Azure.
    - **API REST/SDK di Archiviazione di Azure**: quando si compila un'applicazione, è possibile sviluppare l'applicazione tramite le API REST o gli SDK di Archiviazione di Azure e usare le librerie client di Azure disponibili in più linguaggi.


## <a name="comparison-of-key-capabilities"></a>Confronto delle funzionalità chiave

La tabella seguente contiene un riepilogo delle differenze principali in termini di funzionalità.

| Funzionalità                                                            | Esplora archivi Azure                    | Portale di Azure  | AzCopy<br>Azure PowerShell<br>Interfaccia della riga di comando di Azure            | API REST o SDK di Archiviazione di Azure |
|----------------------------------------------|-------------------------------------------|--------------------------------------|-------------------|---------------------------------------|
| Disponibilità                                                       | Download e installazione <br>Strumento autonomo | Strumenti di esplorazione basati sul Web nel portale di Azure           | Strumento da riga di comando |Interfacce programmabili in .NET, Java, Python, JavaScript, C++, Go, Ruby e PHP                                                         |
| Interfaccia grafica                                              | Yes                                       | Sì                                                     | No                 | No                                                       |
| Piattaforme supportate                                              | Windows, Mac, Linux                       | Basate sul Web     |Windows, Mac, Linux     |Tutte le piattaforme                                                         |
| Operazioni di archiviazione BLOB consentite<br>per BLOB e cartelle            | Caricamento<br>Download<br>Gestisci    | Caricamento<br>Download<br>Gestisci  |Caricamento<br>Download<br>Gestisci             | Sì, personalizzabili                                                        |
| Operazioni di archiviazione di Data Lake Gen1<br>consentite per file e cartelle  | Caricamento<br>Download<br>Gestisci                | No  |Caricamento<br>Download<br>Gestisci                   |      No                                                    |
| Operazioni di archiviazione file consentite<br>per file e directory        | Caricamento<br>Download<br>Gestisci       | Caricamento<br>Download<br>Gestisci   |Caricamento<br>Download<br>Gestisci |                                                         |Sì, personalizzabili
| Operazioni di archiviazione tabelle consentite<br>per le tabelle                      |Gestisci                            | No             |Supporto delle tabelle in AzCopy v7 |Sì, personalizzabili|
| Archiviazione code consentita                                              | Gestisci                                    | No   |No  | Sì, personalizzabile|


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [trasferire dati con Azure Storage Explorer](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Trasferire dati con AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

