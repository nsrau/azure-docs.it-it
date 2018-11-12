---
title: Panoramica del Servizio Migrazione del database di Azure | Microsoft Docs
description: Panoramica del Servizio Migrazione del database di Azure, che offre migrazioni senza interruzioni da diverse origini di database alle piattaforme di dati di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 053e571b6285cd405ea17f43fec1d3ea99732070
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235582"
---
# <a name="what-is-the-azure-database-migration-service"></a>Definizione del Servizio Migrazione del database di Azure
Il Servizio Migrazione del database di Azure è un servizio completamente gestito progettato per consentire migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi (migrazioni online).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Eseguire la migrazione dei database in Azure con strumenti familiari
Il Servizio Migrazione del database di Azure integra alcune delle funzionalità degli strumenti e dei servizi esistenti. Offre ai clienti una soluzione a disponibilità elevata completa. Il servizio usa il [Data Migration Assistant](https://aka.ms/dma) per generare report di valutazione che forniscono indicazioni che consentono di eseguire le modifiche necessarie prima di una migrazione. È compito dell'utente eseguire le correzioni necessarie. Quando si è pronti per iniziare il processo di migrazione, il Servizio Migrazione del database di Azure esegue tutti i passaggi necessari. È possibile avviare i progetti di migrazione con tranquillità sapendo che il processo si avvale delle procedure consigliate stabilite da Microsoft.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Business critical (anteprima).

## <a name="regional-availability"></a>Disponibilità internazionale
Il Servizio Migrazione del database di Azure è attualmente disponibile nelle aree seguenti:

![Disponibilità a livello di area del Servizio Migrazione del database di Azure](media\overview\dms-regional-availability1.png)

Per informazioni aggiornate sulla disponibilità a livello di area del Servizio Migrazione del database di Azure, nel sito di infrastruttura globale di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>Passaggi successivi
- [Creare un'istanza del Servizio Migrazione del database di Azure usando il portale di Azure](quickstart-create-data-migration-service-portal.md).
- [Eseguire la migrazione di SQL Server nel database SQL di Azure](tutorial-sql-server-to-azure-sql.md).
- [Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure](pre-reqs.md).
- [Domande frequenti sull'uso del Servizio Migrazione del database di Azure](faq.md).
