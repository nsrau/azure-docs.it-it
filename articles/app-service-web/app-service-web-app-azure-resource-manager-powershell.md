---
title: Comandi di PowerShell basati su Azure Resource Manager per app Web di Azure | Microsoft Docs
description: Informazioni su come usare i nuovi comandi di PowerShell basati su Azure Resource Manager per la gestione delle app Web di Azure.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: aelnably

---
# Uso di comandi di PowerShell basati su Azure Resource Manager per la gestione di app Web di Azure
Con il rilascio di Microsoft Azure PowerShell versione 1.0.0 sono stati aggiunti nuovi comandi che offrono all'utente la possibilità di usare comandi di PowerShell basati su Azure Resource Manager per gestire app Web.

Per informazioni sulla gestione di gruppi di risorse, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

Per l'elenco completo dei parametri e delle opzioni per i cmdlet di PowerShell basati su Azure Resource Manager per le app Web, vedere le [informazioni di riferimento complete](https://msdn.microsoft.com/library/mt619237.aspx).

## Gestione di piani di servizio app
### Creare un piano di servizio app
Per creare un nuovo piano di servizio app, usare il cmdlet **New-AzureRmAppServicePlan**.

Di seguito sono riportate le descrizioni dei diversi parametri.

* **Name**: nome del piano di servizio app.
* **Location**: località del piano di servizio.
* **ResourceGroupName**: gruppo di risorse che include il piano di servizio app appena creato.
* **Tier**: piano tariffario desiderato. Il valore predefinito è Free, le altre opzioni sono Shared, Basic, Standard e Premium.
* **WorkerSize**: dimensioni dei ruoli di lavoro. Se il parametro Tier specificato è Basic, Standard o Premium, il valore predefinito è small. Le altre opzioni sono Medium e Large.
* **NumberofWorkers**: numero di ruoli di lavoro nel piano di servizio app. Il valore predefinito è 1. 

Esempio di uso del cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### Creare un piano di servizio app in un ambiente del servizio app
Per creare un nuovo piano di servizio app in un ambiente del servizio app, è possibile utilizzare lo stesso comando **New-AzureRmAppServicePlan** con parametri aggiuntivi per specificare il nome dell'ambiente del servizio app e il nome del gruppo di risorse a cui appartiene.

Esempio di uso del cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Per altre informazioni sull'ambiente del servizio app, vedere [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md).

### Visualizzare un elenco dei piani di servizio app esistenti
Per visualizzare un elenco dei piani di servizio app esistenti, usare il cmdlet **Get-AzureRmAppServicePlan**.

Per visualizzare un elenco di tutti i piani di servizio app della propria sottoscrizione, utilizzare:

    Get-AzureRmAppServicePlan

Per visualizzare un elenco di tutti i piani di servizio app di uno specifico gruppo di risorse, usare:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Per ottenere un piano di servizio app specifico, usare:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### Configurare un piano di servizio app esistente
Per modificare le impostazioni di un piano di servizio app esistente, usare il cmdlet **Set-AzureRmAppServicePlan**. È possibile modificare il piano tariffario e il numero e le dimensioni dei ruoli di lavoro.

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### Ridimensionamento di un piano di servizio app
Per ridimensionare un piano di servizio app esistente, usare:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### Modifica delle dimensioni dei ruoli di lavoro in un piano di servizio app
Per modificare le dimensioni dei ruoli di lavoro in un piano di servizio app esistente, usare:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### Modifica del piano tariffario di un piano di servizio app
Per modificare il piano tariffario di un piano di servizio app esistente, usare:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### Eliminare un piano di servizio app esistente
Per eliminare un piano di servizio app esistente, è prima necessario spostare o eliminare tutte le app Web assegnate. È quindi possibile eliminare il piano di servizio app con il cmdlet **Remove-AzureRmAppServicePlan**.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## Gestione di app Web del servizio app
### Creare una nuova app Web
Per creare una nuova app Web, usare il cmdlet **New-AzureRmWebApp**.

