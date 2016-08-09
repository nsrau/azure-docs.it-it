### <a name="noconnection"></a>Come aggiungere o rimuovere i prefissi senza una connessione del gateway VPN

- **Per aggiungere** prefissi di indirizzo aggiuntivi a un gateway di rete locale creato ma ancora privo di una connessione del gateway VPN, usare l'esempio seguente.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Per rimuovere** un prefisso di indirizzo da un gateway di rete locale che non ha una connessione VPN, usare l'esempio seguente. Escludere i prefissi non più necessari. In questo esempio, non è più necessario il prefisso 20.0.0.0/24 usato nell'esempio precedente. In tal modo, sarà possibile aggiornare il gateway di rete locale ed escludere tale prefisso.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Come aggiungere o rimuovere i prefissi con una connessione del gateway VPN

Se è stata creata la connessione VPN e si vuole aggiungere o rimuovere i prefissi di indirizzo IP contenuti nel gateway di rete locale, è necessario seguire questa procedura nell'ordine indicato. Questo comporterà periodi di inattività per la connessione VPN. Quando si aggiornano i prefissi, si procede prima di tutto alla rimozione della connessione, alla modifica dei prefissi e quindi alla creazione di una nuova connessione.

>[AZURE.IMPORTANT] Non eliminare il gateway VPN. In caso contrario sarà necessario ripercorrere i passaggi necessari per ricrearlo, nonché riconfigurare il router locale con le nuove impostazioni.
 
1. Specificare le variabili.

		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

2. Rimuovere la connessione.

		Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

3. Modificare i prefissi.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Creare la connessione. In questo esempio, viene configurato un tipo di connessione IPsec. Per altri tipi di connessione, vedere la pagina dei [cmdlet di PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).
	
		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
		-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec `
		-RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0803_2016-->