---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728562"
---
## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="create-a-blank-sql-database"></a>Creare un database SQL vuoto

Un database SQL di Azure esiste in un set definito di [risorse di calcolo e di archiviazione](../articles/sql-database/sql-database-service-tiers-dtu.md). Il database funziona all'interno di un [gruppo di risorse di Azure](../articles/azure-resource-manager/resource-group-overview.md) e un [server logico di database SQL di Azure](../articles/sql-database/sql-database-features.md).

Per creare un database SQL vuoto, attenersi alla procedura seguente.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

1. Nella pagina **Nuovo** selezionare **Database** > **Database SQL**.

   ![creare database vuoto](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. Nel riquadro **Database SQL** digitare o selezionare i valori riportati di seguito:

   | Impostazione       | Valore consigliato | Descrizione |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome database** | *yourDatabase* | Per informazioni sui nomi di database validi, vedere [Identificatori del database](/sql/relational-databases/databases/database-identifiers). |
   | **Sottoscrizione** | *yourSubscription*  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.windowsazure.com/Subscriptions). |
   | **Gruppo di risorse** | *yourResourceGroup* | Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/naming-conventions). |
   | **Select source** (Seleziona origine) | Database vuoto | Indica che deve essere creato un database vuoto. |

   ![creazione del database](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. Selezionare **Server** per configurare un server per il nuovo database. Digitare o selezionare quindi i valori seguenti:

      | Impostazione       | Valore consigliato | Descrizione |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Nome server** | Qualsiasi nome globalmente univoco | Per i nomi di server validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/naming-conventions). |
      | **Accesso amministratore server** | Qualsiasi nome valido | Per informazioni sui nomi di accesso validi, vedere [Identificatori del database](/sql/relational-databases/databases/database-identifiers).|
      | **Password** | Qualsiasi password valida | La password deve almeno 8 caratteri e usare caratteri inclusi in tre delle categorie seguenti: caratteri maiuscoli, caratteri minuscoli, numeri e caratteri non alfanumerici. |
      | **Posizione** | Qualsiasi località valida | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |

      Scegliere **Seleziona**.

      ![Creare il server di database](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. Selezionare **Piano tariffario** per specificare il livello di servizio, il numero di DTU e la quantità di risorse di archiviazione. Esplorare le opzioni relative alle DTU e alle risorse di archiviazione disponibili per ogni livello di servizio.

      Dopo la selezione del livello di servizio, del numero di DTU e della quantità di risorse di archiviazione, selezionare **Applica**.

   1. Immettere le **regole di confronto** per il database vuoto. Per questa esercitazione usare il valore predefinito. Per altre informazioni sulle regole di confronto, vedere [Collations](/sql/t-sql/statements/collations) (Regole di confronto).

1. Dopo aver completato il modulo **Database SQL**, selezionare **Crea** per creare il database. Il completamento di questo passaggio può richiedere circa un minuto e mezzo.

1. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.

     ![notifica](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Creare una regola del firewall

Il servizio di database SQL crea un firewall a livello di server per impedire alle applicazioni e agli strumenti esterni di connettersi al server o ai database sul server. Seguire questa procedura per creare una [regola del firewall a livello di server di database SQL](../articles/sql-database/sql-database-firewall-configure.md) per l'indirizzo IP del client. Questo processo consente la connettività esterna attraverso il firewall del database SQL solo per l'indirizzo IP.

> [!NOTE]
> Il database SQL comunica sulla porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non è possibile connettersi al server di database SQL di Azure a meno che l'amministratore non apra la porta 1433.

1. Al termine della distribuzione, scegliere **Database SQL** nel menu a sinistra e quindi selezionare *yourDatabase* nella pagina **Database SQL**. Verrà visualizzata la pagina di panoramica del database, che mostra il **nome server** completo, ad esempio *yourserver.database.windows.net*, e offre opzioni per operazioni di configurazione aggiuntive.

1. Copiare il nome completo del server per connettersi al server e ai relativi database nei passaggi successivi.

   ![Nome del server](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. Selezionare **Imposta firewall server** sulla barra degli strumenti. Verrà visualizzata la pagina **Impostazioni del firewall** per il server di database SQL.

   ![Regola del firewall del server](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. Selezionare **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP corrente a una nuova regola del firewall. Una regola del firewall può aprire la porta 1433 per un indirizzo IP singolo o un intervallo di indirizzi IP.

   1. Scegliere **Salva**. Viene creata una regola del firewall a livello di server per l'indirizzo IP corrente, che apre la porta 1433 nel server logico.

   1. Selezionare **OK** e quindi chiudere la pagina **Impostazioni del firewall**.

L'indirizzo IP può ora passare attraverso il firewall e connettersi al server di database SQL e ai relativi database usando SSMS o un altro strumento di propria scelta. Assicurarsi di usare l'account amministratore del server creato in precedenza.

> [!IMPORTANT]
> Per impostazione predefinita, l'accesso attraverso il firewall del database SQL è abilitato per tutti i servizi di Azure. Selezionare **NO** in questa pagina per disabilitare tutti i servizi di Azure.
