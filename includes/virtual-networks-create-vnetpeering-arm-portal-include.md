## Come creare un peering reti virtuali nel portale di Azure

Per creare un peering reti virtuali basato sullo scenario precedente tramite il portale di Azure, seguire questa procedura.

1. Da un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Per stabilire un peering reti virtuali, è necessario creare due collegamenti, uno per ogni direzione, tra due reti virtuali. È possibile creare il collegamento per il peering reti virtuali prima da VNET1 a VNET2. Nel portale fare clic su **Esplora** e scegliere **Reti virtuali**.

	![Creare un peering reti virtuali nel portale di Azure](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure01.png)

3. Nel pannello Reti virtuali scegliere VNET1, fare clic su Peer e quindi su Aggiungi.

	![Scegliere il peering](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure02.png)

4. Nel pannello Aggiungi peering assegnare un nome al collegamento per il peering LinkToVnet2, scegliere la sottoscrizione e la rete virtuale peer VNET2. Fare clic su OK.

	![Collegamento alla rete virtuale](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure03.png)

5. Dopo la creazione del collegamento per il peering reti virtuali, sarà possibile visualizzare lo stato del collegamento come segue:

	![Stato del collegamento](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure04.png)

6. Creare quindi il collegamento per il peering reti virtuali da VNET2 a VNET1. Nel pannello Reti virtuali scegliere VNET2, fare clic su Peer e quindi su Aggiungi.

	![Peer da un'altra rete virtuale](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure05.png)

7. Nel pannello Aggiungi peering assegnare un nome al collegamento per il peering LinkToVnet1, scegliere la sottoscrizione e la rete virtuale peer. Fare clic su OK.

	![Pannello Creazione della rete virtuale](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure06.png)

8. Dopo la creazione del collegamento per il peering reti virtuali, sarà possibile visualizzare lo stato del collegamento come segue:

	![Stato del collegamento finale](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure07.png)

9. Controllare lo stato di LinkToVnet2 che ora risulta anch'esso Connesso.

	![Stato del collegamento finale 2](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure08.png)

10. NOTA: il peering reti virtuali viene stabilito solo se entrambi i collegamenti risultano connessi.

<!---HONumber=AcomDC_0803_2016-->