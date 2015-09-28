<properties 
	pageTitle="Spostare le risorse in un nuovo gruppo di risorse" 
	description="Usare Azure PowerShell o API REST per spostare le risorse a un nuovo gruppo di risorse per Gestione risorse di Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="tomfitz"/>

# Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi

In questo argomento viene illustrato come spostare le risorse da un gruppo di risorse a un altro. È inoltre possibile spostare le risorse in una nuova sottoscrizione. Potrebbe essere necessario spostare le risorse quando si decide che:

1. Ai fini della fatturazione, è necessario che una risorsa si trovi in una sottoscrizione diversa.
2. Una risorsa non condivide più lo stesso ciclo di vita stesso delle risorse con cui era stata precedentemente raggruppata. Si desidera spostare la risorsa in un nuovo gruppo di risorse in modo da poterla gestire separatamente rispetto alle altre risorse.
3. Una risorsa condivide lo stesso ciclo di vita di altre risorse in un gruppo di risorse diverso. Si desidera spostare la risorsa nel gruppo di risorse assieme ad altre risorse in modo da poterle gestire insieme.

Esistono alcune importanti considerazioni da tenere presente durante lo spostamento di una risorsa:

1. Non è possibile modificare il percorso della risorsa. Lo spostamento di una risorsa comporta solo il suo spostamento in un nuovo gruppo di risorse. Il nuovo gruppo di risorse può avere un percorso diverso, ma ciò non modifica la posizione della risorsa.
2. Il gruppo di risorse di destinazione deve contenere solo le risorse che condividono un ciclo di vita dell'applicazione uguale a quello delle risorse che si desidera spostare.
3. Se si utilizza Azure PowerShell, assicurarsi di usare la versione più recente. Il comando **Move-AzureResource** viene aggiornato di frequente. Per aggiornare la versione, eseguire l'Installazione guidata piattaforma Web Microsoft e verificare se è disponibile una nuova versione. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).
4. L'operazione di spostamento può richiedere alcuni minuti e durante tale intervallo il prompt di PowerShell attenderà il completamento dell'operazione.

## Servizi supportati

Attualmente non tutti i servizi supportano lo spostamento di risorse.

Di seguito sono elencati i servizi che supportano lo spostamento in un gruppo di risorse e in una sottoscrizione nuovi:

- Gestione API
- Azure DocumentDB
- Ricerca di Azure
- App Web di Azure (si applicano alcune [limitazioni](app-service-web/app-service-move-resources.md))
- Data Factory
- Insieme di credenziali di chiave
- Mobile Engagement
- Operational Insights
- Cache Redis
- Database SQL

I servizi che supportano lo spostamento di un nuovo gruppo di risorse, ma non una nuova sottoscrizione sono:

- Calcolo (classico)
- Archiviazione (classico)

I servizi che attualmente non supportano lo spostamento di una risorsa sono:

- Reti virtuali

Quando si usano le app Web non è possibile spostare solo un piano di servizio app. Per spostare le app Web, le opzioni disponibili sono:

- Spostare tutte le risorse da un gruppo di risorse a un gruppo di risorse diverso, se il gruppo di risorse di destinazione non contiene già risorse Microsoft.Web.
- Spostare le app Web in un gruppo di risorse diverso, ma conservare il piano di servizio app nel gruppo di risorse originale.

## Uso di PowerShell per spostare le risorse

Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversa, usare il comando **Move-AzureResource**.

Nel primo esempio viene illustrato come spostare una risorsa in un nuovo gruppo di risorse.

    PS C:\> Move-AzureResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

Nel secondo esempio viene illustrato come spostare più risorse in un nuovo gruppo di risorse.

    PS C:\> $webapp = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExampleSite -ResourceType Microsoft.Web/sites
    PS C:\> $plan = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExamplePlan -ResourceType Microsoft.Web/serverFarms
    PS C:\> Move-AzureResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

Per eseguire lo spostamento in una nuova sottoscrizione, includere un valore per il parametro **DestinationSubscriptionId**.

## Uso di API REST per spostare le risorse

Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversi, eseguire:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Sostituire **{source-subscription-id}** e **{source-resource-group-name}** con la sottoscrizione o il gruppo di risorse che attualmente contengono le risorse da spostare. Usare **2015-01-01** per {versione-api}.

Nella richiesta includere un oggetto JSON che definisce il gruppo di risorse di destinazione e le risorse che si desidera spostare.

    {
        "targetResourceGroup": "/subscriptions/{target-subscription-id}/resourceGroups/{target-resource-group-name}", "resources": [
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}"
        ]
    }

## Passaggi successivi
- [Utilizzo di Azure PowerShell con Gestione risorse](./powershell-azure-resource-manager.md)
- [Utilizzo dell'interfaccia della riga di comando di Azure con Gestione risorse](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Utilizzo del portale di Azure per gestire le risorse](azure-portal/resource-group-portal.md)
- [Utilizzo dei tag per organizzare le risorse](./resource-group-using-tags.md)

<!---HONumber=Sept15_HO3-->