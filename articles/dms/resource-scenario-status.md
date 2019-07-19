---
title: Stato degli scenari di migrazione del database | Microsoft Docs
description: Informazioni sullo stato degli scenari di migrazione supportati dal servizio migrazione del database di Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/13/2019
ms.openlocfilehash: 44ac290a471fd0099b6589f84fea604249818432
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868607"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stato degli scenari di migrazione supportati dal servizio migrazione del database di Azure

Il servizio migrazione del database di Azure è progettato per supportare scenari di migrazione diversi (coppie di origine/destinazione) per migrazioni sia offline (monouso) che online (sincronizzazione continua). Il code coverage dello scenario fornito dal servizio migrazione del database di Azure viene esteso nel tempo. Nuovi scenari vengono aggiunti regolarmente. Questo articolo identifica gli scenari di migrazione attualmente supportati dal servizio migrazione del database di Azure e lo stato (anteprima privata, anteprima pubblica o disponibilità generale) per ogni scenario.

## <a name="offline-versus-online-migrations"></a>Migrazioni offline e online

Con il servizio migrazione del database di Azure è possibile eseguire una migrazione offline o online. Con le migrazioni *offline*, il tempo di inattività delle applicazioni inizia quando inizia la migrazione. Per limitare i tempi di inattività al tempo necessario per passare al nuovo ambiente al termine della migrazione, usare una migrazione in *linea* . È consigliabile testare una migrazione offline per determinare se il tempo di inattività è accettabile. in caso contrario, eseguire una migrazione in linea.

## <a name="migration-scenario-status"></a>Stato dello scenario di migrazione

Lo stato degli scenari di migrazione supportati dal servizio migrazione del database di Azure varia a seconda del tempo. In generale, gli scenari vengono inizialmente rilasciati in **Anteprima privata**. Per partecipare all'anteprima privata, è necessario che i clienti inviino una candidatura tramite il [sito di anteprima DMS](https://aka.ms/dms-preview). Dopo l'anteprima privata, lo stato dello scenario diventa **anteprima pubblica**. Gli utenti del servizio migrazione del database di Azure possono provare gli scenari di migrazione in anteprima pubblica direttamente dall'interfaccia utente. Non è necessaria alcuna iscrizione.  Tuttavia, gli scenari di migrazione in anteprima pubblica potrebbero non essere disponibili in tutte le aree e potrebbero subire ulteriori modifiche prima della versione finale. Dopo l'anteprima pubblica, lo stato dello scenario diventa disponibile a livello **generale**. Disponibilità generale (GA) è lo stato finale della versione e la funzionalità è completa e accessibile a tutti gli utenti.

## <a name="migration-scenario-support"></a>Supporto degli scenari di migrazione

Le tabelle seguenti illustrano gli scenari di migrazione supportati quando si usa il servizio migrazione del database di Azure.

> [!NOTE]
> Se uno scenario elencato come supportato di seguito non viene visualizzato nell'interfaccia utente, contattare l'alias [Ask migrazione del database di Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) per altre informazioni.

> [!IMPORTANT]
> Per visualizzare tutti gli scenari attualmente supportati dal servizio migrazione del database di Azure in anteprima privata, vedere il [sito di anteprima DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Supporto della migrazione offline (unica)

La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni offline.

| Destinazione  | Source | Supporto | Stato |
| ------------- | ------------- |:-------------:|:-------------:|
| **Database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL |  |  |
|   | Oracle |  |  |
| **Istanza gestita di database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL |  |  |
|   | Oracle |  |   |
| **VM di Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Database di Azure per MySQL** | MySQL |   |   |
|   | Servizi Desktop remoto MySQL |   |   |
| **Database di Azure per PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Supporto della migrazione online (sincronizzazione continua)

La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni online.

| Destinazione  | Source | Supporto | Stato |
| ------------- | ------------- |:-------------:|:-------------:|
| **Database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL | ✔ | GA |
|   | Oracle |  |  |
| **Istanza gestita di database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL | ✔ | GA |
|   | Oracle | ✔ | Anteprima privata |
| **VM di Azure SQL** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Database di Azure per MySQL** | MySQL | ✔ | GA |
|   | Servizi Desktop remoto MySQL | ✔ | GA |
| **Database di Azure per PostgreSQL** | PostgreSQL | ✔ | GA |
|   | Servizi Desktop remoto PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Anteprima privata |

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del servizio migrazione del database di Azure e della disponibilità a livello di area, vedere l'articolo [che cos'è il servizio migrazione del database di Azure](dms-overview.md).
