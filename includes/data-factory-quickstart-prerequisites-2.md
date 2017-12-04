### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>Installare PowerShell
Installare l'ultima versione di PowerShell, se non è presente nel computer. 

1. Nel Web browser passare alla pagina relativa a [SDK e download per Azure SDK](https://azure.microsoft.com/downloads/). 
2. Fare clic su **Windows - Installazione** nella sezione **Strumenti da riga di comando** -> **PowerShell**. 
3. Per installare PowerShell, eseguire il file **MSI**. 

Per istruzioni dettagliate, vedere l'articolo su [come installare e configurare PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Accedere a PowerShell

1. Avviare **PowerShell** nel computer. Tenere aperto PowerShell fino alla fine di questa guida introduttiva. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.

    ![Avviare PowerShell](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. Eseguire questo comando e immettere lo stesso nome utente e la stessa password di Azure usati per accedere al portale di Azure:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Se si hanno più sottoscrizioni di Azure, eseguire questo comando per visualizzare tutte le sottoscrizioni per l'account:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **SubscriptionId** con l'ID della sottoscrizione di Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
