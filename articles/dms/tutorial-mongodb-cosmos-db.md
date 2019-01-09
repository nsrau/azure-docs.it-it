---
title: "Esercitazione: Usare il servizio Migrazione del database di Azure per eseguire la migrazione di MongoDB all'API Mongo di Azure Cosmos DB offline | Microsoft Docs"
description: Informazioni su come eseguire la migrazione da MongoDB in locale all'API Mongo di Azure Cosmos DB offline con il servizio Migrazione del database di Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 4651c9afab99577622af71297e1fb6465a20097f
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713098"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-mongo-api-offline-using-dms"></a>Esercitazione: Eseguire la migrazione offline di MongoDB all'API Mongo di Azure Cosmos DB con il servizio Migrazione del database di Azure
È possibile usare il servizio Migrazione del database di Azure per eseguire una migrazione offline (una tantum) di database da un'istanza locale o cloud di MongoDB all'API Mongo di Azure Cosmos DB.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

In questa esercitazione si esegue la migrazione del set di dati **Wingtips** in MongoDB ospitato in una macchina virtuale di Azure all'API Cosmos DB per MongoDB usando il Servizio Migrazione del database di Azure. Se non è già stata configurata un'origine MongoDB, vedere l'articolo [Installare e configurare MongoDB in una VM Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario:
- [Creare un account API Azure Cosmos DB per MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Creare una rete virtuale per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Verificare che le regole dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure non blocchino le porte di comunicazione seguenti: 443, 53, 9354, 445 e 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
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

    La VNET consente al servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza del database SQL di Azure di destinazione.

    Per altre informazioni su come creare una VNET nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

6. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    Se serve assistenza nella scelta del piano tariffario del servizio Migrazione del database di Azure, vedere i suggerimenti in questo [post di blog](https://go.microsoft.com/fwlink/?linkid=861067).  

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
    
   È anche possibile usare la modalità della stringa di connessione e specificare un percorso per un contenitore di file di archivio blog in cui sono stati scaricati i dati di raccolta dei quali si vuole eseguire la migrazione.

   > [!NOTE]
   > Il servizio Migrazione del database di Azure può anche eseguire la migrazione di documenti BSON o JSON nelle raccolte API Mongo di Azure Cosmos DB.
    
   Nelle situazioni in cui la risoluzione del nome DNS non è possibile, si può usare l'indirizzo IP.

   ![Specificare le informazioni di origine](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione
1. Nella schermata **Dettagli destinazione della migrazione** specificare i dettagli di connessione dell'account Azure Cosmos DB di destinazione, che è l'account MongoDB di Azure Cosmos DB di cui è già stato effettuato il provisioning e verso cui si esegue la migrazione dei dati MongoDB.

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

- Al termine della migrazione è possibile verificare l'account API Azure Cosmos DB per MongoDB per verificare che sia stata eseguita la migrazione di tutte le raccolte.

    ![Stato dell'attività completato](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Risorse aggiuntive

 * [Informazioni sul servizio Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Passaggi successivi
- Esaminare le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft per altri scenari.