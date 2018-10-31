---
title: Migrazione con tempo di inattività minimo a Database di Azure per PostgreSQL
description: Questo articolo descrive come eseguire una migrazione con tempo di inattività minimo di un database PostgreSQL in Database di Azure per PostgreSQL tramite Servizio Migrazione del database di Azure.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 80e5d30677735b35d90fda6288d7bf6f2ea4aa1b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093834"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migrazione con tempo di inattività minimo a Database di Azure per PostgreSQL
È possibile eseguire migrazioni di PostgreSQL in Database di Azure per PostgreSQL con tempo di inattività minimo usando la **funzionalità di sincronizzazione continua** appena introdotta per [Servizio Migrazione del database di Azure](https://aka.ms/get-dms). Questa funzionalità limita il tempo di inattività cui è soggetta l'applicazione.

## <a name="overview"></a>Panoramica
Servizio Migrazione del database esegue un caricamento iniziale di Database di Azure per PostgreSQL locale e quindi sincronizza continuamente tutte le nuove transazioni in Azure mentre l'applicazione resta in esecuzione. Dopo che i dati sono allineati sul lato Azure di destinazione, è necessario arrestare l'applicazione per un breve momento (tempo di inattività minimo), attendere l'allineamento dell'ultimo batch di dati (dal momento in cui è stata arrestata l'applicazione fino a quando l'applicazione non è effettivamente non disponibile per accettare nuovo traffico) nella destinazione e quindi aggiornare la stringa di connessione in modo da puntare ad Azure. Al termine, l'applicazione sarà live in Azure.

![Sincronizzazione continua con Servizio Migrazione del database di Azure](./media/howto-migrate-online/ContinuousSync.png)

La migrazione tramite Servizio Migrazione del database di Azure di origini PostgreSQL è attualmente in versione di anteprima. Se si vuole provare il servizio per eseguire la migrazione dei carichi di lavoro PostgreSQL, iscriversi tramite la [pagina di anteprima](https://aka.ms/dms-preview) di Servizio Migrazione del database di Azure per indicare il proprio interesse. Commenti e suggerimenti sono particolarmente utili per migliorare ulteriormente il servizio.

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare il video [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) (Modernizzazione delle app con Microsoft Azure), che contiene una demo che mostra come eseguire la migrazione di app PostgreSQL in Database di Azure per PostgreSQL.
- Vedere l'esercitazione [Eseguire la migrazione di PostgreSQL in Database di Azure per PostgreSQL online mediante il Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
