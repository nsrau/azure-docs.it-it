---
title: Clonazione di app Web con PowerShell
description: Come clonare le app Web in nuove app Web con PowerShell.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonazione di app del servizio app di Azure con PowerShell
Con il rilascio di Microsoft Azure PowerShell versione 1.1.0 è stata aggiunta una nuova opzione a `New-AzureRMWebApp` che consente di clonare il contenuto di un'app Web esistente in una nuova app presente in un'area diversa o nella stessa area. In questo modo, i clienti possono distribuire una serie di app in aree diverse in modo semplice e rapido.

La clonazione di app è attualmente supportata solo per i piani di servizio app Premium. La nuova funzionalità usa le stesse limitazioni della funzionalità di backup di App Web. Vedere [Eseguire il backup di un'app Web nel servizio app di Azure](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Clonazione di un'app esistente
Scenario: si vuole clonare il contenuto di un'app Web esistente nell'area South Central US in una nuova app Web presente nell'area North Central US. Questa operazione può essere eseguita con la versione Azure Resource Manager del cmdlet PowerShell per creare una nuova app Web con l'opzione `-SourceWebApp`.

Conoscendo il nome del gruppo di risorse che include l'app Web di origine, è possibile usare il comando PowerShell seguente per ottenere informazioni sull'app Web di origine, denominata in questo caso `source-webapp`:

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Per creare un nuovo piano di servizio app, è possibile usare il comando `New-AzureRmAppServicePlan` come nell'esempio seguente:

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Con il comando `New-AzureRmWebApp` è possibile creare la nuova app Web nell'area North Central US e collegarla a un piano di servizio app Premium esistente. Inoltre, è possibile usare lo stesso gruppo di risorse dell'app Web di origine oppure definire un nuovo gruppo di risorse come illustrato nel comando seguente:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Per clonare un'app Web esistente, inclusi tutti gli slot di distribuzione associati, è necessario usare il parametro `IncludeSourceWebAppSlots`. Il comando PowerShell seguente mostra l'uso di questo parametro con il comando `New-AzureRmWebApp`:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Per clonare un'app Web esistente nella stessa area, è necessario creare un nuovo gruppo di risorse e un nuovo piano di servizio app nella stessa area e quindi usare il comando PowerShell seguente per clonare l'app Web:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonazione di un'app esistente in un ambiente del servizio App
Scenario: si vuole clonare il contenuto di un'app Web esistente nell'area South Central US in una nuova app Web presente in un ambiente del servizio app esistente.

Conoscendo il nome del gruppo di risorse che include l'app Web di origine, è possibile usare il comando PowerShell seguente per ottenere informazioni sull'app Web di origine, denominata in questo caso `source-webapp`:

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Conoscendo il nome dell'ambiente del servizio app e il nome del gruppo di risorse a cui appartiene tale ambiente, è possibile creare la nuova app Web nell'ambiente del servizio app esistente come illustrato nel comando seguente:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Il parametro `Location` è obbligatorio per motivi legacy, ma viene ignorato quando si crea l'app in un ambiente del servizio app. 

## <a name="cloning-an-existing-app-slot"></a>Clonazione dello slot di un'app esistente
Scenario: si vuole clonare lo slot di un'app Web esistente in una nuova app Web o in un nuovo slot dell'app Web. La nuova app Web può trovarsi nella stessa area dello slot dell'app Web originale o in un'area diversa.

Conoscendo il nome del gruppo di risorse che include l'app Web di origine, è possibile usare il comando PowerShell seguente per ottenere informazioni sullo slot dell'app Web di origine, denominato in questo caso `source-webappslot`, collegato all'app Web `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

Il comando seguente mostra la creazione di un clone dell'app Web di origine in una nuova app Web:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Configurazione di Gestione traffico durante la clonazione di un'app
Creare app Web presenti in più aree e configurare Gestione traffico di Azure in modo che il traffico venga instradato a tutte le app Web rappresenta uno scenario importante per assicurarsi che le app dei clienti siano altamente disponibili. Quando si clona un'app Web esistente, è possibile collegare entrambe le app Web a un nuovo profilo o a un profilo esistente di Gestione traffico. È supportata solo la versione Azure Resource Manager di Gestione traffico.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Creazione di un nuovo profilo di Gestione traffico durante la clonazione di un'app
Scenario: si vuole clonare un'app Web in un'altra area mentre si configura un profilo di Gestione traffico di Azure Resource Manager che include entrambe le app Web. Il comando seguente mostra la creazione di un clone dell'app Web di origine in una nuova app Web durante la configurazione di un nuovo profilo di Gestione traffico:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Aggiunta di una nuova app Web clonata a un profilo di Gestione traffico esistente
Scenario: si ha già un profilo di Gestione traffico di Azure Resource Manager a cui si vogliono aggiungere entrambe le app Web come endpoint. A tale scopo, è prima necessario assemblare l'ID del profilo di Gestione traffico esistente. Sono necessari l'ID di sottoscrizione, il nome del gruppo di risorse e il nome del profilo di Gestione traffico esistente.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Dopo aver recuperato l'ID di Gestione traffico, il comando seguente mostra la creazione di un clone dell'app Web di origine in una nuova app Web durante l'aggiunta di entrambe le app a un profilo di Gestione traffico esistente:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Restrizioni attuali
Questa funzionalità è attualmente disponibile in anteprima e nuove capacità verranno aggiunte in futuro. Di seguito sono riportate le limitazioni note relative alla versione corrente della funzionalità di clonazione delle app:

* Le impostazioni di ridimensionamento automatico non vengono clonate.
* Le impostazioni di pianificazione del backup non vengono clonate.
* Le impostazioni della rete virtuale non vengono clonate.
* Le informazioni sull'app non vengono configurate automaticamente nell'app Web di destinazione.
* Le impostazioni di Easy Auth non vengono clonate.
* L'estensione Kudu non viene clonata.
* Le regole TiP non vengono clonate.
* Il contenuto di database non viene clonato.
* Gli indirizzi IP in uscita cambiano in caso di clonazione in una diversa unità di scala.

### <a name="references"></a>Riferimenti
* [Clonazione di app Web](app-service-web-app-cloning.md)
* [Eseguire il backup di un'app Web nel servizio app di Azure](web-sites-backup.md)
* [Supporto di Azure Resource Manager per la versione di anteprima di Gestione traffico di Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introduzione all'ambiente del servizio app](environment/intro.md)
* [Uso di Azure PowerShell con Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

