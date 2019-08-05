---
title: 'Guida introduttiva: Database singoli in Database SQL di Azure | Microsoft Docs'
description: Informazioni su come iniziare rapidamente a usare i database singoli in Database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: 45b0b2bc7a2ef5c3cb6ca801668f7b5be7c8ac73
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639987"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Introduzione ai database singoli in Database SQL di Azure

Un [database singolo](sql-database-single-index.yml) è un database PaaS distribuito come servizio (DbaaS) completamente gestito, ideale da usare come motore di archiviazione per le moderne applicazioni native del cloud. In questa sezione verrà illustrato come configurare e creare rapidamente un database singolo nel database SQL.

## <a name="quickstart-overview"></a>Panoramica della guida introduttiva

Questa sezione contiene una panoramica degli articoli disponibili da consultare per iniziare rapidamente a lavorare con i database singoli. Le guide introduttive seguenti consentono di creare rapidamente un database singolo, configurare una regola del firewall per il server di database e quindi importare un database nel nuovo database singolo usando un file `.bacpac`:

- [Creare un database singolo usando il portale di Azure](sql-database-single-database-get-started.md).
- Dopo averlo creato, è necessario [proteggere il database configurando le regole del firewall](sql-database-server-level-firewall-rule.md).
- Se è già disponibile un database in SQL Server di cui si vuole eseguire la migrazione ad Azure, è necessario installare [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), che analizzerà i database in SQL Server per rilevare eventuali problemi che potrebbero impedire la migrazione all'opzione di distribuzione del database singolo. Se non vengono rilevati problemi, è possibile esportare il database come file `.bacpac` e [importarlo usando il portale di Azure o SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automazione delle operazioni di gestione

È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per creare, configurare e ridimensionare il database.

- [Creare e configurare un database singolo con PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
- [Creare e configurare un database singolo con l'interfaccia della riga di comando di Azure](scripts/sql-database-create-and-configure-database-cli.md)
- [Aggiornare il database singolo e ridimensionare le risorse con PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Aggiornare il database singolo e ridimensionare le risorse con l'interfaccia della riga di comando di Azure](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrazione a un database singolo con tempo di inattività minimo

Queste guide introduttive consentono di creare o importare rapidamente il database in Azure usando un file `.bacpac`. Tuttavia, i file`.bacpac` e `.dacpac` sono progettati per spostare rapidamente il database tra diverse versioni di SQL Server e opzioni di distribuzione all'interno del database SQL di Azure oppure per implementare l'integrazione continua nella pipeline DevOps. Questo metodo non è invece indicato per la migrazione dei database di produzione con tempo di inattività minimo, perché sarebbe necessario interrompere l'aggiunta di nuovi dati, attendere il completamento dell'esportazione del database di origine in un file `.bacpac` e quindi attendere il completamento dell'importazione in Database SQL di Azure. Tutta questa attesa genera tempo di inattività dell'applicazione, soprattutto per i database di grandi dimensioni. Per spostare il database di produzione, è consigliabile scegliere un metodo di migrazione più efficace che garantisca un tempo di inattività minimo. A questo scopo, usare il [Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) per eseguire la migrazione del database con il tempo di inattività minimo. Servizio Migrazione del database ottiene questo risultato effettuando il push incrementale delle modifiche apportate al database di origine nel database singolo da ripristinare. In questo modo, è possibile spostare rapidamente l'applicazione dal database di origine a quello di destinazione con il tempo di inattività minimo.

## <a name="hands-on-learning-modules"></a>Moduli di apprendimento pratico

I seguenti moduli gratuiti di Microsoft Learn riguardano Database SQL di Azure.

- [Effettuare il provisioning di un database SQL di Azure per archiviare i dati dell'applicazione](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Sviluppare e configurare un'applicazione ASP.NET che esegue query su un database SQL di Azure](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Proteggere il database SQL di Azure](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Passaggi successivi

- Trovare un [elenco dettagliato delle funzionalità supportate in Database SQL di Azure](sql-database-features.md).
- Scoprire come rendere [più sicuro il database](sql-database-security-tutorial.md).
- Per altre procedure avanzate, vedere [Come usare un database singolo nel database SQL di Azure](sql-database-howto-single-database.md).
- Trovare altri script di esempio scritti in [PowerShell](sql-database-powershell-samples.md) e nell'[interfaccia della riga di comando di Azure](sql-database-cli-samples.md).
- Trovare altre informazioni sull'[API Gestione](sql-database-single-databases-manage.md) che è possibile usare per configurare i database.
- [Identificare l'istanza gestita/database SQL di Microsoft Azure idoneo per il database locale](/sql/dma/dma-sku-recommend-sql-db/).
