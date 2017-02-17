### <a name="troubleshoot-azure-diagnostics"></a>Risolvere i problemi relativi a Diagnostica di Azure

Se viene visualizzato il messaggio di errore seguente, il provider di risorse Microsoft.insights non è registrato:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Per registrare il provider di risorse, seguire questa procedura nel portale di Azure:

1.  Nel pannello di navigazione a sinistra fare clic su *Sottoscrizioni*.
2.  Selezionare la sottoscrizione identificata nel messaggio di errore.
3.  Fare clic su *Provider di risorse*.
4.  Trovare il provider *Microsoft.insights*.
5.  Fare clic sul collegamento *Registra*.

![Registrare il provider di risorse Microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Dopo aver registrato il provider di risorse *Microsoft.insights*, provare di nuovo a configurare la diagnostica.


<!--HONumber=Feb17_HO2-->


