---
title: Progettare il primo database relazionale C#
description: Informazioni su come progettare il primo database relazionale nel database SQL di Azure con C# e ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.openlocfilehash: 057ffcdc8a21567e909d768236e454ee10036115
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84216565"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-cx23-and-adonet"></a>Esercitazione: Progettare un database relazionale nel database SQL di Azure con C&#x23; e ADO.NET
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Il database SQL di Azure è un database relazionale distribuito come servizio in Microsoft Cloud (Azure). In questa esercitazione viene illustrato come usare il portale di Azure e ADO.NET con Visual Studio per eseguire queste operazioni:

> [!div class="checklist"]
>
> * Creare un database usando il portale di Azure
> * Configurare una regola del firewall IP a livello di server con il portale di Azure
> * Connettersi al database con ADO.NET e Visual Studio
> * Creare tabelle con ADO.NET
> * Inserire, aggiornare ed eliminare dati con ADO.NET
> * Eseguire query sui dati con ADO.NET

*Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!TIP]
> Il modulo gratuito seguente di Microsoft Learn contiene informazioni su come [sviluppare e configurare un'applicazione ASP.NET che esegue query su un database SQL di Azure](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), inclusa la creazione di un database semplice.

## <a name="prerequisites"></a>Prerequisiti

Installazione di [Visual Studio 2019](https://www.visualstudio.com/downloads/) o versioni successive.

## <a name="create-a-blank-database-in-azure-sql-database"></a>Creare un database vuoto nel database SQL di Azure

Un database nel database SQL di Azure viene creato con un set definito di risorse di calcolo e di archiviazione. Il database viene creato in un [gruppo di risorse di Azure](../../active-directory-b2c/overview.md) e viene gestito usando un [server SQL logico](logical-servers.md).

Per creare un database vuoto, seguire questa procedura.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Nella pagina **Nuovo**, selezionare **Database** nella sezione Azure Marketplace e quindi fare clic su **Database SQL** nella sezione **In primo piano**.

   ![creare database vuoto](./media/design-first-database-csharp-tutorial/create-empty-database.png)

3. Compilare il modulo **Database SQL** con le informazioni seguenti, come illustrato nell'immagine precedente:

    | Impostazione       | Valore consigliato | Descrizione |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome database** | *yourDatabase* | Per informazioni sui nomi di database validi, vedere [Identificatori del database](/sql/relational-databases/databases/database-identifiers). |
    | **Sottoscrizione** | *yourSubscription*  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.windowsazure.com/Subscriptions). |
    | **Gruppo di risorse** | *yourResourceGroup* | Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming). |
    | **Seleziona origine** | Database vuoto | Indica che deve essere creato un database vuoto. |

4. Fare clic su **Server** per usare un server esistente o crearne e configurarne uno nuovo. Selezionare un server esistente oppure fare clic su **Crea un nuovo server** e compilare il modulo **Nuovo server** con le informazioni seguenti:

    | Impostazione       | Valore consigliato | Descrizione |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome server** | Qualsiasi nome globalmente univoco | Per i nomi di server validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming). |
    | **Accesso amministratore server** | Qualsiasi nome valido | Per informazioni sui nomi di accesso validi, vedere [Identificatori del database](/sql/relational-databases/databases/database-identifiers). |
    | **Password** | Qualsiasi password valida | La password deve contenere almeno otto caratteri delle tre categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici. |
    | **Posizione** | Qualsiasi località valida | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |

    ![Creare il server di database](./media/design-first-database-csharp-tutorial/create-database-server.png)

5. Fare clic su **Seleziona**.
6. Fare clic su **Piano tariffario** per specificare il livello di servizio, il numero di DTU o vCores e la quantità di risorse di archiviazione. Esplorare le opzioni relative al numero di DTU/vCore e di risorse di archiviazione disponibile per ogni livello di servizio.

    Dopo aver selezionato il livello di servizio, il numero di DTU o vCore e la quantità di spazio di archiviazione, fare clic su **Applica**.

