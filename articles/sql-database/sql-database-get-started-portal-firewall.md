---
title: 'Portale di Azure: Creare una regola del firewall per il database SQL | Microsoft Docs'
description: Creare una regola del firewall a livello di server per il database SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 589d3fa8c0ee8c8f374cd4f34f17401caa46d265
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462257"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-your-sql-database-using-the-azure-portal"></a>Guida introduttiva: Creare una regola firewall a livello del server per il database SQL tramite il portale di Azure

Questa guida introduttiva illustra come creare una regola del firewall a livello di server per un database SQL di Azure al fine di consentire la connessione da una risorsa locale.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva usa come punto di partenza le risorse create in [Creare un database SQLdi Azure nel portale di Azure](sql-database-get-started-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule"></a>Creare una regola del firewall a livello di server

Il servizio di database SQL crea un firewall a livello di server. Questo firewall impedisce ad applicazioni e strumenti di connettersi al server o a qualsiasi database server a meno che non si crei una regola del firewall per aprire il firewall. Per una connessione da un indirizzo IP all'esterno di Azure, creare una regola del firewall per un indirizzo IP specifico o un intervallo di indirizzi. Per altre informazioni sulle regole del firewall, vedere [Regole del firewall per il database SQL di Azure e SQL Data Warehouse](sql-database-firewall-configure.md).

> [!NOTE]
> Il database SQL comunica attraverso la porta 1433. Se si sta provando a connettersi da una rete aziendale, il traffico in uscita sulla porta 1433 potrebbe non essere consentito dal firewall della rete. In questo caso non è possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 1433.
>

Seguire questi passaggi per creare una regola del firewall a livello di server per l'indirizzo IP del client e abilitare la connettività esterna tramite il firewall del database SQL solo per il proprio indirizzo IP.

1. Al termine della distribuzione del [database SQL di Azure necessario come prerequisito](#prerequisites), scegliere **Database SQL** dal menu a sinistra e quindi selezionare **mySampleDatabase** nella pagina **Database SQL**. Si apre la pagina di panoramica per il database che visualizza il nome completo del server, ad esempio **mynewserver-20170824.database.windows.net**, e offre altre opzioni di configurazione.

2. Copiare il nome completo del server da usare per connettersi al server e ai relativi database in altre guide introduttive.

   ![Nome del server](./media/sql-database-get-started-portal/server-name.png)

3. Selezionare **Imposta firewall server** sulla barra degli strumenti. Si apre la pagina **Impostazioni del firewall** per il server del database SQL.

   ![Regola del firewall del server](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Scegliere **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP corrente a una nuova regola del firewall. Una regola del firewall può aprire la porta 1433 per un indirizzo IP singolo o un intervallo di indirizzi IP.

   > [!IMPORTANT]
   > Per impostazione predefinita, l'accesso attraverso il firewall del database SQL è abilitato per tutti i servizi di Azure. Selezionare **NO** in questa pagina per disabilitare tutti i servizi di Azure.
   >

5. Selezionare **Salva**. Viene creata una regola del firewall a livello di server per l'indirizzo IP corrente, che apre la porta 1433 nel server di database SQL.

6. Chiudere la pagina **Impostazioni del firewall**.

È ora possibile connettersi al server del database SQL e ai relativi database usando SQL Server Management Studio o un altro strumento a scelta da questo indirizzo IP, con l'account amministratore del server creato in precedenza.

## <a name="clean-up-resources"></a>Pulire le risorse

Salvare queste risorse se si vuole passare a [Passaggi successivi](#next-steps) per ottenere informazioni su come eseguire la connessione e le query nel database usando alcuni metodi diversi. Se tuttavia si vogliono eliminare le risorse create in questa guida introduttiva, seguire questa procedura.

1. Dal menu a sinistra nel portale di Azure scegliere **Gruppi di risorse** e quindi selezionare **myResourceGroup**.
2. Nella pagina del gruppo di risorse selezionare **Elimina**, digitare **myResourceGroup** nella casella di testo e quindi scegliere **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver creato un database, è possibile [connettersi ed eseguire query](sql-database-connect-query.md) usando uno degli strumenti o dei linguaggi di programmazione preferiti, tra cui
  - [Connettersi ed eseguire query usando SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Connettersi ed eseguire query usando Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Per informazioni su come progettare il primo database, creare tabelle e inserire dati, vedere una delle esercitazioni seguenti:
  - [Progettare il primo database SQL di Azure con SSMS](sql-database-design-first-database.md)
  - [Progettare un database SQL di Azure e connettersi con C# e ADO.NET](sql-database-design-first-database-csharp.md)
