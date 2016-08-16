### Per verificare la connessione tramite PowerShell

È possibile verificare se la connessione è riuscita usando il cmdlet `Get-AzureRmVirtualNetworkGatewayConnection` con o senza `-Debug`.

1. Per configurare i valori in modo che corrispondano ai propri, usare l'esempio di cmdlet seguente. Quando richiesto, selezionare "A" per eseguire "Tutti". Nell'esempio `-Name` fa riferimento al nome della connessione creata che si vuole testare.

		Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. Al termine dell'esecuzione del cmdlet, visualizzare i valori. Nell'esempio seguente lo stato di connessione risulta "Connesso" ed è possibile visualizzare i byte in ingresso e in uscita.

		Body:
		{
		  "name": "MyGWConnection",
		  "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
		  "properties": {
		    "provisioningState": "Succeeded",
		    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
		    "virtualNetworkGateway1": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
		teways/vnetgw1"
		    },
		    "localNetworkGateway2": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
		ways/LocalSite"
		    },
		    "connectionType": "IPsec",
		    "routingWeight": 10,
		    "sharedKey": "abc123",
		    "connectionStatus": "Connected",
		    "ingressBytesTransferred": 33509044,
		    "egressBytesTransferred": 4142431
		  }

### Per verificare la connessione usando il portale di Azure

Nel portale di Azure è possibile visualizzare lo stato della connessione passando alla connessione. Per eseguire questa operazione è possibile procedere diversi modi. Ecco un modo per passare alla connessione.

1. Nel [portale di Azure](http://portal.azure.com) passare a **Gateway di rete virtuali**. Fare clic sul nome di gateway.
2. Nel riquadro fare clic su **Connessioni** in **Impostazioni**. È possibile visualizzare lo stato di ogni connessione.
3. Per altre informazioni sulla connessione, fare clic sul nome corrispondente. Nella pagina Informazioni di base per la connessione prestare attenzione a **Stato connessione**. Lo stato sarà "Operazione riuscita" e "Connesso" dopo aver stabilito una connessione. È possibile controllare i flusso dei dati esaminando **Dati in entrata** e **Dati in uscita**.

	Nell'esempio seguente **Stato connessione** è "Non connesso".

	![Verificare la connessione](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)  

<!---HONumber=AcomDC_0810_2016-->