7. Immettere le **regole di confronto** per il database vuoto. Per questa esercitazione usare il valore predefinito. Per altre informazioni sulle regole di confronto, vedere [Collations](/sql/t-sql/statements/collations) (Regole di confronto)

8. Dopo aver completato il modulo **Database SQL**, fare clic su **Crea** per effettuare il provisioning del database. Questo passaggio potrebbe richiedere alcuni minuti.

9. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.

   ![notifica](./media/design-first-database-csharp-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Creare una regola del firewall IP a livello di server

Database SQL crea un firewall per gli indirizzi IP a livello di server. Questo impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database nel server a meno che una regola del firewall non consenta allo specifico indirizzo IP di superare il firewall. Per abilitare la connettività esterna al database, è prima di tutto necessario aggiungere una regola del firewall IP o l'intervallo di indirizzi IP. Per creare una [regola del firewall IP a livello di server](firewall-configure.md), seguire questa procedura.

> [!IMPORTANT]
> Il database SQL comunica attraverso la porta 1433. Se si intende connettersi al servizio da una rete aziendale, il firewall della rete potrebbe non consentire il traffico in uscita sulla porta 1433. In questo caso, non è possibile connettersi al database a meno che l'amministratore non apra la porta 1433.

1. Al termine della distribuzione, fare clic su **Database SQL** nel menu a sinistra e quindi su *yourDatabase* nella pagina **Database SQL**. Verrà visualizzata la pagina di panoramica del database, che mostra il **nome server** completo, ad esempio *yourserver.database.windows.net*, e offre opzioni per operazioni di configurazione aggiuntive.

2. Copiare il nome completo del server per usarlo per la connessione al server e ai database da SQL Server Management Studio.

   ![Nome del server](./media/design-first-database-csharp-tutorial/server-name.png)

3. Fare clic su **Imposta firewall server** sulla barra degli strumenti. Verrà visualizzata la pagina **Impostazioni del firewall** per il server.

   ![regola del firewall IP a livello di server](./media/design-first-database-csharp-tutorial/server-firewall-rule.png)

4. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP corrente a una nuova regola del firewall per gli indirizzi IP. Una regola del firewall per gli indirizzi IP può aprire la porta 1433 per un singolo indirizzo IP o un intervallo di indirizzi IP.

5. Fare clic su **Salva**. Viene creata una regola del firewall IP a livello di server per l'indirizzo IP corrente, che apre la porta 1433 nel server.

6. Fare clic su **OK** e quindi chiudere la pagina **Impostazioni del firewall**.

L'indirizzo IP può ora superare il firewall per gli indirizzi IP. È quindi possibile connettersi al database usando SQL Server Management Studio o un altro strumento di propria scelta. Assicurarsi di usare l'account amministratore del server creato in precedenza.

> [!IMPORTANT]
> Per impostazione predefinita, l'accesso attraverso il firewall per gli indirizzi IP di Database SQL è abilitato per tutti i servizi di Azure. Selezionare **NO** in questa pagina per disabilitare l'accesso per tutti i servizi di Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato le attività di base che è possibile eseguire con i database, ad esempio creare database e tabelle, connettersi al database, caricare dati ed eseguire query. Si è appreso come:

> [!div class="checklist"]
>
> * Creare un database usando il portale di Azure
> * Configurare una regola del firewall IP a livello di server con il portale di Azure
> * Connettersi al database con ADO.NET e Visual Studio
> * Creare tabelle con ADO.NET
> * Inserire, aggiornare ed eliminare dati con ADO.NET
> * Eseguire query sui dati con ADO.NET

Passare all'esercitazione successiva per informazioni sulla migrazione di dati.

> [!div class="nextstepaction"]
> [Eseguire la migrazione di SQL Server al database SQL di Azure offline con Servizio Migrazione del database](../../dms/tutorial-sql-server-to-azure-sql.md)
