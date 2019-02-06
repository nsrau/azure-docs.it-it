---
title: Abilitare la registrazione diagnostica in Gestione traffico di Azure
description: Informazioni su come abilitare la registrazione diagnostica per il profilo personale di Gestione traffico e accedere ai file di log creati come risultato.
services: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: kumud
ms.openlocfilehash: abdc50d6d3d27ab7611994089345a997afc72cae
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082612"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Abilitare la registrazione diagnostica in Gestione traffico di Azure

Questo articolo descrive come abilitare la registrazione diagnostica e accedere ai dati di log per un profilo di Gestione traffico.

I log di diagnostica di Gestione traffico di Azure possono offrire informazioni dettagliate sul comportamento della risorsa del profilo di Gestione traffico. È possibile, ad esempio, usare i dati di log del profilo per determinare il motivo per cui si è verificato il timeout di singoli probe rispetto a un endpoint.

## <a name="enable-diagnostic-logging"></a>Abilitare la registrazione diagnostica

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/powershell) oppure in PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue PowerShell dal computer, è necessario il modulo *AzureRM* di PowerShell versione 6.13.1 o successiva. Per trovare la versione installata, è possibile eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per accedere ad Azure.

1. **Recuperare il profilo di Gestione traffico:**

    Per abilitare la registrazione diagnostica, è necessario l'ID di un profilo di Gestione traffico. Recuperare il profilo di Gestione traffico per il quale si vuole abilitare la registrazione diagnostica con [Get-AzureRmTrafficManagerProfile](/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile). Nell'output sono incluse informazioni sull'ID del profilo di Gestione traffico.

    ```azurepowershell-interactive
    Get-AzureRmTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Abilitare la registrazione diagnostica per il profilo di Gestione traffico:**

    Abilitare la registrazione diagnostica per il profilo di Gestione traffico usando l'ID ottenuto nel passaggio precedente con [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/set-azurermdiagnosticsetting?view=latest). Il comando seguente consente di archiviare i log dettagliati relativi al profilo di Gestione traffico in uno specifico account di archiviazione di Azure. 

      ```azurepowershell-interactive
    Set-AzureRmDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Verificare le impostazioni di diagnostica:**

      Verificare le impostazioni di diagnostica per il profilo di Gestione traffico con [Get-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermdiagnosticsetting?view=latest). Il comando seguente consente di visualizzare le categorie registrate per una risorsa.

     ```azurepowershell-interactive
     Get-AzureRmDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Assicurarsi che tutte le categorie di log associate alla risorsa del profilo di Gestione traffico vengano visualizzate come abilitate. Verificare inoltre che l'account di archiviazione sia configurato correttamente.

## <a name="access-log-files"></a>Accedere ai file di log
1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare all'account di archiviazione di Azure nel portale.
2. Nella pagina **Panoramica** dell'account di archiviazione di Azure, in **Servizi**, selezionare **BLOB**.
3. Per **Contenitori** selezionare **insights-log-probehealthstatusevents** e scorrere verso il basso fino al file PT1H.json e fare clic su **Download** per scaricare e salvare una copia di questo file di log.

    ![Accedere ai file di log del profilo di Gestione traffico da un archivio BLOB](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schema dei log di Gestione traffico

Tutti i log di diagnostica disponibili tramite Monitoraggio di Azure condividono uno schema di primo livello comune, con la flessibilità necessaria affinché ogni servizio crei proprietà univoche per i propri eventi. Per lo schema dei log di diagnostica di primo livello, vedere [Servizi, schemi e categorie supportati per i log di Diagnostica di Azure](../azure-monitor/platform/tutorial-dashboards.md).

La tabella seguente include lo schema dei log specifico della risorsa del profilo di Gestione traffico di Azure.

|||||
|----|----|---|---|
|**Nome campo**|**Tipo di campo**|**Definizione**|**Esempio**|
|EndpointName|string|Nome dell'endpoint di Gestione traffico di cui viene registrato lo stato di integrità.|*myPrimaryEndpoint*|
|Stato|string|Stato di integrità dell'endpoint di Gestione traffico di cui è stato creato il probe. Lo stato può essere **Up** (Attivo) o **Down** (Non attivo).|**Attivo**|
|||||

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [Monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

