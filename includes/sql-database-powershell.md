
## <a name="start-your-powershell-session"></a>Avviare la sessione di PowerShell
È prima di tutto necessario che sia installata e in esecuzione la versione più recente di Azure PowerShell. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Molte nuove funzionalità del database SQL sono supportate solo con il [modello di distribuzione Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md). Negli esempi vengono quindi usati i [cmdlet di PowerShell per il database SQL di Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) per Resource Manager. I [cmdlet di gestione del servizio database SQL di Azure](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) del modello di distribuzione classica della gestione dei servizi sono supportati per compatibilità con le versioni precedenti, ma è consigliabile usare i cmdlet per Resource Manager.
> 
> 

Eseguire il cmdlet [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) per visualizzare una schermata di accesso in cui immettere le credenziali. Usare le stesse credenziali usate per l'accesso al portale di Azure.

```PowerShell
Add-AzureRmAccount
```

Se si hanno più sottoscrizioni, usare il cmdlet [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) per selezionare la sottoscrizione da usare nella sessione di PowerShell. Per visualizzare la sottoscrizione in uso nella sessione corrente di PowerShell, eseguire [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx). Per visualizzare tutte le sottoscrizioni, eseguire [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx).

```PowerShell
Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```
