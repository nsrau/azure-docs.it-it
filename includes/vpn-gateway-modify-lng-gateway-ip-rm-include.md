Per modificare l'indirizzo IP del gateway, usare il cmdlet `New-AzureRmVirtualNetworkGatewayConnection`. Purché si mantenga esattamente il nome esistente come nome del gateway di rete locale, le impostazioni saranno sovrascritte. A questo punto, il cmdlet "Set" non supporta la modifica dell'indirizzo IP del gateway.

### <a name="gwipnoconnection"></a>Come modificare l'indirizzo IP del gateway senza connessione gateway
Per aggiornare l'indirizzo IP del gateway per il gateway di rete locale che non ha ancora una connessione, usare l'esempio seguente. È anche possibile aggiornare i prefissi di indirizzo contemporaneamente. Le impostazioni specificate sovrascriveranno quelle esistenti. Assicurarsi di usare il nome del gateway di rete locale esistente. In caso contrario, si creerà un nuovo gateway di rete locale e quello esistente non sarà sovrascritto.

Usare l'esempio seguente, sostituendo i valori con i propri.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Come modificare l'indirizzo IP del gateway con una connessione gateway
Se esiste già una connessione gateway, prima di tutto è necessario rimuoverla. Quindi è possibile modificare l'indirizzo IP del gateway e ricreare una nuova connessione. Questo comporterà periodi di inattività per la connessione VPN.

> [!IMPORTANT]
> Non eliminare il gateway VPN. In caso contrario, sarà necessario ripercorrere i passaggi necessari per ricrearlo, nonché riconfigurare il router locale con il nuovo indirizzo IP che sarà assegnato al gateway appena creato.
> 
> 

1. Rimuovere la connessione. È possibile trovare il nome della connessione con il cmdlet `Get-AzureRmVirtualNetworkGatewayConnection`.
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName
2. Modificare il valore GatewayIpAddress. A questo punto è anche possibile modificare i prefissi di indirizzo, se necessario. Si noti che questa operazione sovrascrive le impostazioni del gateway di rete locale esistenti. Usare il nome del gateway di rete locale esistente quando si apporta la modifica, in modo che le impostazioni vengano sovrascritte. In caso contrario, si creerà un nuovo gateway di rete locale e quello esistente non sarà modificato.
   
        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
3. Creare la connessione. In questo esempio, viene configurato un tipo di connessione IPsec. Quando si ricrea la connessione, usare il tipo di connessione specificato per la configurazione in uso. Per altri tipi di connessione, vedere la pagina dei [cmdlet di PowerShell](https://msdn.microsoft.com/library/mt603611.aspx). Per ottenere il nome VirtualNetworkGateway, è possibile eseguire il cmdlet `Get-AzureRmVirtualNetworkGateway`.
   
    Impostare le variabili:
   
        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
   
    Creare la connessione:
   
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0810_2016-->