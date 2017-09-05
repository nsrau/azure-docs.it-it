Prima di iniziare questa configurazione, è necessario accedere all'account Azure. Il cmdlet richiede le credenziali di accesso per l'account di Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../articles/powershell-azure-resource-manager.md).

Per accedere, aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

```powershell
Login-AzureRmAccount
```

Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

Specificare la sottoscrizione da usare.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```