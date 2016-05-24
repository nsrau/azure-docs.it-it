È possibile verificare una connessione VPN nel portale di Azure passando a **Gateway di rete virtuale**, **>** ***facendo clic sul nome del gateway*** **>** **Impostazioni** **>** **Connessioni**. Selezionando il nome della connessione è possibile visualizzare informazioni aggiuntive sulla connessione. Nell'esempio seguente la connessione non è connessa e non è presente alcun flusso di dati.


![Verificare la connessione](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### Per verificare la connessione tramite PowerShell

È anche possibile verificare che la connessione sia riuscita usando `Get-AzureRmVirtualNetworkGatewayConnection –Debug`. Per configurare i valori in modo che corrispondano ai propri, è possibile usare l'esempio di cmdlet seguente. Quando richiesto, selezionare "A" per eseguirli tutti.

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Al termine, scorrere i cmdlet per visualizzare i valori. Nell'esempio seguente lo stato di connessione risulta *Connesso* ed è possibile visualizzare i byte in ingresso e in uscita.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }