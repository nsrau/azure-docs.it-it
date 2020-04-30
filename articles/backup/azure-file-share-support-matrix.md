---
title: Matrice di supporto per il backup di condivisioni file di Azure
description: Fornisce un riepilogo delle impostazioni e delle limitazioni di supporto per il backup di condivisioni file di Azure.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: 3f154f92d8d094654301bc319003adc22299017b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207990"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matrice di supporto per il backup di condivisioni file di Azure

È possibile usare il [servizio backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) per eseguire il backup delle condivisioni file di Azure. Questo articolo riepiloga le impostazioni di supporto quando si esegue il backup di condivisioni file di Azure con backup di Azure.

## <a name="supported-geos"></a>GEOS supportato

Il backup per le condivisioni file di Azure è disponibile nelle aree geografiche seguenti:

| Aree GA | Aree supportate ma non GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Australia sud-orientale (ASE), Canada centrale (CNC), Stati Uniti centro-occidentali (WCUS), Stati Uniti occidentali 2 (WUS 2), India meridionale (INS), Stati Uniti centro-settentrionali (NCUS), Giappone orientale (JPE), Brasile meridionale (BRS), Sud Asia orientale (SEA), Svizzera occidentale (SZW), Emirati Arabi Uniti centrali (UAC), Norvegia orientale (nè), India occidentale (INW), Australia centrale (ACL)                                                  |Australia orientale (AE), Canada orientale (CE), Asia orientale (EA), Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Giappone occidentale (JPW), India centrale (INC), Corea centrale (KRC), Corea meridionale (KRS), Europa settentrionale (NE), Stati Uniti centro-meridionali (SCUS), Regno Unito meridionale (UKS), Regno Unito occidentale (UKW), Europa occidentale (Microsoft), Stati Uniti occidentali (WUS), US Gov Arizona (UGA), US Gov Texas (UGT), US Gov Virginia (UGV), Sudafrica settentrionale (SAN) , Emirati Arabi Uniti settentrionali (UAN), Francia centrale (FRC), Germania settentrionale (GN), Germania centro-occidentale (GWC), Sudafrica occidentale (SAW), Norvegia occidentale (NWW), Svizzera settentrionale (SZN), Stati Uniti centrali (CUS)           |

## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

| Dettagli dell'account di archiviazione | Supporto                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo di account            | Backup di Azure supporta le condivisioni file di Azure presenti negli account di archiviazione per utilizzo generico V1, per utilizzo generico V2 e tipo di archiviazione di file |
| Prestazioni              | Backup di Azure supporta le condivisioni file negli account di archiviazione standard e Premium |
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
| Numero massimo di account di archiviazione che possono essere registrati per ogni insieme di credenziali al giorno | 50    |

## <a name="backup-limits"></a>Limiti relativi a Backup

| Impostazione                                      | Limite |
| -------------------------------------------- | ----- |
| Numero massimo di backup su richiesta al giorno | 4     |
| Numero massimo di backup pianificati al giorno | 1     |

## <a name="restore-limits"></a>Limiti di ripristino

| Impostazione                                                      | Limite   |
| ------------------------------------------------------------ | ------- |
| Numero massimo di ripristini al giorno                           | 10      |
| Numero massimo di file per ripristino                         | 10      |
| Dimensione massima consigliata per il ripristino per le condivisioni file di grandi dimensioni | 15 TiB |

## <a name="retention-limits"></a>Limiti di conservazione

| Impostazione                                                      | Limite    |
| ------------------------------------------------------------ | -------- |
| Numero massimo di punti di ripristino per ogni condivisione file in un momento qualsiasi | 200      |
| Massimo periodo di conservazione del punto di ripristino creato dal backup su richiesta | 10 anni |
| Conservazione massima dei punti di ripristino giornalieri (snapshot) per ogni condivisione file| 200 giorni |
| Conservazione massima dei punti di ripristino settimanali (snapshot) per ogni condivisione file | 200 settimane |
| Conservazione massima dei punti di ripristino mensili (snapshot) per ogni condivisione file | 120 mesi |
| Conservazione massima dei punti di ripristino annuali (snapshot) per ogni condivisione file | 10 anni |

## <a name="supported-restore-methods"></a>Metodi di ripristino supportati

| Metodo Restore     | Dettagli                                                      |
| ------------------ | ------------------------------------------------------------ |
| Ripristino della condivisione completa | È possibile ripristinare la condivisione file completa nel percorso originale o in un percorso alternativo |
| Ripristino a livello di elemento | È possibile ripristinare singoli file e cartelle nel percorso originale o in un percorso alternativo |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [eseguire il backup delle condivisioni file di Azure](backup-afs.md)
* Informazioni su come [ripristinare le condivisioni file di Azure](restore-afs.md)
* Informazioni su come [gestire i backup di condivisioni file di Azure](manage-afs-backup.md)
