### <a name="noconnection"></a>Come aggiungere o rimuovere prefissi senza una connessione gateway
### <a name="to-add-additional-prefixes"></a>Per aggiungere prefissi aggiuntivi

Per aggiungere prefissi di indirizzo aggiuntivi a un gateway di rete locale creato, ma ancora privo di una connessione gateway, usare l'esempio seguente. Assicurarsi di modificare i valori sostituendoli con i propri.

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
```
### <a name="to-remove-an-address-prefix"></a>Per rimuovere un prefisso di indirizzo

Per rimuovere un prefisso di indirizzo da un gateway di rete locale che non ha una connessione VPN, usare l'esempio seguente. Escludere i prefissi non più necessari. In questo esempio, non è più necessario il prefisso 20.0.0.0/24 usato nell'esempio precedente. In tal modo, sarà possibile aggiornare il gateway di rete locale ed escludere tale prefisso.

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
```

### <a name="withconnection"></a>Come aggiungere o rimuovere prefissi con una connessione gateway
Se è stata creata la connessione gateway e si vogliono aggiungere o rimuovere i prefissi di indirizzo IP inclusi nel gateway di rete locale, è necessario seguire questa procedura nell'ordine indicato. Questo comporterà periodi di inattività per la connessione VPN. Quando si aggiornano i prefissi, si procede prima di tutto alla rimozione della connessione, alla modifica dei prefissi e quindi alla creazione di una nuova connessione. Negli esempi seguenti assicurarsi di modificare i valori sostituendoli con i propri.

> [!IMPORTANT]
> Non eliminare il gateway VPN. In caso contrario sarà necessario ripercorrere i passaggi necessari per ricrearlo, nonché riconfigurare il router locale con le nuove impostazioni.
> 
> 

1. Rimuovere la connessione.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
  ```
2. Modificare i prefissi di indirizzo IP per il gateway di rete locale.
   
  Impostare la variabile per LocalNetworkGateway.

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
  ```
   
  Modificare i prefissi.
   
  ```powershell
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```
3. Creare la connessione. In questo esempio, viene configurato un tipo di connessione IPsec. Quando si ricrea la connessione, usare il tipo di connessione specificato per la configurazione in uso. Per altri tipi di connessione, vedere la pagina dei [cmdlet di PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .
   
  Impostare la variabile per VirtualNetworkGateway.

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  Creare la connessione. Si noti che in questo esempio usa la variabile $local impostata nel passaggio precedente.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```
