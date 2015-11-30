> [AZURE.NOTE]Questo argomento include esempi che usano i cmdlet di Azure Powershell. Azure PowerShell è attualmente disponibile in due versioni - 1.0 Preview e 0.9.8. Se si hanno script esistenti e non si vuole modificarli in questo momento, è possibile continuare a usare la versione 0.9.8. Quando si usa la versione 1.0, è consigliabile testare gli script in ambienti di preproduzione prima di usarli in produzione per evitare effetti imprevisti.
>
> Nella maggior parte dei casi, l'unica differenza tra le due versioni sta nel fatto che il nome del cmdlet della versione 1.0 segue il modello {verbo}-AzureRm{nome}, mentre quello della versione 0.9.8 non include **Rm**, ad esempio, New-AzureRmResourceGroup invece di New-AzureResourceGroup. Quando la differenza tra le versioni è più significativa, in questo argomento vengono illustrati esempi per entrambe le versioni.
>
> Quando si usa Azure PowerShell 0.9.8, è prima necessario abilitare la modalità Gestione risorse con il comando **Switch-AzureMode AzureResourceManager**. Questo comando non è necessario nella versione 1.0.
>
> Per informazioni sulla versione 1.0 e su come installarla e disinstallarla, vedere [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/). Per informazioni sulle modifiche significative apportate ai comandi di Gestione risorse, vedere [Modifiche ai cmdlet di Gestione risorse di Azure PowerShell](../articles/powershell-preview-resource-manager-changes.md).

<!---HONumber=Nov15_HO4-->