---
title: Abilitare la registrazione delle risorse in gestione traffico di Azure
description: Informazioni su come abilitare la registrazione delle risorse per il profilo di gestione traffico e accedere ai file di log creati come risultato.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: rohink
ms.openlocfilehash: 09c04d727e42f88bb376a59d66a23688e16abb13
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089057"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Abilitare la registrazione delle risorse in gestione traffico di Azure

Questo articolo descrive come abilitare la raccolta dei log delle risorse di diagnostica e accedere ai dati di log per un profilo di gestione traffico.

I log delle risorse di gestione traffico di Azure possono fornire informazioni sul comportamento della risorsa del profilo di gestione traffico. È possibile, ad esempio, usare i dati di log del profilo per determinare il motivo per cui si è verificato il timeout di singoli probe rispetto a un endpoint.

## <a name="enable-resource-logging"></a>Abilitare la registrazione delle risorse

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/powershell) oppure in PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue PowerShell dal computer, è necessario il modulo Azure PowerShell versione 1.0.0 o successiva. Per trovare la versione installata, è possibile eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per accedere ad Azure.

1. **Recuperare il profilo di Gestione traffico:**

    Per abilitare la registrazione delle risorse, è necessario l'ID di un profilo di gestione traffico. Recuperare il profilo di gestione traffico per cui si vuole abilitare la registrazione delle risorse con [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Nell'output sono incluse informazioni sull'ID del profilo di Gestione traffico.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Abilitare la registrazione delle risorse per il profilo di gestione traffico:**

    Abilitare la registrazione delle risorse per il profilo di gestione traffico usando l'ID ottenuto nel passaggio precedente con [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Il comando seguente consente di archiviare i log dettagliati relativi al profilo di Gestione traffico in uno specifico account di archiviazione di Azure. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Verificare le impostazioni di diagnostica:**

      Verificare le impostazioni di diagnostica per il profilo di Gestione traffico con [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Il comando seguente consente di visualizzare le categorie registrate per una risorsa.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Assicurarsi che tutte le categorie di log associate alla risorsa del profilo di Gestione traffico vengano visualizzate come abilitate. Verificare inoltre che l'account di archiviazione sia configurato correttamente.

## <a name="access-log-files"></a>Accedere ai file di log
1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare all'account di archiviazione di Azure nel portale.
2. Nella pagina **Panoramica** dell'account di archiviazione di Azure, in **Servizi**, selezionare **BLOB**.
3. Per **Contenitori** selezionare **insights-log-probehealthstatusevents** e scorrere verso il basso fino al file PT1H.json e fare clic su **Download** per scaricare e salvare una copia di questo file di log.

    ![Accedere ai file di log del profilo di Gestione traffico da un archivio BLOB](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schema dei log di Gestione traffico

Tutti i log delle risorse disponibili tramite monitoraggio di Azure condividono uno schema di primo livello comune, con la flessibilità che consente a ogni servizio di emettere proprietà univoche per gli eventi. Per lo schema dei log delle risorse di livello superiore, vedere [servizi, schemi e categorie supportati per i log delle risorse di Azure](../azure-monitor/platform/tutorial-dashboards.md).

La tabella seguente include lo schema dei log specifico della risorsa del profilo di Gestione traffico di Azure.

|Nome campo|Tipo di campo|Definizione|Esempio|
|----|----|---|---|
|EndpointName|Stringa|Nome dell'endpoint di Gestione traffico di cui viene registrato lo stato di integrità.|*myPrimaryEndpoint*|
|Stato|Stringa|Stato di integrità dell'endpoint di Gestione traffico di cui è stato creato il probe. Lo stato può essere **Up** (Attivo) o **Down** (Non attivo).|**Fino**|
|||||

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [Monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

