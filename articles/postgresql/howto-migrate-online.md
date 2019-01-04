---
title: Migrazione con tempo di inattività minimo a Database di Azure per PostgreSQL
description: Questo articolo descrive come eseguire una migrazione con tempo di inattività minimo di un database PostgreSQL in Database di Azure per PostgreSQL tramite Servizio Migrazione del database di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: ceb64781dc7e5243f785ad239c24e5f21b0481ce
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543649"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migrazione con tempo di inattività minimo a Database di Azure per PostgreSQL
È possibile eseguire migrazioni di PostgreSQL in Database di Azure per PostgreSQL con tempo di inattività minimo usando la **funzionalità di sincronizzazione continua** appena introdotta per [Servizio Migrazione del database di Azure](https://aka.ms/get-dms). Questa funzionalità limita il tempo di inattività cui è soggetta l'applicazione.

## <a name="overview"></a>Panoramica
Servizio Migrazione del database di Azure esegue un caricamento iniziale di Database di Azure per PostgreSQL locale e quindi sincronizza continuamente tutte le nuove transazioni in Azure mentre l'applicazione resta in esecuzione. Dopo che i dati sono allineati sul lato Azure di destinazione, è necessario arrestare l'applicazione per un breve momento (tempo di inattività minimo), attendere l'allineamento dell'ultimo batch di dati (dal momento in cui è stata arrestata l'applicazione fino a quando l'applicazione non è effettivamente non disponibile per accettare nuovo traffico) nella destinazione e quindi aggiornare la stringa di connessione in modo da puntare ad Azure. Al termine, l'applicazione sarà live in Azure.

![Sincronizzazione continua con Servizio Migrazione del database di Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare il video [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) (Modernizzazione delle app con Microsoft Azure), che contiene una demo che mostra come eseguire la migrazione di app PostgreSQL in Database di Azure per PostgreSQL.
- Vedere l'esercitazione [Eseguire la migrazione di PostgreSQL in Database di Azure per PostgreSQL online mediante il Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
