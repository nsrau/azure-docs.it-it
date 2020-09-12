---
title: Matrice di supporto per il backup di condivisioni file di Azure
description: Informazioni riepilogative su impostazioni e limitazioni del supporto per il backup di condivisioni file di Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 6381170df93fdf52c2d0dc7059ad47bbff734025
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378033"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matrice di supporto per il backup di condivisioni file di Azure

Per eseguire il backup di condivisioni file di Azure è possibile usare il [servizio Backup di Azure](./backup-overview.md). Questo articolo riepiloga le impostazioni di supporto quando si esegue il backup di condivisioni file di Azure con Backup di Azure.

## <a name="supported-regions"></a>Aree supportate

### <a name="ga-regions-for-azure-file-shares-backup"></a>Aree GA per il backup di condivisioni file di Azure

Il backup delle condivisioni file di Azure è disponibile in tutte le aree **ad eccezione** di: Germania centrale (sovrano), Germania nord-orientale (sovrano), Cina orientale, Cina orientale 2, Cina settentrionale, Cina settentrionale 2, US gov Iowa

## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

| Dettagli dell'account di archiviazione | Supporto                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo di account            | Backup di Azure supporta le condivisioni file di Azure presenti negli account di archiviazione per utilizzo generico V1, utilizzo generico V2 e tipo di archiviazione file |
| Prestazioni              | Backup di Azure supporta le condivisioni file negli account di archiviazione Standard e Premium |
| Replica              | Sono supportate le condivisioni file di Azure negli account di archiviazione con qualsiasi tipo di replica |
| Firewall abilitato         | Le condivisioni file di Azure negli account di archiviazione con regole del firewall che consentono ai servizi Microsoft Azure di accedere all'account di archiviazione sono supportate|

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
| Numero massimo di condivisioni file che possono essere protette per ogni insieme di credenziali al giorno| 200   |
| Numero massimo di account di archiviazione che possono essere registrati al giorno per ogni insieme di credenziali | 50    |
| Numero massimo di condivisioni file che possono essere protette per ogni insieme di credenziali | 2000   |
| Numero massimo di account di archiviazione che è possibile registrare per ogni insieme di credenziali | 200   |

## <a name="backup-limits"></a>Limiti relativi a Backup

| Impostazione                                      | Limite |
| -------------------------------------------- | ----- |
| Numero massimo di backup su richiesta al giorno | 10   |
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
