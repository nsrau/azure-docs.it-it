---
title: Raccogliere metriche delle risorse PaaS di Azure con Log Analytics | Microsoft Docs
description: Informazioni su come abilitare la raccolta di metriche delle risorse PaaS di Azure con PowerShell per la conservazione e l'analisi in Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: magoedte
ms.openlocfilehash: 83491c4902dabc6bab1e222551298cfaffbaecf4
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Configurare la raccolta di metriche delle risorse PaaS di Azure con Log Analytics

Le risorse PaaS (Platform as a Service) di Azure, come SQL di Azure e Siti Web di Azure (app Web), possono fornire dati sulle metriche di prestazioni in modo nativo a Log Analytics. Questo script consente agli utenti di abilitare la registrazione delle metriche per le risorse PaaS già distribuite in uno specifico gruppo di risorse o in un'intera sottoscrizione. 

Attualmente, non è possibile abilitare la registrazione delle metriche per risorse PaaS tramite il portale di Azure. È necessario quindi usare uno script di PowerShell. Combinata con gli strumenti di monitoraggio di Log Analytics, la funzionalità nativa di registrazione delle metriche consente di monitorare le risorse di Azure su larga scala. 

## <a name="prerequisites"></a>prerequisiti
Prima di procedere, verificare che nel computer siano installati i moduli di Azure Resource Manager seguenti:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>È consigliabile che i moduli di Azure Resource Manager siano tutti della stessa versione, in modo da garantire la compatibilità quando si eseguiranno i comandi di Azure Resource Manager da PowerShell.
>
Per installare la versione più recente dei moduli di Azure Resource Manager nel computer in uso, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Abilitare Diagnostica di Azure  
Per configurare Diagnostica di Azure per risorse PaaS, è necessario eseguire lo script **Enable-AzureRMDiagnostics.ps1**, disponibile in [PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript).  Lo script supporta gli scenari seguenti:
  
* Definizione di una risorsa correlata a uno o più gruppi di risorse in una sottoscrizione  
* Definizione di una risorsa correlata a uno specifico gruppo di risorse in una sottoscrizione  
* Riconfigurazione di una risorsa per essere inoltrata a un'altra area di lavoro

Sono supportate solo le risorse che eseguono la raccolta di metriche con Diagnostica di Azure e le inviano direttamente a Log Analytics.  Per un elenco dettagliato, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics](log-analytics-azure-storage.md) 

Eseguire la procedura seguente per scaricare ed eseguire lo script.

1.  Nella schermata iniziale di Windows digitare **PowerShell** e fare doppio clic su PowerShell nei risultati della ricerca.  Selezionare **Esegui come amministratore** dal menu.   
2. Salvare il file di script **Enable-AzureRMDiagnostics.ps1** in locale eseguendo il comando seguente e specificando il percorso in cui archiviare lo script.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Eseguire `Login-AzureRmAccount` per creare una connessione con Azure.   
4. Eseguire lo script seguente `.\Enable-AzureRmDiagnostics.ps1` senza parametri per abilitare la raccolta dei dati da una risorsa specifica nella sottoscrizione o con il parametro `-ResourceGroup <myResourceGroup>` per specificare una risorsa in un determinato gruppo di risorse.   
5. Se si hanno più sottoscrizioni, selezionare quella appropriata dall'elenco immettendo il valore corretto.<br><br> ![Selezionare la sottoscrizione restituita dallo script](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> In caso contrario, viene automaticamente selezionata una singola sottoscrizione disponibile.
6. Lo script restituisce quindi un elenco delle aree di lavoro di Log Analytics registrate nella sottoscrizione.  Selezionare l'area di lavoro appropriata dall'elenco.<br><br> ![Selezionare l'area di lavoro restituita dallo script](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Selezionare la risorsa di Azure da cui si vuole abilitare la raccolta. Se, ad esempio, si digita 5, si abilita la raccolta dati per i database SQL Azure.<br><br> ![Selezionare il tipo di risorsa restituita dallo script](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   È possibile selezionare solo risorse che eseguono la raccolta di metriche con Diagnostica di Azure e le inviano direttamente a Log Analytics.  Verrà visualizzato il valore **True** sotto la colonna **Metriche** per l'elenco di risorse individuate nella sottoscrizione o nel gruppo di risorse specificato.    
8. Verrà chiesto di confermare la selezione.  Immettere **Y** per abilitare la registrazione delle metriche per tutte le risorse selezionate per l'ambito definito, che in questo esempio corrispondono a tutti i database SQL presenti nella sottoscrizione.  

Lo script verrà eseguito in tutte le risorse corrispondenti ai criteri selezionati e abiliterà la raccolta delle metriche da tali risorse. Al termine, verrà visualizzato un messaggio con cui si informa che la configurazione è stata completata.  

Subito dopo il completamento, nel repository di Log Analytics inizieranno a essere visualizzati i dati raccolti dalla risorsa PaaS di Azure.  Viene creato un record con tipo `AzureMetrics`; l'analisi di questi record è supportata dalle soluzioni di gestione [Analisi SQL di Azure ](log-analytics-azure-sql.md) e [Analisi app Web di Azure](log-analytics-azure-web-apps-analytics.md).   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Aggiornare una risorsa per l'invio di dati a un'altra area di lavoro
Se si ha una risorsa che sta già inviando dati a un'area di lavoro di Log Analytics e successivamente si decide di riconfigurarla in modo che faccia riferimento a un'altra area di lavoro, è possibile eseguire lo script con il parametro `-Update`.  

**Esempio:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Verrà chiesto di fornire le stesse informazioni specificate durante l'esecuzione dello script per l'avvio della configurazione iniziale.  

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 

* Usare i [Campi personalizzati](log-analytics-custom-fields.md) per analizzare i record degli eventi nei singoli campi.

* Vedere [Creare un dashboard personalizzato da usare in Log Analytics](log-analytics-dashboards.md) per informazioni su come visualizzare le ricerche nei log in modo significativo per l'organizzazione.