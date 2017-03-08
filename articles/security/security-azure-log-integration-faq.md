---
title: Domande frequenti sull&quot;integrazione dei log di Azure | Documentazione Microsoft
description: Queste domande frequenti riguardano l&quot;integrazione dei log di Azure.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: d31d4c390d5e03c8b206284c3ae75defed2a38af
ms.openlocfilehash: 83bedb42e8e685f5ea65e160ab7bfe128236ef82
ms.lasthandoff: 02/23/2017


---
# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Domande frequenti sull'integrazione dei log di Azure
Queste domande frequenti riguardano l'integrazione dei log di Azure, un servizio che consente di integrare log non elaborati delle risorse di Azure nei sistemi di gestione di informazioni ed eventi di sicurezza locali. Questa integrazione fornisce un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza associati alle applicazioni.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Come è possibile vedere gli account di archiviazione da cui l'integrazione dei log di Azure estrae i log delle VM di Azure?
Eseguire il comando **azlog source list**.

## <a name="how-can-i-update-the-proxy-configuration"></a>Come è possibile aggiornare la configurazione del proxy?
Se la configurazione del proxy non consente l'accesso di archiviazione di Azure direttamente, aprire il file **AZLOG.EXE.CONFIG** in **c:\Programmi\Integrazione dei log di Microsoft Azure**. Aggiornare il file in modo che includa la sezione **defaultProxy** con l'indirizzo del proxy dell'organizzazione. Al termine dell'aggiornamento, arrestare e avviare il servizio usando i comandi **net stop azlog** e **net start azlog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Come è possibile vedere le informazioni sulla sottoscrizione negli eventi di Windows?
Aggiungere il **subscriptionid** al nome descrittivo durante l'aggiunta dell'origine.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
L'XML dell'evento include i metadati illustrati di seguito, compreso l'ID sottoscrizione.

![XML dell'evento][1]

## <a name="error-messages"></a>messaggi di errore
### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Durante l'esecuzione del comando **azlog createazureid**, perché viene visualizzato il seguente errore?
Error:

  *Impossibile creare l'applicazione AAD - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37 - Motivo = "Accesso negato" - Messaggio = "Privilegi insufficienti per completare l'operazione".*

**Azlog createazureid** tenta di creare un'entità servizio in tutti i tenant di Azure AD per le sottoscrizioni a cui l'account di accesso di Azure può accedere. Se l'account di accesso di Azure è solo un utente Guest nel tenant di Azure AD, il comando non riesce e mostra l'errore "Privilegi insufficienti per completare l'operazione". Richiedere all'amministratore del tenant di aggiungere l'account come utente nel tenant.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>Durante l'esecuzione del comando **azlog authorize**, perché viene visualizzato il seguente errore?
Error:

  *Warning creating Role Assignment - AuthorizationFailed: The client janedo@microsoft.com' with object id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/write' over scope '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000'.* (Avviso durante la creazione dell'assegnazione del ruolo - Autorizzazione non riuscita: il client " con ID oggetto "fe9e03e4-4dad-4328-910f-fd24a9660bd2' non dispone di autorizzazione per eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' over scope '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000").

Il comando **azlog authorize** assegna il ruolo di Lettore all'entità servizio di Azure AD (creata con **azlog createazureid**) per le sottoscrizioni fornite. Se l'account di accesso di Azure non è un coamministratore o un proprietario della sottoscrizione, l'operazione ha esito negativo con il messaggio di errore "Autorizzazione non riuscita". Per completare l'operazione è necessario il controllo di accesso di Azure basato sui ruoli (RBAC) di coamministratore o proprietario.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>Dove si trova la definizione delle proprietà nel log di controllo?
Vedere:

* [Operazioni di controllo con Gestione risorse](../azure-resource-manager/resource-group-audit.md)
* [Elencare gli eventi di gestione in una sottoscrizione nell'API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Dove sono disponibili altre informazioni sugli avvisi del Centro sicurezza di Azure?
Vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Come è possibile scegliere quali informazioni raccogliere con la diagnostica delle VM?
Per informazioni dettagliate su come ottenere, modificare e impostare la configurazione di Diagnostica di Azure in Windows [(WAD)](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , vedere *Usare PowerShell per abilitare la Diagnostica di Azure in una macchina virtuale che esegue Windows* . Di seguito è riportato un esempio:

### <a name="get-the-wad-config"></a>Ottenere la configurazione WAD
    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>Modificare la configurazione WAD
L'esempio seguente è una configurazione in cui vengono raccolti solo gli ID evento 4624 e 4625 dal registro eventi di sicurezza. Gli eventi Microsoft Antimalware vengono raccolti dal registro eventi di sistema. Per informazioni dettagliate sull'uso di espressioni XPath, vedere [Utilizzo di eventi] (https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85) for details on the use of XPath expressions.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>Impostare la configurazione WAD
    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Dopo aver apportato modifiche, verificare l'account di archiviazione per assicurarsi che vengano raccolti gli eventi corretti.

Se si verificano problemi durante l'installazione e la configurazione, aprire una [richiesta di supporto](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request), selezionare "Integrazione log" come servizio per cui si richiede il supporto.


<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

