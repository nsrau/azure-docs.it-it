Se è necessario modificare i prefissi per il sito locale, usare le istruzioni seguenti. Vengono forniti due set di istruzioni che dipendono dal fatto di aver già creato la connessione del gateway VPN.

### Aggiungere o rimuovere i prefissi senza una connessione del gateway VPN


- **Per aggiungere** prefissi di indirizzi aggiuntivi a un sito locale creato, ma che non ha ancora una connessione di gateway VPN, usare l'esempio seguente.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Per rimuovere** un prefisso di indirizzo da un sito locale che non ha una connessione VPN, usare l'esempio seguente. Escludere i prefissi non più necessari. In questo esempio, non è più necessario il prefisso 20.0.0.0/24 (dell'esempio precedente). In tal modo, sarà possibile aggiornare il sito locale ed escludere tale prefisso.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Aggiungere o rimuovere i prefissi con una connessione del gateway VPN

Se è stata creata la connessione VPN e si desidera aggiungere o rimuovere i prefissi di indirizzo IP contenuti nel sito locale, è necessario eseguire i passaggi seguenti nell'ordine riportato. Ciò comporterà periodi di inattività per la connessione VPN, in quanto sarà necessario rimuovere e ricreare il gateway. Tuttavia, poiché è stato richiesto un indirizzo IP per la connessione, non sarà necessario riconfigurare il router VPN locale a meno che non si decide di modificare i valori usati in precedenza.

 
1. Rimuovere la connessione del gateway. 
2. Modificare i prefissi per il sito locale. 
3. Creare una nuova connessione del gateway. 

È possibile usare l'esempio seguente come linea guida.


	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0107_2016-->