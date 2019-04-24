---
title: "Esercitazione: Usare il servizio Migrazione del database di Azure per eseguire la migrazione offline di MongoDB all'API di Azure Cosmos DB per MongoDB | Microsoft Docs"
description: Informazioni su come eseguire la migrazione offline da MongoDB in locale all'API di Azure Cosmos DB per MongoDB con il servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 01a64345285149a50c226ebb344982425a813156
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995082"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Esercitazione: Eseguire la migrazione offline di MongoDB all'API di Azure Cosmos DB per MongoDB con il servizio Migrazione del database
È possibile usare il servizio Migrazione del database di Azure per eseguire una migrazione offline (una tantum) di database da un'istanza locale o cloud di MongoDB all'API di Azure Cosmos DB per MongoDB.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

In questa esercitazione si esegue la migrazione di un set di dati in MongoDB ospitato in una macchina virtuale di Azure all'API di Azure Cosmos DB per MongoDB usando il servizio Migrazione del database di Azure. Se non è già stata configurata un'origine MongoDB, vedere l'articolo [Installare e configurare MongoDB in una VM Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario:
- [Completare la procedura di pre-migrazione](../cosmos-db/mongodb-pre-migration.md), ad esempio stimare la velocità effettiva, scegliere una chiave di parti e i criteri di indicizzazione.
- [Creare un account dell'API di Azure Cosmos DB per MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Creare una rete virtuale di Azure per Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con peering di rete per Microsoft, aggiungere gli [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) di servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    > - Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > - Endpoint di archiviazione
    > - Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché Servizio Migrazione del database di Azure non dispone di connettività Internet.

- Verificare che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in ingresso nel Servizio Migrazione del database di Azure: 443, 53, 9354, 445 e 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Aprire Windows Firewall per consentire al servizio Migrazione del database di Azure di accedere al server MongoDB di origine, per impostazione predefinita attraverso la porta TCP 27017.
- Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration
1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Selezionare la sottoscrizione in cui si desidera creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.
 
    ![Visualizzare i provider di risorse](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.
 
    ![Registrare il provider di risorse](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Creare un'istanza
1.  Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.
 
    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la località in cui si vuole creare l'istanza di Servizio Migrazione del database di Azure. 

5. Selezionare una rete virtuale (VNET) esistente o crearne una nuova.

    La VNET consente al servizio Migrazione del database di Azure di accedere all'istanza di MongoDB di origine e all'account Azure Cosmos DB di destinazione.

    Per altre informazioni su come creare una VNET nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

6. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    Per informazioni utili per la scelta del livello appropriato per Servizio Migrazione del database di Azure, vedere i suggerimenti riportati in questo [post di blog](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione
Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.
 
      ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.

3. Selezionare **+ Nuovo progetto di migrazione**.

4. Nella schermata **Nuovo progetto di migrazione** specificare un nome per il progetto e quindi selezionare **MongoDB** nella casella di testo **Tipo del server di origine**, **CosmosDB (API MongoDB)** nella casella di testo **Tipo del server di destinazione** e **Migrazione dei dati offline** per **Scegli il tipo di attività**. 

    ![Creare un progetto del servizio Migrazione del database](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Selezionare **Crea ed esegui attività** per creare il progetto ed eseguire l'attività di migrazione.

## <a name="specify-source-details"></a>Specificare le informazioni di origine
1. Nella schermata **Dettagli origine** specificare i dettagli di connessione per il server MongoDB di origine.
    
   È anche possibile usare la modalità della stringa di connessione e specificare un percorso per un contenitore di file di archivio BLOB in cui sono stati scaricati i dati di raccolta dei quali si vuole eseguire la migrazione.

   > [!NOTE]
   > Il servizio Migrazione del database di Azure può anche eseguire la migrazione di documenti BSON o JSON nelle raccolte dell'API di Azure Cosmos DB per MongoDB.
    
   Nelle situazioni in cui la risoluzione del nome DNS non è possibile, si può usare l'indirizzo IP.

   ![Specificare le informazioni di origine](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione
1. Nella schermata **Dettagli destinazione della migrazione** specificare i dettagli di connessione dell'account Azure Cosmos DB di destinazione, che è l'account dell'API di Azure Cosmos DB per MongoDB di cui è già stato effettuato il provisioning e verso cui si esegue la migrazione dei dati MongoDB.

    ![Specificare i dettagli della destinazione](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Selezionare **Salva**.

## <a name="map-to-target-databases"></a>Eseguire il mapping nei database di destinazione
1. Nella schermata **Map to target databases** (Esegui il mapping nel database di destinazione) eseguire il mapping del database di origine e del database di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome di database del database di origine, Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    Se accanto al nome del database viene visualizzata la stringa **Create** (Crea), ciò indica che il servizio Migrazione del database di Azure non ha trovato il database di destinazione e provvederà quindi a crearlo.

    A questo punto della migrazione è possibile [eseguire il provisioning della velocità effettiva](https://docs.microsoft.com/azure/cosmos-db/set-throughput). In Cosmos DB è possibile effettuare il provisioning della velocità effettiva a livello di database o di singola raccolta. La velocità effettiva viene misurata in [unità richiesta](https://docs.microsoft.com/azure/cosmos-db/request-units) (UR). Vedere altre informazioni sui [prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Eseguire il mapping nei database di destinazione](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Selezionare **Salva**.
3. Nella schermata **Impostazioni raccolta** espandere l'elenco delle raccolte, quindi esaminare le raccolte di cui verrà eseguita la migrazione.

    Si noti che il servizio Migrazione del database di Azure seleziona automaticamente tutte le raccolte esistenti nell'istanza MongoDB di origine che non esistono nell'account Azure Cosmos DB di destinazione. Se si vuole eseguire di nuovo la migrazione delle raccolte che includono già dati, occorre selezionarle esplicitamente in questo pannello.

    È possibile specificare la quantità di UR da usare per le raccolte. Il servizio Migrazione del database di Azure suggerisce le impostazioni predefinite intelligenti in base alle dimensioni della raccolta.

    È anche possibile specificare una chiave di partizione per sfruttare il [partizionamento in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) per una scalabilità ottimale. Esaminare le [procedure consigliate per la selezione di una chiave di partizione](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Selezionare le tabelle delle collezioni](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Selezionare **Salva**.

5. Nella schermata **Riepilogo migrazione** specificare un nome per l'attività di migrazione nella casella di testo **Nome attività**.

    ![Riepilogo della migrazione](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Eseguire la migrazione
- Selezionare **Esegui migrazione**.

    Viene visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **Non avviato**.

    ![Stato dell'attività](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorare la migrazione
- Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** delle migrazioni non viene indicato **Completata**.

   > [!NOTE]
   > È possibile selezionare l'attività per ottenere dettagli sulle metriche di migrazione a livello di database e raccolta.

    ![Stato dell'attività completato](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Verificare i dati in Cosmos DB

- Al termine della migrazione è possibile verificare l'account Azure Cosmos DB per verificare che sia stata eseguita la migrazione di tutte le raccolte.

    ![Stato dell'attività completato](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Ottimizzazione della post-migrazione

Dopo aver eseguito la migrazione dei dati archiviati nel database MongoDB nell'API per MongoDB di Azure Cosmos DB, è possibile connettersi ad Azure Cosmos DB e gestire i dati. È anche possibile eseguire altre procedure di ottimizzazione della post-migrazione, ad esempio: ottimizzare i criteri di indicizzazione, aggiornare il livello di coerenza predefinito o configurare la distribuzione globale per l'account Azure Cosmos DB. Per altre informazioni, vedere l'articolo [Ottimizzazione della post-migrazione](../cosmos-db/mongodb-post-migration.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

 * [Informazioni sul servizio Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Passaggi successivi
- Esaminare le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft per altri scenari.
