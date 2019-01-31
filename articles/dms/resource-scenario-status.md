---
title: Stato degli scenari di migrazione del database | Microsoft Docs
description: Informazioni sullo stato degli scenari di migrazione supportati dal Servizio Migrazione del database di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: edc6e651c3ec352115e360e50f98a3e36cd287c0
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904081"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Stato degli scenari di migrazione supportati dal Servizio Migrazione del database di Azure
Il Servizio Migrazione del database di Azure è progettato per supportare una varietà di scenari di migrazione (coppie origine/destinazione) per le migrazioni sia offline (uniche) che online (sincronizzazione continua). Gli scenari supportati dal Servizio Migrazione del database di Azure aumentano con il tempo. Nuovi scenari vengono aggiunti regolarmente. Questo articolo identifica gli scenari di migrazione attualmente supportati dal Servizio Migrazione del database di Azure e lo stato (Anteprima privata [o limitata], Anteprima pubblica o Disponibile a livello generale) per ogni scenario.

## <a name="offline-versus-online-migrations"></a>Migrazioni offline e online
Quando si esegue la migrazione di database in Azure con il Servizio Migrazione del database di Azure, è possibile eseguire una migrazione offline oppure online. Con le migrazioni *offline*, il tempo di inattività delle applicazioni inizia quando inizia la migrazione. Per le migrazioni *online*, il tempo di inattività è limitato al tempo necessario per il trasferimento nel nuovo ambiente al completamento della migrazione. È consigliabile testare una migrazione offline per determinare se il tempo di inattività è accettabile. In caso contrario, eseguire una migrazione online.

## <a name="migration-scenario-status"></a>Stato dello scenario di migrazione
Lo stato di ogni scenario di migrazione supportato dal Servizio Migrazione del database di Azure varia nel tempo. Generalmente gli scenari vengono dapprima rilasciati in **Anteprima privata** e per sfruttarne le funzionalità il cliente deve inviare una candidatura tramite il [sito dell'anteprima del Servizio Migrazione del database](https://aka.ms/dms-preview). Una volta completata l'Anteprima privata, lo stato dello scenario diventa **Anteprima pubblica**. Tutti gli utenti del Servizio Migrazione del database di Azure possono usufruire degli scenari di migrazione disponibili in Anteprima pubblica. Tuttavia, lo scenario di migrazione potrebbe non essere disponibile in tutte le aree e la funzionalità potrebbe subire ulteriori modifiche prima del rilascio finale. Quando lo stato di uno scenario di migrazione passa a **Disponibile a livello generale**, ossia lo stato di rilascio finale, la funzionalità è completa e accessibile a tutti gli utenti del Servizio Migrazione del database di Azure. 

## <a name="migration-scenario-support"></a>Supporto degli scenari di migrazione

Le tabelle seguenti mostrano gli scenari di migrazione supportati quando si usa il Servizio Migrazione del database di Azure.

> [!NOTE]
> Se uno scenario elencato come supportato non è visualizzato nell'interfaccia utente, contattare il [team di migrazione dati](mailto:datamigrationteam@microsoft.com) per altre informazioni.

### <a name="offline-one-time-migration-support"></a>Supporto della migrazione offline (unica)
La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni offline.

| Destinazione  | Source (Sorgente) | Supporto |
| ------------- | ------------- | :-------------: |
| **Database SQL di Azure**  | SQL Server | ✔ |
|   | RDS SQL  |  ✔ |
|   | Oracle  |   |
| **Istanza gestita di database SQL di Azure**  | SQL Server  | ✔ |
|   | RDS SQL  | ✔ |
|   | Oracle  | ✔  |
| **VM di Azure SQL**  | SQL Server | ✔ |
|   | Oracle  |   |
| **Azure Cosmos DB**  | MongoDB | ✔ |
| **Database di Azure per MySQL**  | MySQL |  |
|   | Servizi Desktop remoto MySQL  |  |
| **Database di Azure per PostgreSQL**  | PostgreSQL |  |
|  | RDS PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>Supporto della migrazione online (sincronizzazione continua)
La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni online.

| Destinazione  | Source (Sorgente) | Supporto |
| ------------- | ------------- | :-------------: |
| **Database SQL di Azure**  | SQL Server | ✔ |
|   | RDS SQL  |   |
|   | Oracle  |  ✔ |
| **Istanza gestita di database SQL di Azure**  | SQL Server  | ✔ |
|   | RDS SQL  |  |
|   | Oracle  | ✔  |
| **VM di Azure SQL**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Azure Cosmos DB**  | MongoDB  | ✔ |
| **Database di Azure per MySQL**  | MySQL | ✔ |
|   | RDS MySQL  | ✔ |
| **Database di Azure per PostgreSQL**  | PostgreSQL | ✔ |
|  | RDS PostgreSQL  | ✔ |

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica del Servizio Migrazione del database di Azure e informazioni sulla disponibilità a livello di area, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](dms-overview.md). 
