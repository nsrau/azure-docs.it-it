---
title: Informazioni di riferimento sul contenuto delle guide di avvio rapido per il database singolo
description: Informazioni di riferimento per il contenuto di tutte le guide di avvio rapido che consentono di iniziare rapidamente a usare i database singoli nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: cd1880f58d4ef457cdc411e065588204a5ad6672
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448768"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Introduzione ai database singoli in Database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Un [database singolo](../index.yml) è un database distribuito come servizio (DbaaS) per piattaforme distribuite come servizio (PaaS) completamente gestito, ideale da usare come motore di archiviazione per le moderne applicazioni native del cloud. In questa sezione verrà illustrato come configurare e creare rapidamente un database singolo nel database SQL di Azure.

## <a name="quickstart-overview"></a>Panoramica della guida introduttiva

Questa sezione contiene una panoramica degli articoli disponibili da consultare per iniziare rapidamente a lavorare con i database singoli. Le guide di avvio rapido seguenti consentono di creare rapidamente un database singolo, configurare una regola del firewall a livello di server e quindi importare un database nel nuovo database singolo usando un file `.bacpac`:

- [Creare un database singolo usando il portale di Azure](single-database-create-quickstart.md).
- Dopo averlo creato, è necessario [proteggere il database configurando le regole del firewall](firewall-create-server-level-portal-quickstart.md).
- Se si dispone già di un database in SQL Server di cui si vuole eseguire la migrazione al database SQL di Azure, è necessario installare [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), che analizzerà i database in SQL Server per rilevare eventuali problemi che potrebbero bloccare la migrazione. Se non vengono rilevati problemi, è possibile esportare il database come file `.bacpac` e [importarlo usando il portale di Azure o SqlPackage](database-import.md).


## <a name="automating-management-operations"></a>Automazione delle operazioni di gestione

È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per creare, configurare e ridimensionare il database.

- [Creare e configurare un database singolo con PowerShell](scripts/create-and-configure-database-powershell.md) o con l'[interfaccia della riga di comando di Azure](scripts/create-and-configure-database-cli.md)
- [Aggiornare il database singolo e ridimensionare le risorse con PowerShell](scripts/monitor-and-scale-database-powershell.md) o con l'[interfaccia della riga di comando di Azure](scripts/monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrazione a un database singolo con tempo di inattività minimo

Queste guide introduttive consentono di creare o importare rapidamente il database in Azure usando un file `.bacpac`. Tuttavia, i file`.bacpac` e `.dacpac` sono progettati per spostare rapidamente i database tra diverse versioni di SQL Server e all'interno di Azure SQL oppure per implementare l'integrazione continua nella pipeline DevOps. Questo metodo non è invece indicato per la migrazione dei database di produzione con tempo di inattività minimo, perché sarebbe necessario interrompere l'aggiunta di nuovi dati, attendere il completamento dell'esportazione del database di origine in un file `.bacpac` e quindi attendere il completamento dell'importazione in Database SQL di Azure. Tutta questa attesa genera tempo di inattività dell'applicazione, soprattutto per i database di grandi dimensioni. Per spostare il database di produzione, è consigliabile scegliere un metodo di migrazione più efficace che garantisca un tempo di inattività minimo. A questo scopo, usare il [Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) per eseguire la migrazione del database con il tempo di inattività minimo. Servizio Migrazione del database ottiene questo risultato effettuando il push incrementale delle modifiche apportate al database di origine nel database singolo da ripristinare. In questo modo, è possibile spostare rapidamente l'applicazione dal database di origine a quello di destinazione con il tempo di inattività minimo.

## <a name="hands-on-learning-modules"></a>Moduli di apprendimento pratico

I seguenti moduli gratuiti di Microsoft Learn riguardano Database SQL di Azure.

- [Effettuare il provisioning di un database nel database SQL per archiviare i dati dell'applicazione](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Sviluppare e configurare un'applicazione ASP.NET che esegue query su un database nel database SQL di Azure](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Proteggere un database nel database SQL di Azure](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Passaggi successivi

- Trovare un [elenco dettagliato delle funzionalità supportate in Database SQL di Azure](features-comparison.md).
- Scoprire come rendere [più sicuro il database](secure-database-tutorial.md).
- Per altre procedure avanzate, vedere [Come usare un database singolo nel database SQL di Azure](how-to-content-reference-guide.md).
- Trovare altri script di esempio scritti in [PowerShell](powershell-script-content-guide.md) e nell'[interfaccia della riga di comando di Azure](az-cli-script-samples-content-guide.md).
- Trovare altre informazioni sull'[API Gestione](single-database-manage.md) che è possibile usare per configurare i database.
- [Identificare lo SKU corretto del database SQL di Azure o dell'istanza gestita di SQL di Azure per il database locale](/sql/dma/dma-sku-recommend-sql-db/).
