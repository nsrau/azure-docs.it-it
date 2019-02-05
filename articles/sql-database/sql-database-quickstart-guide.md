---
title: 'Guida introduttiva: Database singoli in Database SQL di Azure | Microsoft Docs'
description: Informazioni su come iniziare rapidamente a usare i database singoli in Database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464812"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Introduzione ai database singoli in Database SQL di Azure

Un [database singolo](sql-database-single-index.yml) in Database SQL di Azure è un database PaaS distribuito come servizio (DbaaS) completamente gestito, ideale da usare come motore di archiviazione per le moderne applicazioni native del cloud. In questa sezione verrà illustrato come configurare e creare rapidamente un database SQL.

## <a name="quickstart-overview"></a>Panoramica della guida introduttiva

Questa sezione contiene una panoramica degli articoli disponibili da consultare per iniziare rapidamente a lavorare con i database singoli. Il modo più semplice per creare il primo database SQL consiste nell'usare il [portale di Azure](sql-database-get-started-portal.md), in cui è possibile configurare i parametri necessari.
Dopo averlo creato, è necessario [proteggere il database configurando le regole del firewall](sql-database-get-started-portal-firewall.md). 

Se è già disponibile un database in SQL Server di cui si vuole eseguire la migrazione ad Azure, è necessario installare [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), che analizzerà i database in SQL Server per rilevare eventuali problemi che potrebbero impedire la migrazione al database singolo. Se non vengono rilevati problemi, è possibile esportare il database come file `.bacpac` e [importarlo usando il portale di Azure o SqlPackage](sql-database-import.md).

Queste guide introduttive consentono di configurare, creare e importare rapidamente i database nel cloud di Azure.

## <a name="automating-management-operations"></a>Automazione delle operazioni di gestione

Il portale di Azure consente di creare e modificare con facilità il database singolo. In alternativa, è possibile usare [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) o l'[interfaccia della riga di comando di Azure](scripts/sql-database-create-and-configure-database-cli.md) per creare i database.
È anche possibile aggiornare il database singolo e ridimensionare le risorse con [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) o con l'[interfaccia della riga di comando di Azure](scripts/sql-database-monitor-and-scale-database-cli.md).

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrazione a un database singolo con tempo di inattività minimo

Gli articoli di queste guide introduttive consentono di creare o importare rapidamente il database in Azure usando `.bacpac`. Tuttavia, i file`.bacpac` e `.dacpack` sono progettati per spostare rapidamente il database tra diverse versioni di SQL Server e Database SQL di Azure (database singolo, pool elastico o Istanza gestita) o per implementare l'integrazione continua nella pipeline DevOps. Questo metodo non è invece indicato per la migrazione dei database di produzione con tempo di inattività minimo, perché sarebbe necessario aspettare che il database di origine venga esportato come file `.bacpac` e quindi importato in Database SQL di Azure, con un conseguente tempo di inattività per l'applicazione, soprattutto nel caso di database di grandi dimensioni. Se si intende spostare il database di produzione, per eseguire la migrazione è consigliabile scegliere un metodo più efficace che garantisca un tempo di inattività minimo. [Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) è un servizio che consente di eseguire la migrazione del database con il tempo di inattività minimo. In questo modo, è possibile spostare rapidamente l'applicazione dal database di origine a quello di destinazione con il tempo di inattività minimo.

## <a name="next-steps"></a>Passaggi successivi

* Trovare un [elenco dettagliato delle funzionalità supportate in Database SQL di Azure](sql-database-features.md). 
* Scoprire come rendere [più sicuro il database](sql-database-security-tutorial.md). 
* Trovare esercitazioni più avanzate nella [sezione di procedure](sql-database-howto-single-database.md). 
* Trovare altri script di esempio scritti in [PowerShell](sql-database-powershell-samples.md) e nell'[interfaccia della riga di comando di Azure](sql-database-cli-samples.md). 
* Trovare altre informazioni sull'[API Gestione](sql-database-single-databases-manage.md) che è possibile usare per configurare i database. 
