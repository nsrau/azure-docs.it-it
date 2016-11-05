
## <a name="start-your-powershell-session"></a>Avviare la sessione di PowerShell
Prima di tutto è necessario che sia installata ed eseguita la versione più recente di [Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) . Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../articles/powershell-install-configure.md).

> [!NOTE]
> Negli esempi inclusi in questo argomento viene usato il [modello di distribuzione Azure Resource Manager](../articles/resource-group-overview.md), quindi vengono impiegati gli esempi di [cmdlet di Azure Resource Manager](http://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Eseguire il cmdlet [**Add-AzureRmAccount**](http://msdn.microsoft.com/library/mt619267.aspx) per visualizzare una schermata di accesso in cui immettere le credenziali. Usare le stesse credenziali usate per l'accesso al portale di Azure.

    Add-AzureRmAccount

Se si hanno più sottoscrizioni, usare il cmdlet [**Set-AzureRmContext**](http://msdn.microsoft.com/library/mt619263.aspx) per selezionare la sottoscrizione da usare nella sessione di PowerShell. Per visualizzare la sottoscrizione in uso nella sessione corrente di PowerShell, eseguire [**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx). Per visualizzare tutte le sottoscrizioni, eseguire [**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'



<!--HONumber=Oct16_HO2-->


