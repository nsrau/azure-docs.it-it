## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Come creare una rete virtuale classica nel portale di Azure
Per creare una rete virtuale classica in base allo scenario precedente, seguire questa procedura.

1. In un browser passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Crea una risorsa** > **Rete** > **Rete virtuale**. Si noti che l'elenco **Selezionare un modello di distribuzione** indica già **Classico**. 3. Fare clic su **Crea** come illustrato nella figura seguente.
   
    ![Creare reti virtuali nel portale di Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Nel riquadro **Rete virtuale** digitare il **nome** della rete virtuale e quindi fare clic su **Spazio di indirizzi**. Configurare le impostazioni dello spazio di indirizzi per la rete virtuale e la prima subnet, quindi fare clic su **OK**. La figura seguente illustra le impostazioni del blocco CIDR per questo scenario.
   
    ![Riquadro dello spazio di indirizzi](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse a cui aggiungere la rete virtuale oppure fare clic su **Crea nuovo gruppo di risorse** per aggiungere la rete virtuale a un nuovo gruppo di risorse. La figura seguente mostra le impostazioni del gruppo di risorse per un nuovo gruppo di risorse denominato **TestRG**. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Gestione risorse di Azure](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Riquadro Crea gruppo di risorse](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Se necessario, modificare le impostazioni relative a **Sottoscrizione** e **Località** per la rete virtuale. 
7. Se non si vuole visualizzare la rete virtuale come riquadro nella **Schermata iniziale**, disabilitare **Aggiungi alla Schermata iniziale**. 
8. Fare clic su **Crea** e osservare il riquadro **Creazione della rete virtuale** come illustrato nella figura seguente.
   
    ![Creare reti virtuali nel portale](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Attendere che la rete virtuale da creare sia pronta e quando viene visualizzato il riquadro, fare clic per aggiungere altre subnet.
   
    ![Creare reti virtuali nel portale](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Verrà visualizzata la voce **Configurazione** per la rete virtuale, come illustrato nella figura. 
   
    ![Creare reti virtuali nel portale](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Fare clic su **Subnet** > **Aggiungi**, digitare un **nome** e specificare un **intervallo di indirizzi (blocco CIDR)** per la subnet e quindi fare clic su **OK**. La figura seguente illustra le impostazioni per lo scenario corrente.
    
    ![Creare reti virtuali nel portale di Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

