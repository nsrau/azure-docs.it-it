Per creare una rete virtuale in base allo scenario precedente tramite il portale di Azure, seguire questa procedura.

1. Da un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.

2. Fare clic su **NUOVO** > **Rete** > **Rete virtuale**, quindi fare clic su **Gestione risorse** dall'elenco **Selezionare un modello di distribuzione** e scegliere **Crea**.

3. Nel pannello **Crea rete virtuale** configurare le impostazioni della rete virtuale, come illustrato nella figura riportata di seguito.

	![Pannello Creare la rete virtuale](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse a cui aggiungere la rete virtuale o fare clic su **Crea nuovo** per aggiungere la rete virtuale a un nuovo gruppo di risorse. La figura seguente mostra le impostazioni del gruppo di risorse per un nuovo gruppo di risorse denominato **TestRG**. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md/#resource-groups).

	![Gruppo di risorse](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Se necessario, modificare le impostazioni per **Sottoscrizione** e **Percorso** per la rete virtuale.

6. Fare clic su **Crea** e osservare il riquadro **Creazione rete virtuale** come illustrato nella figura riportata di seguito.

	![Pannello Creazione della rete virtuale](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

7. Attendere che la rete virtuale venga creata, quindi nel pannello **Rete virtuale** fare clic su **Tutte le impostazioni** > **Subnet** > **Aggiungi**.

8. Specificare le impostazioni della subnet per la subnet *BackEnd*, come illustrato di seguito, quindi fare clic su **OK**.

	![Impostazioni della subnet](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

9. Visualizzare l'elenco di subnet.

	![Elenco delle subnet nella rete virtuale](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

<!---HONumber=AcomDC_0107_2016-->