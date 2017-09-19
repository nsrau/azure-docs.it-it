
In questo video di Azure Friday, con Scott Hanselman e Karthik Raman, Principal Engineering Manager, sono disponibili altre informazioni sulla distribuzione globale in Azure Cosmos DB.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

Per informazioni sul funzionamento della replica di database globale in Azure Cosmos DB, vedere [Distribuire i dati a livello globale con Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).

## <a id="addregion"></a>Aggiungere aree di database globali tramite il portale di Azure
Azure Cosmos DB è disponibile in tutte le [aree di Azure][azureregions] del mondo. Dopo aver selezionato il livello di coerenza predefinito per l'account di database, è possibile associare una o più aree, a seconda del livello di coerenza predefinito e delle esigenze di distribuzione globale scelti.

1. Nella barra a sinistra del [portale di Azure](https://portal.azure.com/) fare clic su **Azure Cosmos DB**.
2. Nel pannello **Azure Cosmos DB** selezionare l'account di database da modificare.
3. Nel pannello dell'account fare clic su **Replica i dati a livello globale** dal menu.
4. Nel pannello **Replica i dati a livello globale** selezionare le aree da aggiungere o rimuovere facendo clic su di esse nella mappa e quindi scegliere **Salva**. L'aggiunta di aree ha un costo. Per altre informazioni, vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) o l'articolo [Distribuire i dati a livello globale con Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).
   
    ![Fare clic sulle aree nella mappa per aggiungerle o rimuoverle][1]
    
Dopo l'aggiunta di una seconda area, nel pannello **Replica i dati a livello globale** del portale viene abilitata l'opzione **Failover manuale**. È possibile usare questa opzione per testare il processo di failover o per modificare l'area di scrittura primaria. Dopo avere aggiunto una terza area, l'opzione **Priorità di failover** viene abilitata nello stesso pannello per poter modificare l'ordine di failover per le operazioni di lettura.  

### <a name="selecting-global-database-regions"></a>Selezionare aree di database globali
Esistono due scenari comuni per la configurazione di due o più aree:

1. Offerta di accesso con bassa latenza ai dati indipendentemente dalla posizione del mondo in cui si trovano gli utenti finali
2. Aggiunta di resilienza a livello di area per garantire continuità aziendale e ripristino di emergenza (BCDR)

Per offrire l'accesso con bassa latenza agli utenti finali, è consigliabile distribuire l'applicazione e aggiungere Azure Cosmos DB nelle aree corrispondenti alla località in cui si trovano gli utenti dell'applicazione.

Per finalità BCDR, è consigliabile aggiungere le aree in base alle coppie di aree descritte nell'articolo [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
