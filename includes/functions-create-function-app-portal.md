
1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Fare clic su **Calcolo** > **App per le funzioni**, selezionare la **sottoscrizione**, immettere un **nome** univoco per identificare l'app per le funzioni e quindi specificare le impostazioni seguenti:
   
   * **[Gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md)**: selezionare **Crea nuovo** e immettere un nome per il nuovo gruppo di risorse. 
   * **[Piano di hosting](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)** può essere uno dei piani seguenti: 
     * **Piano a consumo**: tipo di piano predefinito per Funzioni di Azure. Quando si sceglie un piano a consumo, occorre anche scegliere la **località**.  
     * **Piano di Servizio app**: se si sceglie un piano di servizio app è necessario creare un **piano di servizio app/località** o selezionarne uno esistente. Queste impostazioni determinano [località, funzionalità, costi e risorse di calcolo](https://azure.microsoft.com/pricing/details/app-service/) associati all'app.  
   * **Account di archiviazione**: ogni app per le funzioni richiede un account di archiviazione. È possibile [creare un account di archiviazione](../articles/storage/storage-create-storage-account.md#create-a-storage-account) oppure sceglierne uno esistente. 
     
    ![Creare un'app per le funzioni nel portale di Azure](./media/functions-create-function-app-portal/function-app-create-flow.png)

3. Fare clic su **Crea** per effettuare il provisioning della nuova app per le funzioni e distribuirla.  
