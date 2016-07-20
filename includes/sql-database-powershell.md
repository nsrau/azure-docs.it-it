
## Avviare la sessione di PowerShell

Prima di tutto è necessario che la versione più recente di [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) sia installata e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../articles/powershell-install-configure.md).


>[AZURE.NOTE] Molte nuove funzionalità del database SQL sono supportate solo con il [modello di distribuzione Azure Resource Manager](../articles/resource-group-overview.md). Gli esempi usano quindi i [cmdlet di PowerShell per il database SQL di Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) per Resource Manager. I [cmdlet del database SQL di Azure (versione classica)](https://msdn.microsoft.com/library/azure/dn546723.aspx) del modello di distribuzione classica esistenti sono supportati per compatibilità con le versioni precedenti, ma è consigliabile l'uso dei cmdlet per Resource Manager.


Eseguire il cmdlet [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) per visualizzare una schermata di accesso in cui immettere le credenziali. Usare le stesse credenziali usate per l'accesso al portale di Azure.

	Add-AzureRmAccount

Se si hanno più sottoscrizioni usare il cmdlet [**Set AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) per selezionare la sottoscrizione da usare nella sessione di PowerShell. Per visualizzare quale sottoscrizione è usata nella sessione corrente di PowerShell, eseguire [**Get-AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx). Per visualizzare tutte le sottoscrizioni, digitare [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx).

	Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

<!---HONumber=AcomDC_0706_2016-->