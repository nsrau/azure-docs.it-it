---
title: Gestire i dati personali in Microsoft Azure | Microsoft Docs
description: Linee guida per correggere, aggiornare, eliminare ed esportare i dati personali in Azure Active Directory e nel database SQL di Azure
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.openlocfilehash: 51dca8955745b40a9126b142ea15e707fe58bc72
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="manage-personal-data-in-microsoft-azure"></a>Gestire i dati personali in Microsoft Azure

Questo articolo descrive le linee guida per correggere, aggiornare, eliminare ed esportare i dati personali in Azure Active Directory e nel database SQL di Azure.

## <a name="scenario"></a>Scenario

Una società con sede a Dublino offre pacchetti completi per matrimoni di fascia alta in Irlanda e in tutto il mondo per una clientela sia locale sia internazionale. La società ha uffici, clienti, dipendenti e fornitori in tutto il mondo al fine di garantire un servizio completo nelle località che offre.

Tra i tanti servizi che gestisce, la società tiene traccia delle risposte agli inviti che includono preferenze e allergie alimentari. Gli ospiti che partecipano ai matrimoni possono iscriversi a varie attività come una passeggiata a cavallo, un giro in barca e così via. Possono persino interagire reciprocamente in una pagina Web centrale nei mesi che precedono l'evento. La società raccoglie i dati personali di dipendenti, fornitori, clienti e ospiti dei matrimoni. A causa della natura internazionale della propria attività, la società deve osservare più livelli di normative.

## <a name="problem-statement"></a>Presentazione del problema

- Gli amministratori di dati devono essere in grado di correggere eventuali errori nei dati personali, modificare i dati e aggiornare quelli incompleti.

- Devono essere in grado di eliminare i dati personali su richiesta dell'interessato.

- Devono poter esportare i dati e renderli disponibili su richiesta di un soggetto interessato in un formato comune e strutturato.

## <a name="company-goals"></a>Obiettivi della società

- I dati personali errati di clienti, ospiti, dipendenti e fornitori devono essere corretti e aggiornati in Azure Active Directory e nel database SQL di Azure.

- I dati personali devono essere eliminati in Azure Active Directory e nel database SQL di Azure su richiesta di un interessato.

- I dati personali devono essere esportati su richiesta di un interessato in un formato comune e strutturato.

## <a name="solutions"></a>Soluzioni

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory: rettificare/correggere i dati personali errati o incompleti e cancellare/eliminare i profili utente/dati personali

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è un servizio Microsoft per la gestione delle identità e delle directory multi-tenant, basato sul cloud.
È possibile correggere, aggiornare o eliminare i profili utente dei clienti e dei dipendenti e le informazioni di lavoro dell'utente che contengono dati personali, ad esempio il nome, il titolo di lavoro, l'indirizzo o il numero di telefono, nell'ambiente [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) tramite il [portale di Azure](https://portal.azure.com/).

È necessario accedere con un account di amministratore globale per la directory.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Come si correggono o si aggiornano le informazioni del profilo e di lavoro degli utenti in Azure Active Directory?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2. Selezionare **Tutti i servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

    ![supporto/image1.png](media/manage-personal-data-azure/image001.png)

3. Nel pannello **Utenti e gruppi** selezionare **Utenti**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. Nel pannello **Utenti e gruppi - Utenti** selezionare un utente nell'elenco, quindi nel pannello dell'utente selezionato scegliere **Profilo** per visualizzare le informazioni del profilo utente che devono essere corrette o aggiornate.

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. Correggere o aggiornare le informazioni e quindi selezionare **Salva** nella barra dei comandi.

6.  Nel pannello dell'utente selezionato, selezionare **Informazioni lavoro** per visualizzare le informazioni di lavoro dell'utente che devono essere corrette o aggiornate.

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. Correggere o aggiornare le informazioni di lavoro e quindi nella barra dei comandi selezionare **Salva**.

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Come si elimina un profilo utente in Azure Active Directory?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2. Selezionare **Tutti i servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

    ![](media/manage-personal-data-azure/image001.png)

3. Nel pannello **Utenti e gruppi** selezionare **Utenti**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. Nel pannello **Utenti e gruppi - Utenti** selezionare un utente nell'elenco.

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. Nel pannello dell'utente selezionare **Panoramica**, quindi nella barra dei comandi selezionare **Elimina**.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>Database SQL: rettificare/correggere i dati personali errati o incompleti, cancellare/eliminare di dati personali ed esportare i dati personali 

Il [database SQL di Microsoft Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) è un database che consente agli sviluppatori di compilare e gestire le applicazioni.

I dati personali possono essere aggiornati nel [database SQL di Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) usando le query SQL standard e possono anche essere eliminati. È inoltre possibile esportarli dal database SQL usando vari modi, tra cui Importazione/Esportazione guidata Server SQL di Azure, e un'ampia gamma di formati, incluso un file con estensione BACPAC.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>Come si correggono, aggiornano o cancellano i dati personali nel database SQL?

Per informazioni su come correggere o aggiornare i dati personali nel database SQL, consultare al documentazione relativa a [Update (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [Update Text](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [Update with Common Table Expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql) o [Update Write Text](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql).

Per informazioni su come eliminare i dati personali nel database SQL, consultare la documentazione relativa a [Delete (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql).

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>Come si esportano i dati personali verso un file BACPAC nel database SQL?

Un file BACPAC include i dati e i metadati del database SQL ed un file compresso con estensione BACPAC. Questa operazione può essere eseguita usando il [portale di Azure](https://portal.azure.com/), l'utilità della riga di comando SQLPackage, SQL Server Management Studio (SSMS) o PowerShell.

Per informazioni su come esportare i dati in un file BACPAC, visitare la pagina [Esportare un database SQL di Azure in un file BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export), che include le istruzioni dettagliate per seguire i modi elencati in precedenza.

Come si esportano i dati personali dal database SQL con Importazione/Esportazione guidata SQL Server?

Questa procedura guidata consente di copiare i dati da un'origine a una destinazione. Per un'introduzione alla procedura guidata, incluse le istruzioni per visualizzarla, le informazioni sulle autorizzazioni e le istruzioni su come ottenere assistenza per questo strumento, visitare la pagina Web [Importare ed esportare dati con l'Importazione/Esportazione guidata SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard).

Per una panoramica dei passaggi della procedura guidata, visitare la pagina Web [Steps in the SQL Server Import and Export Wizard](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard) (Passaggi di Importazione/Esportazione guidata SQL Server).

## <a name="next-steps"></a>Passaggi successivi:

[Database SQL di Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

