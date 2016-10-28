È necessario creare un endpoint con carico bilanciato per ogni macchina virtuale che ospita una replica di Azure. Se si dispongono di repliche in più aree, ogni replica per tale area deve essere nello stesso servizio cloud nella stessa rete virtuale. Le repliche di creazione del gruppo di disponibilità che si estendono su più aree di Azure richiedono la configurazione di più reti virtuali. Per ulteriori informazioni sulla configurazione della connettività tra reti virtuali, vedere[configurare connettività tra reti virtuali](../articles/vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Nel portale di Azure, passare a ogni macchina virtuale che ospita una replica e visualizzare i dettagli.

1. Scegliere la scheda **Endpoint**per ognuna delle macchine virtuali.

1. Verificare che **Nome**e**Porta pubblica** dell’endpoint del listener che si desidera utilizzare non siano già in uso. Nell'esempio seguente, il nome è "MyEndpoint" e la porta è "1433".

1. Nel client locale, scaricare e installare[l'ultimo modulo PowerShell](https://azure.microsoft.com/downloads/).

1. Avviare **Azure PowerShell**. Viene aperta una nuova sessione di PowerShell con i moduli amministrativi di Azure caricati.

1. Eseguire**Get-AzurePublishSettingsFile**. Questo cmdlet conduce a un browser per scaricare un file di impostazioni di pubblicazione in una directory locale. Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure.

1. Eseguire il comando**Import-AzurePublishSettingsFile** con il percorso del file di impostazioni di pubblicazione che si è scaricato:

		Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

	Una volta importato il file di impostazioni di pubblicazione, è possibile gestire la sottoscrizione di Azure nella sessione di PowerShell.
<!----HONumber=AcomDC_0128_2016-->