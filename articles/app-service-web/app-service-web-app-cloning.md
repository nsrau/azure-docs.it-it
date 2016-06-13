<properties
	pageTitle="Clonazione di app Web con PowerShell"
	description="Come clonare le app Web in nuove app Web con PowerShell."
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="ahmedelnably"/>

# Clonazione di app del servizio app di Azure con PowerShell#

Con il rilascio di Microsoft Azure PowerShell versione 1.1.0 è stata aggiunta una nuova opzione a New-AzureRMWebApp che consente all'utente di clonare un'app Web esistente in un'app appena creata in un'area diversa o nella stessa area. In questo modo i clienti possono distribuire una quantità di app in aree geografiche diverse rapidamente e con facilità.

La clonazione di app è attualmente supportata solo per i piani di servizio app Premium. La nuova funzionalità usa le stesse limitazioni della funzionalità di backup di App Web. Vedere [Eseguire il backup di un'app Web nel servizio app di Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Per informazioni su come usare cmdlet di Azure PowerShell basati su Azure Resource Manager per gestire le app Web, vedere [Uso di comandi di PowerShell basati su Azure Resource Manager per la gestione di app Web di Azure](app-service-web-app-azure-resource-manager-powershell.md)

## Clonazione di un'app esistente ##

Scenario: l'utente vuole clonare il contenuto di un'app Web esistente nell'area South Central US in una nuova app Web nell'aera North Central US. Questa operazione può essere eseguita con la versione Azure Resource Manager del cmdlet di PowerShell per creare una nuova app Web con l'opzione -SourceWebApp.

Conoscendo il nome del gruppo di risorse che include l'app Web di origine, è possibile usare il comando di PowerShell seguente per ottenere informazioni sull'app Web di origine, denominata in questo caso source-webapp:

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Per creare un nuovo piano di servizio app, è possibile usare il comando New-AzureRmAppServicePlan come nell'esempio seguente:

	New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Con il comando New-AzureRmWebApp è possibile creare la nuova app Web nell'area North Central US e collegarla a un piano di servizio app Premium esistente. È anche possibile usare lo stesso gruppo di risorse dell'app Web di origine o definirne uno nuovo, come illustrato di seguito:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Per clonare un'app Web esistente, inclusi tutti gli slot di distribuzione associati, l'utente dovrà usare il parametro IncludeSourceWebAppSlots. Il comando di PowerShell seguente illustra l'uso del parametro con il comando New-AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Per clonare un'app Web esistente nella stessa area, l'utente dovrà creare un nuovo gruppo di risorse e un nuovo piano di servizio app nella stessa area e quindi usare il comando di PowerShell seguente per clonare l'app Web:

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## Clonazione di un'app esistente in un ambiente del servizio App ##

Scenario: l'utente vuole clonare il contenuto di un'app Web esistente nell'area South Central US in una nuova app Web in un ambiente del servizio app.

Conoscendo il nome del gruppo di risorse che include l'app Web di origine, è possibile usare il comando di PowerShell seguente per ottenere informazioni sull'app Web di origine, denominata in questo caso source-webapp:

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Conoscendo il nome dell'ambiente del servizio app e il nome del gruppo di risorse a cui appartiene l'ambiente del servizio app, l'utente può usare il comando New-AzureRmWebApp per creare la nuova app Web nell'ambiente del servizio app esistente, come illustrato di seguito:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

Il parametro Location è obbligatorio per motivi di compatibilità con le versioni precedenti, ma verrà ignorato se si crea un'app in un ambiente del servizio app.

## Clonazione dello slot di un'app esistente ##

Scenario: l'utente vuole clonare lo slot di un'app Web esistente in una nuova app Web o un nuovo slot dell'app Web. La nuova app Web può trovarsi nella stessa area dello slot dell'app Web originale o in un'area diversa.

Conoscendo il nome del gruppo di risorse che include l'app Web di origine, è possibile usare il comando di PowerShell seguente per ottenere informazioni sullo slot dell'app Web di origine, denominata in questo caso source-webappslot, collegata all'app Web source-webapp:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

Di seguito è illustrata la creazione di un clone dell'app Web di origine in una nuova app Web:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## Configurazione di Gestione traffico durante la clonazione di un'app ##

La creazione di app Web con più aree e la configurazione di Gestione traffico di Azure per indirizzare il traffico a tali app Web sono importanti per garantire la disponibilità elevata delle app dei clienti. Durante la clonazione di un'app Web esistente è possibile scegliere di connettere entrambe le app Web a un nuovo profilo di Gestione traffico oppure a uno esistente. Si noti che è supportata unicamente la versione Azure Resource Manager di Gestione traffico.

### Creazione di un nuovo profilo di Gestione traffico durante la clonazione di un'app ###

Scenario: l'utente vuole clonare un'app Web in un'altra area mentre configura un profilo di Gestione traffico di Azure Resource Manager che include entrambe le app Web. Di seguito è illustrata la creazione di un clone dell'app Web di origine in una nuova app Web durante la configurazione di un nuovo profilo di Gestione traffico:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### Aggiunta di una nuova app Web clonata a un profilo di Gestione traffico esistente ###

Scenario: l'utente ha già un profilo di Gestione traffico di Azure Resource Manager a cui vorrebbe aggiungere entrambe le app Web come endpoint. A questo scopo, è necessario assemblare prima di tutto l'ID del profilo di Gestione traffico esistente, per cui saranno necessari l'ID sottoscrizione, il nome del gruppo di risorse e il nome del profilo di Gestione traffico esistente.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Dopo aver recuperato l'ID di Gestione traffico, di seguito è illustrata la creazione di un clone dell'app Web di origine in una nuova app Web durante l'aggiunta a un profilo di Gestione traffico esistente:

	$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## Restrizioni attuali ##

Questa funzionalità è attualmente in anteprima e sono allo studio nuove funzionalità che saranno aggiunte con il tempo. L'elenco seguente include le restrizioni note della versione corrente relative alla clonazione di app:

- Le impostazioni di ridimensionamento automatico non vengono clonate.
- Le impostazioni di pianificazione del backup non vengono clonate.
- Le impostazioni della rete virtuale non vengono clonate.
- Le informazioni sull'app non vengono configurate automaticamente nell'app Web di destinazione.
- Le impostazioni di Easy Auth non vengono clonate.
- L'estensione Kudu non viene clonata.
- Le regole TiP non vengono clonate.
- Il contenuto di database non viene clonato.


### Riferimenti ###
- [Uso di comandi di PowerShell basati su Azure Resource Manager per la gestione di app Web di Azure](app-service-web-app-azure-resource-manager-powershell.md)
- [Clonazione di app Web con il portale di Azure](app-service-web-app-cloning-portal.md)
- [Eseguire il backup di un'app Web nel servizio app di Azure](web-sites-backup.md)
- [Supporto di Gestione risorse di Azure per la versione di anteprima di Gestione traffico di Azure](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md)
- [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0601_2016-->