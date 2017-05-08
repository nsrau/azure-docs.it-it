Per modificare l'indirizzo IP del gateway, usare il cmdlet 'New-AzureRmVirtualNetworkGatewayConnection'. Il cmdlet 'Set' non supporta al momento la modifica dell'indirizzo IP del gateway.

### <a name="gwipnoconnection"></a>Modificare l'indirizzo IP del gateway senza connessione gateway
Per modificare l'indirizzo IP del gateway per il gateway di rete locale che non ha ancora una connessione, usare l'esempio seguente. È anche possibile modificare contemporaneamente i prefissi di indirizzo. Assicurarsi di usare il nome del gateway di rete locale esistente per sovrascrivere le impostazioni correnti. In caso contrario si creerà un nuovo gateway di rete locale, invece di sovrascrivere quello esistente.

Usare l'esempio seguente, sostituendo i valori con i propri:

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>Modificare l'indirizzo IP del gateway con una connessione gateway esistente
Se esiste già una connessione gateway è prima necessario rimuoverla. Dopo aver rimosso la connessione sarà possibile modificare l'indirizzo IP del gateway e ricreare una nuova connessione. È anche possibile modificare contemporaneamente i prefissi di indirizzo. Questo comporterà periodi di inattività per la connessione VPN.

> [!IMPORTANT]
> Non eliminare il gateway VPN, altrimenti sarà necessario ripercorrere i passaggi necessari per ricrearlo. È anche necessario aggiornare il dispositivo VPN locale con il nuovo indirizzo IP del gateway VPN.
> 
> 

1. Rimuovere la connessione. Il nome della connessione può essere trovato usando il cmdlet 'Get-AzureRmVirtualNetworkGatewayConnection'.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Modificare il valore 'GatewayIpAddress'. È anche possibile modificare contemporaneamente i prefissi di indirizzo. Assicurarsi di usare il nome del gateway di rete locale esistente per sovrascrivere le impostazioni correnti. In caso contrario si creerà un nuovo gateway di rete locale, invece di sovrascrivere quello esistente.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Creare la connessione. In questo esempio viene configurato un tipo di connessione IPsec. Quando si ricrea la connessione, usare il tipo di connessione specificato per la configurazione in uso. Per altri tipi di connessione, vedere la pagina dei [cmdlet di PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Per ottenere il nome VirtualNetworkGateway è possibile eseguire il cmdlet 'Get-AzureRmVirtualNetworkGateway'.
   
    Impostare le variabili.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Creare la connessione.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```