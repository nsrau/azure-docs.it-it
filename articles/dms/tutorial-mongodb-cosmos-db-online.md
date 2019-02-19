---
title: "Esercitazione: Usare Servizio Migrazione del database di Azure per eseguire la migrazione online di MongoDB all'API di Azure Cosmos DB per MongoDB | Microsoft Docs"
description: Informazioni su come eseguire la migrazione online da MongoDB in locale all'API di Azure Cosmos DB per MongoDB con Servizio Migrazione del database di Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/12/2019
ms.openlocfilehash: 95286b7a63471ee07f76276d8b4b63ca5f2aecce
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212029"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms-preview"></a>Esercitazione: Eseguire la migrazione online di MongoDB all'API di Azure Cosmos DB per MongoDB con Servizio Migrazione del database (anteprima)
È possibile usare Servizio Migrazione del database di Azure per eseguire una migrazione online di database (con tempi di inattività minimi) da un'istanza locale o cloud di MongoDB all'API di Azure Cosmos DB per MongoDB.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.
> * Completare la migrazione quando si è pronti.

In questa esercitazione si esegue la migrazione di un set di dati in MongoDB ospitato in una macchina virtuale di Azure all'API di Azure Cosmos DB per MongoDB con tempi di inattività minimi, usando Servizio Migrazione del database di Azure. Se non è già stata configurata un'origine MongoDB, vedere l'articolo [Installare e configurare MongoDB in una VM Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento di dati tra aree o regioni geografiche può rallentare il processo di migrazione.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione online da MongoDB all'API di Azure Cosmos DB per MongoDB. Per una migrazione offline, vedere [Eseguire la migrazione offline di MongoDB all'API di Azure Cosmos DB per MongoDB con il servizio Migrazione del database](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario:
- [Creare un account dell'API di Azure Cosmos DB per MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Creare una rete virtuale di Azure per Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Verificare che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione seguenti: 443, 53, 9354, 445 e 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Modificare il firewall del server di origine per consentire a Servizio Migrazione del database di Azure di accedere al server MongoDB di origine, che per impostazione predefinita corrisponde alla porta TCP 27017.
- Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration
1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)
       
2. Selezionare la sottoscrizione in cui si desidera creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.
 
    ![Visualizzare i provider di risorse](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)
    
3.  Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.
 
    ![Registrare il provider di risorse](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Creare un'istanza
1.  Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2.  Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.
 
    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3.  Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la località in cui si vuole creare l'istanza di Servizio Migrazione del database di Azure. 

5. Selezionare una rete virtuale (VNET) esistente o crearne una nuova.

    La VNET consente al servizio Migrazione del database di Azure di accedere all'istanza di MongoDB di origine e all'account Azure Cosmos DB di destinazione.

    Per altre informazioni su come creare una VNET nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

6. Selezionare uno SKU del piano tariffario Premium.

    > [!NOTE]
    > Le migrazioni online sono supportate solo quando viene usato il piano tariffario Premium. Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    Per informazioni utili per la scelta del livello appropriato per Servizio Migrazione del database di Azure, vedere i suggerimenti riportati in questo [post di blog](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7.  Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione
Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.
 
    ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.

    In alternativa, è possibile individuare l'istanza di Servizio Migrazione del database di Azure dal riquadro di ricerca nel portale di Azure.

    ![Usare il riquadro di ricerca nel portale di Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Selezionare **+ Nuovo progetto di migrazione**.

4. Nella schermata **Nuovo progetto di migrazione** specificare un nome per il progetto e quindi selezionare **MongoDB** nella casella di testo **Tipo del server di origine**, **Cosmos DB (API MongoDB)** nella casella di testo **Tipo del server di destinazione** e infine **Migrazione dei dati online [anteprima]** in **Scegli il tipo di attività**.

    ![Creare un progetto del servizio Migrazione del database](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5.  Selezionare **Salva** e quindi **Crea ed esegui attività** per creare il progetto ed eseguire l'attività di migrazione.

## <a name="specify-source-details"></a>Specificare le informazioni di origine
1. Nella schermata **Dettagli origine** specificare i dettagli di connessione per il server MongoDB di origine.

    Per la connessione a un'origine sono disponibili tre modalità:
       * **Modalità standard**, che accetta un nome di dominio completo o un indirizzo IP, il numero di porta e le credenziali di connessione.
       * **Modalità stringa di connessione**, che accetta una stringa di connessione di MongoDB come illustrato nell'articolo [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (Formato URI della stringa di connessione).
       * **Dati di Archiviazione di Azure**, che accetta l'URL di firma di accesso condiviso di un contenitore BLOB. Selezionare **Il BLOB contiene i dump BSON** se il contenitore BLOB include dump BSON generati dallo [strumento bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) di MongoDB e deselezionare questa opzione se il contenitore contiene file JSON.

    Nei casi in cui la risoluzione dei nomi DNS non è possibile, si può usare l'indirizzo IP.

   ![Specificare le informazioni di origine](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Selezionare **Salva**.

   > [!NOTE]
   > L'indirizzo del server di origine deve essere l'indirizzo dell'elemento primario se l'origine è un set di repliche e il router se l'origine è un cluster MongoDB partizionato. Per un cluster MongoDB partizionato, Servizio Migrazione del database di Azure deve potersi connettere alle singole partizioni nel cluster e questo può richiedere l'apertura del firewall in più computer.          

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione
1. Nella schermata **Dettagli destinazione della migrazione** specificare i dettagli di connessione dell'account Azure Cosmos DB di destinazione, che è l'account dell'API di Azure Cosmos DB per MongoDB di cui è già stato effettuato il provisioning e verso cui si esegue la migrazione dei dati MongoDB.

    ![Specificare i dettagli della destinazione](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Selezionare **Salva**.

## <a name="map-to-target-databases"></a>Eseguire il mapping nei database di destinazione
1. Nella schermata **Map to target databases** (Esegui il mapping nel database di destinazione) eseguire il mapping del database di origine e del database di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome di database del database di origine, Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    Se accanto al nome del database viene visualizzata la stringa **Create** (Crea), ciò indica che il servizio Migrazione del database di Azure non ha trovato il database di destinazione e provvederà quindi a crearlo.

    A questo punto della migrazione, se si vuole configurare la velocità effettiva condivisa per il database specificare un valore di UR di velocità effettiva. In Cosmos DB è possibile effettuare il provisioning della velocità effettiva a livello di database o di singola raccolta. La velocità effettiva viene misurata in [unità richiesta](https://docs.microsoft.com/azure/cosmos-db/request-units) (UR). Vedere altre informazioni sui [prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Eseguire il mapping nei database di destinazione](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Selezionare **Salva**.

3. Nella schermata **Impostazioni raccolta** espandere l'elenco delle raccolte, quindi esaminare le raccolte di cui verrà eseguita la migrazione.

    Si noti che il servizio Migrazione del database di Azure seleziona automaticamente tutte le raccolte esistenti nell'istanza MongoDB di origine che non esistono nell'account Azure Cosmos DB di destinazione. Se si vuole eseguire di nuovo la migrazione di raccolte che già includono dati, è necessario selezionarle esplicitamente in questa schermata.

    È possibile specificare il numero di UR da usare per le raccolte. Nella maggior parte dei casi dovrebbe essere sufficiente un valore compreso tra 500 (almeno 1000 per le raccolte partizionate) e 4000. Il servizio Migrazione del database di Azure suggerisce le impostazioni predefinite intelligenti in base alle dimensioni della raccolta.

    È anche possibile specificare una chiave di partizione per sfruttare il [partizionamento in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) per una scalabilità ottimale. Esaminare le [procedure consigliate per la selezione di una chiave di partizione](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Se non si ha una chiave di partizione, è sempre possibile usare **_id** come chiave di partizione per una migliore velocità effettiva.

    ![Selezionare le tabelle delle collezioni](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Selezionare **Salva**.

5. Nella schermata **Riepilogo migrazione** specificare un nome per l'attività di migrazione nella casella di testo **Nome attività**.

    ![Riepilogo della migrazione](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Eseguire la migrazione
- Selezionare **Esegui migrazione**.

   Verrà visualizzata la finestra dell'attività di migrazione con lo **stato** dell'attività.

   ![Stato dell'attività](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorare la migrazione
- Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché come **Stato** della migrazione non viene indicato **Riproduzione**.

   > [!NOTE]
   > È possibile selezionare l'attività per ottenere dettagli sulle metriche di migrazione a livello di database e raccolta.

   ![Stato dell'attività: Riproduzione](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Verificare i dati in Cosmos DB

1. Apportare modifiche al database MongoDB di origine.
2. Connettersi a Cosmos DB per verificare se i dati vengono replicati dal server MongoDB di origine.

    ![Stato dell'attività: Riproduzione](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)
 
## <a name="complete-the-migration"></a>Completare la migrazione

* Quando tutti i documenti dell'origine sono disponibili nella destinazione Cosmos DB, selezionare **Fine** nel menu di scelta rapida dell'attività di migrazione per completare la migrazione.

    Questa azione terminerà la riproduzione di tutte le modifiche in sospeso e completerà la migrazione.

    ![Stato dell'attività: Riproduzione](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="additional-resources"></a>Risorse aggiuntive

 * [Informazioni sul servizio Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Passaggi successivi
- Esaminare le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft per altri scenari.