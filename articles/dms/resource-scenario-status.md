---
title: Stato dello scenario di migrazione del database
titleSuffix: Azure Database Migration Service
description: Informazioni sullo stato degli scenari di migrazione supportati dal servizio migrazione del database di Azure.Learn about the status of the migration scenarios supported by Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254921"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stato degli scenari di migrazione supportati dal servizio di migrazione del database di AzureStatus of migration scenarios supported by Azure Database Migration Service

Il servizio Migrazione del database di Azure è progettato per supportare diversi scenari di migrazione (coppie di origine/destinazione) per le migrazioni offline (una tantera) e online (sincronizzazione continua). La copertura dello scenario fornita dal servizio di migrazione del database di Azure viene estesa nel tempo. Nuovi scenari vengono aggiunti regolarmente. Questo articolo identifica gli scenari di migrazione attualmente supportati dal servizio di migrazione del database di Azure e lo stato (anteprima privata, anteprima pubblica o disponibilità generale) per ogni scenario.

## <a name="offline-versus-online-migrations"></a>Migrazioni offline e online

Con il servizio Migrazione del database di Azure è possibile eseguire una migrazione offline o online. Con le migrazioni *offline*, il tempo di inattività delle applicazioni inizia quando inizia la migrazione. Per limitare i tempi di inattività al tempo necessario per ridurre il nuovo ambiente al termine della migrazione, utilizzare una migrazione *online.* È consigliabile testare una migrazione offline per determinare se il tempo di inattività è accettabile. in caso contrario, eseguire una migrazione online.

## <a name="migration-scenario-status"></a>Stato dello scenario di migrazione

Lo stato degli scenari di migrazione supportati dal servizio Migrazione del database di Azure varia in base al tempo. In genere, gli scenari vengono rilasciati per la prima volta in **anteprima privata.** La partecipazione all'anteprima privata richiede ai clienti di inviare una candidatura tramite il [sito DMS Preview.](https://aka.ms/dms-preview) Dopo l'anteprima privata, lo stato dello scenario diventa **anteprima pubblica**. Gli utenti del servizio Migrazione del database di Azure possono provare gli scenari di migrazione in anteprima pubblica direttamente dall'interfaccia utente. Non è richiesta alcuna iscrizione.  Tuttavia, gli scenari di migrazione nell'anteprima pubblica potrebbero non essere disponibili in tutte le aree geografiche e potrebbero subire ulteriori modifiche prima della versione finale. Dopo l'anteprima pubblica, lo stato dello scenario diventa **generalmente disponibile.** La disponibilità generale (GA) è lo stato di rilascio finale e la funzionalità è completa e accessibile a tutti gli utenti.

## <a name="migration-scenario-support"></a>Supporto degli scenari di migrazione

Le tabelle seguenti mostrano quali scenari di migrazione sono supportati quando si usa il servizio di migrazione del database di Azure.The following tables show which migration scenarios are supported when using Azure Database Migration Service.

> [!NOTE]
> Se uno scenario elencato di seguito non viene visualizzato nell'interfaccia utente, contattare l'alias [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) per ulteriori informazioni.

> [!IMPORTANT]
> Per visualizzare tutti gli scenari attualmente supportati dal servizio di migrazione del database di Azure in anteprima privata, vedere il [sito DMS Preview](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Supporto della migrazione offline (unica)

La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni offline.

| Destinazione  | Source (Sorgente) | Supporto | Stato |
| ------------- | ------------- |:-------------:|:-------------:|
| **Database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL |  |  |
|   | Oracle |  |  |
| **Istanza gestita del database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL |  |  |
|   | Oracle |  |   |
| **VM di Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Database di Azure per MySQL** | MySQL |   |   |
|   | Servizi Desktop remoto MySQL |   |   |
| **Database di Azure per PostgreSQL** | PostgreSQL |  |
|  | Servizi Desktop remoto PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Supporto della migrazione online (sincronizzazione continua)

La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni online.

| Destinazione  | Source (Sorgente) | Supporto | Stato |
| ------------- | ------------- |:-------------:|:-------------:|
| **Database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL | ✔ | GA |
|   | Oracle |  |  |
| **Istanza gestita del database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL | ✔ | GA |
|   | Oracle | ✔ | Anteprima privata |
| **VM di Azure SQL** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Database di Azure per MySQL** | MySQL | ✔ | GA |
|   | Servizi Desktop remoto MySQL | ✔ | GA |
| **Database di Azure per PostgreSQL** | PostgreSQL | ✔ | GA |
|   | Servizi Desktop remoto PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Anteprima pubblica |

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del servizio migrazione del database di Azure e della disponibilità regionale, vedere l'articolo [Che cos'è il servizio Migrazione del database](dms-overview.md)di Azure.For an overview of Azure Database Migration Service and regional availability, see the article What is the Azure Database Migration Service .
