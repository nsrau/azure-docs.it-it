Per creare una rete virtuale nel portale di Azure, seguire questa procedura. Si noti che le schermate sono fornite come esempi. Assicurarsi di sostituire i valori con i propri. Per altre informazioni sull'uso delle reti virtuali, vedere la [panoramica sulla rete virtuale](../articles/virtual-network/virtual-networks-overview.md).

1. In un browser passare al [portale di Azure](http://portal.azure.com) e, se necessario, accedere con l'account Azure.

2. Fare clic su **Nuovo** **>** **Rete** **>** **Rete virtuale**.

	![VNetBlade](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal650.png)

3. Nella parte inferiore del pannello Rete virtuale selezionare **Resource Manager** nell'elenco **Selezionare un modello di distribuzione** e quindi fare clic su **Crea**.


	![Selezionare Resource Manager](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png)

4. Nel pannello **Crea rete virtuale** configurare le impostazioni della rete virtuale. Nel pannello si aggiungerà il primo spazio di indirizzi e un intervallo di indirizzi di una singola subnet. Dopo aver creato la rete virtuale, è possibile tornare indietro e aggiungere altre subnet e altri spazi di indirizzi. Si tratta di una limitazione corrente del portale. È sempre possibile tornare indietro per aggiornare i valori modificando le proprietà della rete virtuale nel portale o tramite PowerShell. I valori da usare dipendono dalla configurazione che si vuole creare. Assicurarsi di fare riferimento ai valori di configurazione pianificati.

	![Pannello Creare la rete virtuale](./media/vpn-gateway-basic-vnet-rm-portal-include/createavnet250.png)

5. Verificare che la **sottoscrizione** sia quella corretta. È possibile modificare le sottoscrizioni tramite l'elenco a discesa.

6. Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse esistente o crearne uno nuovo digitandone il nome. Se si crea un nuovo gruppo, denominare il gruppo di risorse in base ai valori di configurazione pianificati. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md#resource-groups).

7. Selezionare quindi le impostazioni relative alla **località** per la rete virtuale. Si noti che questa impostazione determinerà la posizione in cui risiedono le risorse che si distribuiscono in questa rete virtuale. Non è possibile modificare questa impostazione in un secondo momento senza ridistribuire le risorse.

8. Selezionare **Aggiungi al dashboard** se si vuole trovare facilmente la rete virtuale nel dashboard e quindi fare clic su **Crea**.
	
	![Aggiungi al dashboard](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png)


9. Dopo aver fatto clic su Crea, verrà visualizzato un riquadro nel dashboard che riflette lo stato di avanzamento della rete virtuale. Il riquadro verrà modificato durante la creazione della rete virtuale.

	![Pannello Creazione della rete virtuale](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png)