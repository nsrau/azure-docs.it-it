È necessario creare un endpoint con carico bilanciato per ogni macchina virtuale che ospita una replica di Azure. Se si dispongono di repliche in più aree, ogni replica per tale area deve essere nello stesso servizio cloud nella stessa rete virtuale. Le repliche di creazione del gruppo di disponibilità che si estendono su più aree di Azure richiedono la configurazione di più reti virtuali. Per altre informazioni sulla configurazione della connettività tra reti virtuali, vedere [Configurare la connettività tra reti virtuali](../articles/vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Nel portale di Azure, passare a ogni macchina virtuale che ospita una replica e visualizzare i dettagli.
2. Scegliere la scheda **Endpoint** per ognuna delle macchine virtuali.
3. Verificare che il **nome** e la **porta pubblica** specificati per l'endpoint del listener non siano già in uso. Nell'esempio seguente, il nome è "MyEndpoint" e la porta è "1433".
4. Nel client locale, scaricare e installare [l'ultimo modulo PowerShell](https://azure.microsoft.com/downloads/).
5. Avviare **Azure PowerShell**. Viene aperta una nuova sessione di PowerShell con i moduli amministrativi di Azure caricati.
6. Eseguire **Get-AzurePublishSettingsFile**. Questo cmdlet conduce a un browser per scaricare un file di impostazioni di pubblicazione in una directory locale. Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure.
7. Eseguire il comando **Import-AzurePublishSettingsFile** con il percorso del file di impostazioni di pubblicazione che si è scaricato:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Una volta importato il file di impostazioni di pubblicazione, è possibile gestire la sottoscrizione di Azure nella sessione di PowerShell.

<!--HONumber=Nov16_HO3-->


