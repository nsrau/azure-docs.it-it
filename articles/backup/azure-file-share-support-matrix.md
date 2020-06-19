---
title: Matrice di supporto per il backup di condivisioni file di Azure
description: Informazioni riepilogative su impostazioni e limitazioni del supporto per il backup di condivisioni file di Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.openlocfilehash: 42578cc83ef193801fa700ec7d136385411e5f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684634"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matrice di supporto per il backup di condivisioni file di Azure

Per eseguire il backup di condivisioni file di Azure è possibile usare il [servizio Backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview). Questo articolo riepiloga le impostazioni di supporto quando si esegue il backup di condivisioni file di Azure con Backup di Azure.

## <a name="supported-geos"></a>Aree geografiche supportate

Il backup di condivisioni file di Azure è disponibile nelle aree geografiche seguenti:

**Regioni di disponibilità generale**:<br>
Australia sud-orientale (ASE), Canada centrale (CNC), Stati Uniti centro-occidentali (WCUS), Stati Uniti centro-meridionali (SCUS), Stati Uniti occidentali 2 (WUS 2), India meridionale (INS), Stati Uniti centro-settentrionali (NCUS), Giappone orientale (JPE), Brasile meridionale (BRS), Asia sud-orientale (SEA), Svizzera occidentale (SZW), Emirati Arabi Uniti centrali (UAC), Norvegia orientale (NWE), India occidentale (INW), Australia centrale (ACL), Corea centrale (KRC), Giappone occidentale (JPW), Sudafrica settentrionale (SAN), Regno Unito meridionale (UKS), Regno Unito occidentale (UKW), Corea meridionale (KRS), Europa settentrionale (NE), Germania settentrionale (GN), Norvegia occidentale (NWW), Sudafrica occidentale (SAW), Svizzera settentrionale (SZN), Germania centro-occidentale (GWC), Emirati Arabi Uniti settentrionali (UAN), Francia centrale (FRC), India centrale (INC), Canada orientale (CNE), Asia orientale (EA), Australia orientale (AE), Stati Uniti centrali (CUS), Stati Uniti occidentali (WUS), US Gov Arizona (UGA), US Gov Texas (UGT), US Gov Virginia (UGV), US DoD (area centrale) (UDC), US DoD (area orientale) (UDE)

**Aree supportate (come parte dell'anteprima) ma non ancora con disponibilità generale**:<br>
Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Europa occidentale (WE)

## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

| Dettagli dell'account di archiviazione | Supporto                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo di account            | Backup di Azure supporta le condivisioni file di Azure presenti negli account di archiviazione per uso generico V1, per uso generico V2 e account di archiviazione di tipo archiviazione file |
| Prestazioni              | Backup di Azure supporta le condivisioni file negli account di archiviazione Standard e Premium |
| Replica              | Sono supportate le condivisioni file di Azure negli account di archiviazione con qualsiasi tipo di replica |

## <a name="supported-file-shares"></a>Condivisioni file supportate

| Tipo di condivisione file                                   | Supporto   |
| -------------------------------------------------- | --------- |
| Standard                                           | Supportato |
| large                                              | Supportato |
| Premium                                            | Supportato |
| Condivisioni file connesse al servizio sincronizzazione file di Azure | Supportato |

## <a name="protection-limits"></a>Limiti di protezione

| Impostazione                                                      | Limite |
| ------------------------------------------------------------ | ----- |
| Numero massimo di condivisioni file che possono essere protette al giorno per ogni insieme di credenziali | 200   |
| Numero massimo di account di archiviazione che possono essere registrati al giorno per ogni insieme di credenziali | 50    |

## <a name="backup-limits"></a>Limiti relativi a Backup

| Impostazione                                      | Limite |
| -------------------------------------------- | ----- |
| Numero massimo di backup su richiesta al giorno | 4     |
| Numero massimo di backup pianificati al giorno | 1     |

## <a name="restore-limits"></a>Limiti di ripristino

| Impostazione                                                      | Limite   |
| ------------------------------------------------------------ | ------- |
| Numero massimo di ripristini al giorno                           | 10      |
| Numero massimo di file per ogni ripristino                         | 10      |
| Dimensioni massime consigliate per il ripristino per le condivisioni file di grandi dimensioni | 15 TiB |

## <a name="retention-limits"></a>Limiti di conservazione

| Impostazione                                                      | Limite    |
| ------------------------------------------------------------ | -------- |
| Numero massimo di punti di ripristino per ogni condivisione file in un momento qualsiasi | 200      |
| Periodo massimo di conservazione del punto di ripristino creato da backup su richiesta | 10 anni |
| Conservazione massima dei punti di ripristino giornalieri (snapshot) per ogni condivisione file| 200 giorni |
| Conservazione massima dei punti di ripristino settimanali (snapshot) per ogni condivisione file | 200 settimane |
| Conservazione massima dei punti di ripristino mensili (snapshot) per ogni condivisione file | 120 mesi |
| Conservazione massima dei punti di ripristino annuali (snapshot) per ogni condivisione file | 10 anni |

## <a name="supported-restore-methods"></a>Metodi di ripristino supportati

| Metodo di ripristino     | Dettagli                                                      |
| ------------------ | ------------------------------------------------------------ |
| Ripristino completo della condivisione | È possibile ripristinare una condivisione file completa nel percorso originale o in uno alternativo |
| Ripristino a livello di elemento | È possibile ripristinare singoli file e cartelle nel percorso originale o in un percorso alternativo |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [Eseguire il backup di condivisioni file di Azure](backup-afs.md)
* Informazioni su come [Eseguire il ripristino di condivisioni file di Azure](restore-afs.md)
* Informazioni su come [Gestire i backup di condivisioni file di Azure](manage-afs-backup.md)
