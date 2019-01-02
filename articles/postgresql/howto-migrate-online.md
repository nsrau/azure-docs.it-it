---
title: Migrazione con tempo di inattività minimo a Database di Azure per PostgreSQL
description: Questo articolo descrive come eseguire una migrazione con tempo di inattività minimo di un database PostgreSQL in Database di Azure per PostgreSQL tramite Servizio Migrazione del database di Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 0c8c3443a19c26dade9699560e883969d3c074df
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53010842"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migrazione con tempo di inattività minimo a Database di Azure per PostgreSQL
È possibile eseguire migrazioni di PostgreSQL in Database di Azure per PostgreSQL con tempo di inattività minimo usando la **funzionalità di sincronizzazione continua** appena introdotta per [Servizio Migrazione del database di Azure](https://aka.ms/get-dms). Questa funzionalità limita il tempo di inattività cui è soggetta l'applicazione.

## <a name="overview"></a>Panoramica
Servizio Migrazione del database di Azure esegue un caricamento iniziale di Database di Azure per PostgreSQL locale e quindi sincronizza continuamente tutte le nuove transazioni in Azure mentre l'applicazione resta in esecuzione. Dopo che i dati sono allineati sul lato Azure di destinazione, è necessario arrestare l'applicazione per un breve momento (tempo di inattività minimo), attendere l'allineamento dell'ultimo batch di dati (dal momento in cui è stata arrestata l'applicazione fino a quando l'applicazione non è effettivamente non disponibile per accettare nuovo traffico) nella destinazione e quindi aggiornare la stringa di connessione in modo da puntare ad Azure. Al termine, l'applicazione sarà live in Azure.

![Sincronizzazione continua con Servizio Migrazione del database di Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare il video [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) (Modernizzazione delle app con Microsoft Azure), che contiene una demo che mostra come eseguire la migrazione di app PostgreSQL in Database di Azure per PostgreSQL.
- Vedere l'esercitazione [Eseguire la migrazione di PostgreSQL in Database di Azure per PostgreSQL online mediante il Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
