---
title: 'Portale di Azure: creare un database SQL | Microsoft Docs'
description: Creare un server logico di database SQL, una regola del firewall a livello di server e database nel portale di Azure ed eseguire una query.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 09/07/2018
ms.openlocfilehash: 0e7ea33fa775bfba934d68d7cbcdd754880c3e55
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165008"
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Creare un database SQL di Azure nel portale di Azure

Questa guida introduttiva illustra come creare un database SQL in Azure usando il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md). Il database SQL di Azure è un'offerta di "database distribuito come servizio" che consente di eseguire e ridimensionare i database di SQL Server a disponibilità elevata nel cloud. Questa guida introduttiva illustra come iniziare a creare un database SQL e quindi eseguire una query usando il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

  >[!NOTE]
  >In questa esercitazione viene usato il modello di acquisto basato su DTU, ma è disponibile anche il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Creazione di un database SQL

Un database SQL di Azure viene creato con un set definito di [risorse di calcolo e di archiviazione](sql-database-service-tiers-dtu.md). Il database viene creato in un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) e in un [server logico di database SQL di Azure](sql-database-features.md).

Seguire questi passaggi per creare un database SQL contenente i dati di esempio di Adventure Works LT.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Database** dalla pagina **Nuovo**, quindi selezionare **Crea** in **Database SQL** nella pagina **Nuovo**.

   ![Creare il database 1](./media/sql-database-get-started-portal/create-database-1.png)

3. Compilare il modulo Database SQL con le informazioni seguenti, come illustrato nell'immagine precedente:   

   | Impostazione       | Valore consigliato | Descrizione |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome database** | mySampleDatabase | Per i nomi di database validi, vedere [Identificatori del database](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Sottoscrizione** | Sottoscrizione in uso  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.windowsazure.com/Subscriptions). |
   | **Gruppo di risorse**  | myResourceGroup | Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Seleziona origine** | Sample (AdventureWorksLT) | Carica lo schema e i dati di AdventureWorksLT nel nuovo database |

   > [!IMPORTANT]
   > È necessario selezionare il database di esempio in questo modulo perché verrà usato nel resto di questa guida introduttiva.
   >

4. In **Server** fare clic su **Configurare le impostazioni necessarie** e compilare il server SQL (server logico) con le informazioni seguenti, come illustrato nell'immagine seguente:   

   | Impostazione       | Valore consigliato | Descrizione |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome server** | Qualsiasi nome globalmente univoco | Per i nomi di server validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Accesso amministratore server** | Qualsiasi nome valido | Per i nomi di accesso validi, vedere [Identificatori del database](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Password** | Qualsiasi password valida | La password deve almeno 8 caratteri e contenere caratteri inclusi in tre delle categorie seguenti: caratteri maiuscoli, caratteri minuscoli, numeri e caratteri non alfanumerici. |
   | **Sottoscrizione** | Sottoscrizione in uso | Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.windowsazure.com/Subscriptions). |
   | **Gruppo di risorse** | myResourceGroup | Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Posizione** | Qualsiasi località valida | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |

   > [!IMPORTANT]
   > L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida di avvio rapido. Prendere nota di queste informazioni per usarle in seguito.
   >  

   ![Creare il server di database](./media/sql-database-get-started-portal/create-database-server.png)

5. Dopo aver completato il modulo, fare clic su **Seleziona**.

6. Fare clic su **Piano tariffario** per specificare il livello di servizio, il numero di DTU e la quantità di risorse di archiviazione. Esplorare le opzioni relative alla quantità di DTU e di risorse di archiviazione disponibile per ogni livello di servizio.

   > [!IMPORTANT]
   > Nel livello Premium è attualmente disponibile più di 1 TB di archiviazione in tutte le aree tranne le seguenti: Regno Unito settentrionale, Stati Uniti centro-occidentali, Regno Unito meridionale 2, Cina orientale, USDoDCentral, Germania centrale, USDoDEast, US Gov Southwest, US Gov South Central, Germania nord-orientale, Cina settentrionale, US Gov East. Nelle altre aree la quantità massima di risorse di archiviazione nel livello Premium è limitata a 1 TB. Vedere [Limitazioni correnti per P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

7. Per questa guida introduttiva selezionare il livello di servizio **Standard** e quindi usare il dispositivo di scorrimento per selezionare **10 DTU (S0)** e **1** GB di archiviazione.

   ![Creare il database s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. Accettare le condizioni dell'anteprima per usare l'opzione **Spazio di archiviazione aggiuntivo**.

   > [!IMPORTANT]
   > Nel livello Premium è attualmente disponibile più di 1 TB di archiviazione in tutte le aree tranne le seguenti: Stati Uniti centro-occidentali, Cina orientale, USDoDCentral, USGov Iowa, Germania centrale, USDoDEast, US Gov Southwest, Germania nord-orientale, Cina settentrionale. Nelle altre aree la quantità massima di risorse di archiviazione nel livello Premium è limitata a 1 TB. Vedere [Limitazioni correnti per P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

9. Dopo la selezione del livello di servizio, del numero di DTU e della quantità di risorse di archiviazione, fare clic su **Applica**.  

10. Dopo aver completato il modulo del database SQL, fare clic su **Crea** per effettuare il provisioning del database. Il provisioning richiede alcuni minuti.

11. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.

     ![notifica](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Effettuare una query sul database SQL

Ora che è stato creato un database di esempio in Azure, usare lo strumento di query predefinito nel portale di Azure per verificare la possibilità di connettersi al database ed eseguire query sui dati.

1. Nella pagina Database SQL del database fare clic su **Editor di query (anteprima)** nel menu a sinistra e quindi scegliere **Accedi**.

   ![Accesso](./media/sql-database-get-started-portal/query-editor-login.png)

2. Selezionare l'autenticazione di SQL Server, specificare le informazioni necessarie per l'accesso e quindi fare clic su **OK** per accedere.

3. Dopo l'autenticazione come **ServerAdmin**, digitare la query seguente nel riquadro dell'editor di query.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Fare clic su **Esegui** e quindi esaminare i risultati della query nel riquadro **Risultati**.

   ![Risultati dell'Editor di query](./media/sql-database-get-started-portal/query-editor-results.png)

5. Chiudere la pagina **Editor di query** e fare clic su **OK** per rimuovere le modifiche non salvate.

## <a name="clean-up-resources"></a>Pulire le risorse

Salvare queste risorse se si vuole passare a [Passaggi successivi](#next-steps) per ottenere informazioni su come eseguire la connessione e le query nel database usando alcuni metodi diversi. Se tuttavia si vogliono eliminare le risorse create in questa esercitazione introduttiva, seguire questa procedura.


1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic su **myResourceGroup**.
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare **myResourceGroup** nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Ora che è stato creato un database, è necessario creare una regola del firewall a livello di server per connettersi al database tramite gli strumenti locali. Vedere [Creare una regola del firewall a livello di server](sql-database-get-started-portal-firewall.md)
- Se si crea una regola del firewall a livello di server, è possibile [connettersi ed eseguire query](sql-database-connect-query.md) usando uno degli strumenti o dei linguaggi preferiti, tra cui
  - [Connettersi ed eseguire query usando SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Connettersi ed eseguire query usando Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Per creare database usando l'interfaccia della riga di comando di Azure, vedere [Esempi di interfaccia della riga di comando di Azure](sql-database-cli-samples.md)
- Per creare database usando Azure PowerShell, vedere [Esempi Azure PowerShell](sql-database-powershell-samples.md)