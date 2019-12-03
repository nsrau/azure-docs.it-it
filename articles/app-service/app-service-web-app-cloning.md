---
title: Clonare un'app con PowerShell
description: Informazioni su come clonare l'app di Servizio app in una nuova app tramite PowerShell. Viene analizzata una vasta gamma di scenari di clonazione, inclusa l'integrazione di gestione traffico.
author: ahmedelnably
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: ccff07009d2f46f6d91b8e3c57158aa6ede3607e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671425"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonazione di app del servizio app di Azure con PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Con il rilascio di Microsoft Azure PowerShell versione 1.1.0 è stata aggiunta una nuova opzione a `New-AzWebApp` che consente di clonare il contenuto di un'app di Servizio app esistente in una nuova app presente in un'area diversa o nella stessa area. In questo modo, i clienti possono distribuire una serie di app in aree diverse in modo semplice e rapido.

La clonazione di app è supportata per i piani di servizio app standard, Premium, Premium v2 e isolated. La nuova funzionalità usa le stesse limitazioni della funzionalità di backup di Servizio app. Vedere [Eseguire il backup di un'app in Servizio app di Azure](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Clonazione di un'app esistente
Scenario: un'app esistente nell'area Stati Uniti centro-meridionali e si vuole clonare il contenuto in una nuova app nell'area Stati Uniti centro-settentrionali. Questa operazione può essere eseguita con la versione Azure Resource Manager del cmdlet PowerShell per creare una nuova app con l'opzione `-SourceWebApp`.

Conoscendo il nome del gruppo di risorse che include l'app di origine, è possibile usare il comando PowerShell seguente per ottenere informazioni sull'app di origine, denominata in questo caso `source-webapp`:

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Per creare un nuovo piano di servizio app, è possibile usare il comando `New-AzAppServicePlan` come nell'esempio seguente:

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Con il comando `New-AzWebApp` è possibile creare la nuova app nell'area Stati Uniti centro-settentrionali e collegarla a un piano di servizio app esistente. Inoltre, è possibile usare lo stesso gruppo di risorse dell'app di origine oppure definire un nuovo gruppo di risorse, come illustrato nel comando seguente:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Per clonare un'app esistente, inclusi tutti gli slot di distribuzione associati, è necessario usare il parametro `IncludeSourceWebAppSlots`.  Si noti che il parametro `IncludeSourceWebAppSlots` è supportato solo per la clonazione di un'intera app, inclusi tutti i relativi slot. Il comando PowerShell seguente mostra l'uso di questo parametro con il comando `New-AzWebApp`:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Per clonare un'app esistente nella stessa area, è necessario creare un nuovo gruppo di risorse e un nuovo piano di Servizio app nella stessa area e quindi usare il comando PowerShell seguente per clonare l'app:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonazione di un'app esistente in un ambiente del servizio App
Scenario: un'app esistente nell'area Stati Uniti centro-meridionali e si vuole clonare il contenuto in una nuova app in un ambiente del servizio app esistente (ASE).

Conoscendo il nome del gruppo di risorse che include l'app di origine, è possibile usare il comando PowerShell seguente per ottenere informazioni sull'app di origine, denominata in questo caso `source-webapp`:

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Conoscendo il nome dell'ambiente del servizio app e il nome del gruppo di risorse a cui appartiene tale ambiente, è possibile creare la nuova app nell'ambiente del servizio app esistente, come illustrato nel comando seguente:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Il parametro `Location` è obbligatorio per motivi legacy, ma viene ignorato quando si crea l'app in un ambiente del servizio app. 

## <a name="cloning-an-existing-app-slot"></a>Clonazione dello slot di un'app esistente
Scenario: si vuole clonare uno slot di distribuzione esistente di un'app in una nuova app o in un nuovo slot. La nuova app può trovarsi nella stessa area dello slot dell'app originale o in un'area diversa.

Conoscendo il nome del gruppo di risorse che include l'app di origine, è possibile usare il comando PowerShell seguente per ottenere informazioni sullo slot dell'app di origine, denominata in questo caso `source-appslot`, legata a `source-app`:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Il comando seguente mostra la creazione di un clone dell'app di origine in una nuova app:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Configurazione di Gestione traffico durante la clonazione di un'app
Creare app presenti in più aree e configurare Gestione traffico di Azure in modo che il traffico venga instradato a tutte le app rappresenta uno scenario importante per assicurarsi che le app dei clienti siano altamente disponibili. Quando si clona un'app esistente, è possibile collegare entrambe le app a un nuovo profilo o a un profilo esistente di Gestione traffico. È supportata solo la versione Azure Resource Manager di Gestione traffico.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Creazione di un nuovo profilo di Gestione traffico durante la clonazione di un'app
Scenario: si vuole clonare un'app in un'altra area, durante la configurazione di un Azure Resource Manager profilo di gestione traffico che include entrambe le app. Il comando seguente mostra la creazione di un clone dell'app di origine in una nuova app durante la configurazione di un nuovo profilo di Gestione traffico:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Aggiunta di una nuova app clonata a un profilo di Gestione traffico esistente
Scenario: si dispone già di un profilo di gestione traffico Azure Resource Manager e si desidera aggiungere entrambe le app come endpoint. A tale scopo, è prima necessario assemblare l'ID del profilo di Gestione traffico esistente. Sono necessari l'ID di sottoscrizione, il nome del gruppo di risorse e il nome del profilo di Gestione traffico esistente.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Dopo aver recuperato l'ID di Gestione traffico, il comando seguente mostra la creazione di un clone dell'app di origine in una nuova app durante l'aggiunta di entrambe le app a un profilo di Gestione traffico esistente:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Restrizioni attuali
Di seguito sono riportate le limitazioni note della funzionalità di clonazione delle app:

* Le impostazioni di ridimensionamento automatico non vengono clonate.
* Le impostazioni di pianificazione del backup non vengono clonate.
* Le impostazioni della rete virtuale non vengono clonate.
* Le informazioni sull'app non vengono configurate automaticamente nell'app di destinazione.
* Le impostazioni di Easy Auth non vengono clonate.
* L'estensione Kudu non viene clonata.
* Le regole TiP non vengono clonate.
* Il contenuto di database non viene clonato.
* Gli indirizzi IP in uscita cambiano in caso di clonazione in una diversa unità di scala.
* Non disponibile per le app Linux

### <a name="references"></a>Riferimenti
* [Clonazione di Servizio app](app-service-web-app-cloning.md)
* [Eseguire il backup di un'app in Servizio app di Azure](manage-backup.md)
* [Supporto di Azure Resource Manager per la versione di anteprima di Gestione traffico di Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introduzione all'ambiente del servizio app](environment/intro.md)
* [Uso di Azure PowerShell con Azure Resource Manager](../azure-resource-manager/manage-resources-powershell.md)

