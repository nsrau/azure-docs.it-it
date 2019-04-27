---
title: Stato degli scenari di migrazione del database | Microsoft Docs
description: Informazioni sullo stato degli scenari di migrazione supportato dal servizio migrazione del Database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/04/2019
ms.openlocfilehash: 4159b2e7af83030f46d5aca150ef99a1380e711f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473009"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stato degli scenari di migrazione supportato dal servizio migrazione del Database di Azure
Servizio migrazione del Database di Azure è progettato per supportare diversi scenari di migrazione (coppie origine/destinazione) sia per offline (una tantum) e le migrazioni online (sincronizzazione continua). Il code coverage scenario fornito dal servizio migrazione del Database viene esteso nel corso del tempo. Nuovi scenari vengono aggiunti regolarmente. Questo articolo identifica gli scenari di migrazione attualmente supportati dal servizio migrazione del Database e dello stato (anteprima privata, anteprima pubblica o disponibilità generale) per ogni scenario.

## <a name="offline-versus-online-migrations"></a>Migrazioni offline e online
Con servizio migrazione del Database di Azure, è possibile eseguire una migrazione online o offline. Con le migrazioni *offline*, il tempo di inattività delle applicazioni inizia quando inizia la migrazione. Per limitare il tempo di inattività per il tempo necessario per trasferire gli nel nuovo ambiente al termine della migrazione, usare un *online* migrazione. Si consiglia di testare una migrazione offline per determinare se il tempo di inattività è accettabile. in caso contrario, eseguire una migrazione online.

## <a name="migration-scenario-status"></a>Stato dello scenario di migrazione
Lo stato degli scenari di migrazione supportato dal servizio migrazione del Database varia nel tempo. In generale, gli scenari vengono rilasciati inizialmente con **anteprima privata**. Che fanno parte di anteprima privata richiede ai clienti di inviare una candidatura tramite il [sito di anteprima DMS](https://aka.ms/dms-preview). Dopo l'anteprima privata, lo stato di uno scenario diventa **versione di anteprima pubblica**. Gli utenti servizio migrazione del Database di Azure è possono provare in scenari di migrazione in anteprima pubblica direttamente dall'interfaccia utente. Nessun abbonamento è obbligatorio.  Scenari di migrazione in anteprima pubblica, tuttavia, potrebbero non essere disponibili in tutte le aree e possono subire modifiche aggiuntive prima della versione finale. Dopo la versione di anteprima pubblica, lo stato di uno scenario diventa **disponibilità a livello generale**. Disponibilità generale (GA) è lo stato della versione finale e la funzionalità è completa e accessibile a tutti gli utenti.

## <a name="migration-scenario-support"></a>Supporto degli scenari di migrazione
Le tabelle seguenti illustrano gli scenari di migrazione supportati quando si usare servizio migrazione del Database di Azure.

> [!NOTE]
> Se uno scenario elencato come supportato non è visualizzato nell'interfaccia utente, contattare il [team di migrazione dati](mailto:datamigrationteam@microsoft.com) per altre informazioni.

> [!IMPORTANT]
> Per visualizzare tutti gli scenari attualmente supportati dal servizio migrazione del Database di Azure in anteprima privata, vedere la [sito di anteprima DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Supporto della migrazione offline (unica)
La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni offline.

| Destinazione  | Source (Sorgente) | Supporto | Stato |
| ------------- | ------------- |:-------------:|:-------------:|
| **Database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL |  |  |
|   | Oracle |  |  |
| **Istanza gestita di database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL |  |  |
|   | Oracle |  |   |
| **VM di Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Anteprima pubblica |
| **Database di Azure per MySQL** | MySQL |   |   |
|   | Servizi Desktop remoto MySQL |   |   |
| **Database di Azure per PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Supporto della migrazione online (sincronizzazione continua)
La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni online.

| Destinazione  | Source (Sorgente) | Supporto | Stato |
| ------------- | ------------- |:-------------:|:-------------:|
| **Database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL | ✔ | GA |
|   | Oracle |  |  |
| **Istanza gestita di database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL | ✔ | GA |
|   | Oracle | ✔ | Anteprima privata |
| **VM di Azure SQL** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Anteprima pubblica |
| **Database di Azure per MySQL** | MySQL | ✔ | GA |
|   | Servizi Desktop remoto MySQL | ✔ | GA |
| **Database di Azure per PostgreSQL** | PostgreSQL | ✔ | GA |
|   | Servizi Desktop remoto PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Anteprima privata |

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica del servizio migrazione del Database e la disponibilità a livello di area, vedere l'articolo [che cos'è il servizio migrazione del Database](dms-overview.md).