Di seguito sono riportate le descrizioni dei diversi parametri.

* **Name**: nome dell'app Web.
* **AppServicePlan**: nome del piano di servizio usato per l'hosting dell'app Web.
* **ResourceGroupName**: gruppo di risorse che ospita il piano di servizio app.
* **Location**: località dell'app Web.

Esempio di uso del cmdlet:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### Creare una nuova app Web in un ambiente del servizio app
Per creare una nuova app Web in un ambiente del servizio app, è possibile usare lo stesso comando **New-AzureRmWebApp** con parametri aggiuntivi per specificare il nome dell'ambiente del servizio app e il nome del gruppo di risorse a cui appartiene.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Per altre informazioni sull'ambiente del servizio app, vedere [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md).

### Eliminare un'app Web esistente
Per eliminare un'app Web esistente, è possibile usare il cmdlet **Remove-AzureRmWebApp**. È necessario specificare il nome dell'app Web e il nome del gruppo di risorse.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### Visualizzare un elenco delle app Web esistenti
Per visualizzare un elenco delle app Web esistenti, usare il cmdlet **Get-AzureRmWebApp**.

Per visualizzare un elenco di tutte le app Web della propria sottoscrizione, usare:

    Get-AzureRmWebApp

Per visualizzare un elenco di tutte le app Web di uno specifico gruppo di risorse, usare:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Per ottenere un'app Web specifica, usare:

    Get-AzureRmWebApp -Name ContosoWebApp

### Configurare un'app Web esistente
Per modificare le impostazioni e le configurazioni di un'app Web esistente, usare il cmdlet **Set-AzureRmWebApp**. Per un elenco completo dei parametri, vedere le [informazioni di riferimento sul cmdlet](https://msdn.microsoft.com/library/mt652487.aspx).

Esempio (1): usare il cmdlet per modificare le stringhe di connessione

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Esempio (2): aggiungere impostazioni dell'app di esempio

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Esempio (3): impostare l'app Web per l'esecuzione in modalità a 64 bit

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### Modificare lo stato di un'app Web esistente
#### Riavviare un'app Web
Per riavviare un'app Web, è necessario specificare il nome e il gruppo di risorse dell'app Web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Arrestare un'app Web
Per arrestare un'app Web, è necessario specificare il nome e il gruppo di risorse dell'app Web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Avviare un'app Web
Per avviare un'app Web, è necessario specificare il nome e il gruppo di risorse dell'app Web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Gestire i profili di pubblicazione delle app Web
Ogni app Web ha un profilo di pubblicazione che può essere usato per pubblicare le app. Sui profili di pubblicazione possono essere eseguite alcune operazioni.

#### Recuperare il profilo di pubblicazione
Per recuperare il profilo di pubblicazione di un'app Web, usare:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Si noti che in questo modo il profilo di pubblicazione viene restituito sia nella riga di comando che in un file di testo.

#### Reimpostare il profilo di pubblicazione
Per reimpostare la password di pubblicazione per la distribuzione FTP e Web di un'app Web, usare:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Gestire i certificati delle app Web
Per informazioni sulla gestione dei certificati delle app Web, vedere l'articolo relativo all'[associazione di certificati SSL con PowerShell](app-service-web-app-powershell-ssl-binding.md).

### Passaggi successivi
* Per informazioni sul supporto di PowerShell per Azure Resource Manager, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).
* Per informazioni sugli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md).
* Per informazioni sulla gestione dei certificati SSL del servizio app con PowerShell, vedere l'articolo relativo all'[associazione di certificati SSL con PowerShell.](app-service-web-app-powershell-ssl-binding.md)
* Per l'elenco completo dei cmdlet di PowerShell basati su Azure Resource Manager per le app Web di Azure, vedere le [informazioni di riferimento](https://msdn.microsoft.com/library/mt619237.aspx).

<!---HONumber=AcomDC_0615_2016-->