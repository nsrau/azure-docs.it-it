<properties
   pageTitle="Domande frequenti sull'integrazione dei log di Azure | Microsoft Azure"
   description="Queste domande frequenti riguardano l'integrazione dei log di Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/> 

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/> 

# Domande frequenti sull'integrazione dei log di Azure

Queste domande frequenti riguardano l'integrazione dei log di Azure, un servizio che consente di integrare log non elaborati delle risorse di Azure nei sistemi di gestione di informazioni ed eventi di sicurezza locali. Questa integrazione fornisce un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza associati alle applicazioni.

## Come è possibile vedere gli account di archiviazione da cui l'integrazione dei log di Azure estrae i log delle VM di Azure?

Eseguire il comando **azlog source list**.

## Come è possibile aggiornare la configurazione del proxy?

Se la configurazione del proxy non consente l'accesso di archiviazione di Azure direttamente, aprire il file **AZLOG.EXE.CONFIG** in **c:\\Programmi\\Integrazione dei log di Microsoft Azure**. Aggiornare il file in modo che includa la sezione **defaultProxy** con l'indirizzo del proxy dell'organizzazione. Al termine dell'aggiornamento, arrestare e avviare il servizio usando i comandi **net stop azlog** e **net start azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## Come è possibile vedere le informazioni sulla sottoscrizione negli eventi di Windows?

Aggiungere il **subscriptionid** al nome descrittivo durante l'aggiunta dell'origine.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

L'XML dell'evento include i metadati illustrati di seguito, compreso l'ID sottoscrizione.

![XML dell'evento][1] 

## messaggi di errore

### Durante l'esecuzione del comando **azlog createazureid**, perché viene visualizzato il seguente errore?

Error:

  *Impossibile creare l'applicazione AAD - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37 - Motivo = "Accesso negato" - Messaggio = "Privilegi insufficienti per completare l'operazione".*

Il comando **azlog createazureid** tenta di creare un'entità servizio in tutti i tenant di Azure AD per le sottoscrizioni a cui l'account di accesso di Azure può accedere. Se l'account di accesso di Azure è solo un utente Guest nel tenant di Azure AD, il comando non riesce e mostra l'errore "Privilegi insufficienti per completare l'operazione". Richiedere all'amministratore del tenant di aggiungere l'account come utente nel tenant.

### Durante l'esecuzione del comando **azlog authorize**, perché viene visualizzato il seguente errore?

Error:

  *Avviso creazione assegnazione di ruolo - AuthorizationFailed: il client janedo@microsoft.com' con ID oggetto "fe9e03e4-4dad-4328-910f-fd24a9660bd2" non dispone dell'autorizzazione per eseguire l'azione "Microsoft.Authorization/roleAssignments/write" sull'ambito "/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000".*

Il comando **azlog authorize** assegna il ruolo di Lettore all'entità servizio di Azure AD (creata con **azlog createazureid**) per le sottoscrizioni fornite. Se l'account di accesso di Azure non è un coamministratore o un proprietario della sottoscrizione, l'operazione ha esito negativo con il messaggio di errore "Autorizzazione non riuscita". Per completare l'operazione è necessario il controllo di accesso di Azure basato sui ruoli (RBAC) di coamministratore o proprietario.

## Dove si trova la definizione delle proprietà nel log di controllo?

Vedere:

- [Operazioni di controllo con Gestione risorse](../resource-group-audit.md)
- [Elencare gli eventi di gestione in una sottoscrizione nell'API REST di Azure Insights](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## Dove sono disponibili altre informazioni sugli avvisi del Centro sicurezza di Azure?

Vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md).

## Come è possibile scegliere quali informazioni raccogliere con la diagnostica delle VM?

Per informazioni dettagliate su come ottenere, modificare e impostare la configurazione di Diagnostica di Azure in Windows *(WAD)*, vedere [Usare PowerShell per abilitare la Diagnostica di Azure in una macchina virtuale che esegue Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md). Di seguito è riportato un esempio:

### Ottenere la configurazione WAD

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### Modificare la configurazione WAD

L'esempio seguente è una configurazione in cui vengono raccolti solo gli ID evento 4624 e 4625 dal registro eventi di sicurezza. Gli eventi Microsoft Antimalware vengono raccolti dal registro eventi di sistema. Per informazioni dettagliate sull'uso delle espressioni XPath, vedere [Utilizzo degli eventi](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85) .

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### Impostare la configurazione WAD

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Dopo aver apportato modifiche, verificare l'account di archiviazione per assicurarsi che vengano raccolti gli eventi corretti.

In caso di domande sull'integrazione dei log di Azure, inviare un messaggio di posta elettronica all'indirizzo [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references--> 
[1]: ./media/security-azure-log-integration-faq/event-xml.png

<!---HONumber=AcomDC_0921_2016-->