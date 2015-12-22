I moduli possono essere installati in due modi diversi: usando PowerShell Gallery oppure l'Installazione guidata piattaforma Web. Il risultato finale è molto simile, anche se la modalità scelta per eseguire l'installazione determina il percorso di installazione predefinito dei moduli nel computer.

Quando si esegue l'installazione da PowerShell Gallery, il percorso predefinito dei file è *%ProgramFiles%\\WindowsPowerShell\\Modules*. Quando si usa l'Installazione guidata piattaforma Web, il percorso predefinito dei file è *%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell*. È opportuno scegliere e mantenere l'uno o l'altro approccio per evitare errori nei successivi aggiornamenti dei cmdlet. L'installazione guidata piattaforma Web riceve i cmdlet aggiornati ogni mese. PowerShell Gallery riceve le versioni aggiornate dei cmdlet quando vengono rilasciate. Per questo motivo, alcuni utenti preferiscono usare PowerShell Gallery.

Per informazioni aggiuntive sull'installazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

**Per installare moduli da PowerShell Gallery**

1. Per installare il modulo Gestione risorse direttamente da PowerShell Gallery, aprire Windows PowerShell come amministratore e digitare quanto segue:

		Install-Module AzureRM
		Install-AzureRM

2. Dopo aver installato i moduli, per poterli usare è necessario importarli:

		Import-AzureRM

**Per installare moduli con l'Installazione guidata piattaforma Web**

- È possibile installare moduli usando l'[Installazione guidata piattaforma Web](http://aka.ms/webpi-azps). Quando si fa clic sul collegamento, viene avviata l'installazione.

- Se si verificano errori durante l'uso dell'Installazione guidata piattaforma Web, potrebbe essere già stata installata una versione precedente dei cmdlet usando PowerShell Gallery. Vedere questo [post di blog](https://azure.microsoft.com/blog/azps-1-0/), per informazioni su come rimuovere le versioni precedenti dei moduli e tornare operativi. In genere gli errori si verificano quando è stata usata l'Installazione guidata piattaforma Web e si passa a PowerShell Gallery o viceversa. Il problema viene risolto rimuovendo i moduli installati in precedenza. Successivamente è possibile eseguire l'installazione dal nuovo percorso.

<!---HONumber=AcomDC_1217_2015-->