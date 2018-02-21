### <a name="enable-logging-with-diagnostics-settings"></a>Abilitare la registrazione con le impostazioni di diagnostica

1. Accedere al [portale di Azure][lnk-portal] e passare all'hub IoT.
1. Selezionare **Impostazioni di diagnostica**.
1. Selezionare **Attiva diagnostica**.

   ![Attivare la diagnostica][1]

1. Assegnare un nome alle impostazioni di diagnostica.
1. Scegliere dove si vogliono inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni:
   * Archivia in un account di archiviazione
   * Streaming in un hub eventi
   * Invia a Log Analytics
1. Scegliere le operazioni che si vuole monitorare e abilitare i log per tali operazioni. Le operazioni per cui le impostazioni di diagnostica possono inviare report sono:
   * connessioni
   * Telemetria dei dispositivi
   * Messaggi da cloud a dispositivo
   * Operazioni relative alle identità dei dispositivi
   * Caricamenti di file
   * Routing dei messaggi
   * Operazioni da cloud a dispositivi gemelli
   * Operazioni da dispositivo gemello a cloud
   * Operazioni di dispositivo gemello
   * Operazioni di processo
   * Metodi diretti  
1. Salvare le nuove impostazioni. 

Per attivare le impostazioni di diagnostica con PowerShell, usare il codice seguente:

```
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Le nuove impostazioni diventano effettive in circa 10 minuti. Trascorso questo tempo, i log vengono visualizzati nella destinazione di archiviazione configurata, nel pannello **Impostazioni di diagnostica**. Per altre informazioni sulla configurazione della diagnostica, vedere [Raccogliere e utilizzare i dati dei log dalle risorse di Azure][lnk-diagnostics-settings].

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
