<properties 
	pageTitle="Spostare le risorse in un nuovo gruppo di risorse" 
	description="Usare Azure PowerShell o API REST per spostare le risorse a un nuovo gruppo di risorse per Gestione risorse di Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="tomfitz"/>

# Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi

In questo argomento viene illustrato come spostare le risorse da un gruppo di risorse a un altro. È anche possibile spostare le risorse in una nuova sottoscrizione che però deve essere presente all'interno dello stesso [tenant](./active-directory/active-directory-howto-tenant.md). Potrebbe essere necessario spostare le risorse quando si decide che:

1. Ai fini della fatturazione, è necessario che una risorsa si trovi in una sottoscrizione diversa.
2. Una risorsa non condivide più lo stesso ciclo di vita stesso delle risorse con cui era stata precedentemente raggruppata. Si desidera spostare la risorsa in un nuovo gruppo di risorse in modo da poterla gestire separatamente rispetto alle altre risorse.
3. Una risorsa condivide lo stesso ciclo di vita di altre risorse in un gruppo di risorse diverso. Si desidera spostare la risorsa nel gruppo di risorse assieme ad altre risorse in modo da poterle gestire insieme.

Durante lo spostamento di risorse, sia il gruppo di origine che il gruppo di destinazione sono bloccati per la durata dell'operazione. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi fino al completamento dello spostamento.

Non è possibile modificare il percorso della risorsa. Lo spostamento di una risorsa comporta solo il suo spostamento in un nuovo gruppo di risorse. Il nuovo gruppo di risorse può avere un percorso diverso, ma ciò non modifica la posizione della risorsa.

## Controllo prima di spostare le risorse

Prima di spostare una risorsa è necessario eseguire alcuni passi importanti. Verificando queste condizioni è possibile evitare errori.

1. Il servizio deve supportare lo spostamento di risorse. Vedere l'elenco seguente per informazioni sui [servizi che supportano lo spostamento di risorse](#services-that-support-move).
2. Il provider di risorse della risorsa da spostare deve essere registrato nella sottoscrizione di destinazione, altrimenti verrà visualizzato un errore che indica che la **sottoscrizione non è registrata per un tipo di risorsa**. Questo problema può verificarsi se si sposta una risorsa in una nuova sottoscrizione, ma la sottoscrizione non è mai stata usata con tale tipo di risorsa. Per informazioni su come controllare lo stato della registrazione e registrare i provider di risorse, vedere la pagina relativa a [provider e tipi di risorse](../resource-manager-supported-services/#resource-providers-and-types).
3. Se si usa Azure PowerShell o l'interfaccia della riga di comando di Azure, assicurarsi di usare la versione più recente. Per aggiornare la versione, eseguire l'Installazione guidata piattaforma Web Microsoft e verificare se è disponibile una nuova versione. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e [Installare il CLI di Azure](xplat-cli-install.md).
4. Se si sta spostando un'app del Servizio app, è stata rivista la sezione [Limitazioni del servizio app](#app-service-limitations).

## Servizi che supportano lo spostamento

Di seguito sono elencati i servizi che supportano lo spostamento in un gruppo di risorse e in una sottoscrizione nuovi:

- Gestione API
- App del servizio app (vedere le [limitazioni del servizio app](#app-service-limitations) più avanti)
- Automazione
- Batch
- RETE CDN
- Data factory
- DocumentDB
- Cluster HDInsight
- Insieme di credenziali di chiave
- Mobile Engagement
- Hub di notifica
- Operational Insights
- Cache Redis
- Search
- Server del database SQL (vedere le [limitazioni del database SQL](#sql-database-limitations) più avanti)

## Servizi che supportano parzialmente lo spostamento

I servizi che supportano lo spostamento di un nuovo gruppo di risorse, ma non una nuova sottoscrizione sono:

- Macchine virtuali (classico)
- Archiviazione (classico)
- Reti virtuali
- Microsoft Azure

## Servizi che non supportano lo spostamento

I servizi che attualmente non supportano lo spostamento di una risorsa sono:

- Macchine virtuali
- Archiviazione
- Express Route

## Limitazioni del servizio app

Quando si usano le app del servizio app non è possibile spostare solo un piano di servizio app. Per spostare le app del servizio app, le opzioni disponibili sono:

- Spostare tutte le risorse da un gruppo di risorse a un gruppo di risorse diverso, se il gruppo di risorse di destinazione non contiene già risorse Microsoft.Web.
- Spostare le app Web in un gruppo di risorse diverso, ma conservare il piano di servizio app nel gruppo di risorse originale.

## Limitazioni del database SQL

Non è possibile spostare un database SQL separatamente dal server in cui è eseguito, poiché il database e il server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database.

## Uso di PowerShell per spostare le risorse

Per spostare le risorse esistenti in un gruppo di risorse o in una sottoscrizione diversa, usare il comando **Move-AzureRmResource**.

Nel primo esempio viene illustrato come spostare una risorsa in un nuovo gruppo di risorse.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

Nel secondo esempio viene illustrato come spostare più risorse in un nuovo gruppo di risorse.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Per eseguire lo spostamento in una nuova sottoscrizione, includere un valore per il parametro **DestinationSubscriptionId**.

## Utilizzo l’interfaccia della riga di comando di Azure per spostare le risorse

Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversa, usare il comando **azure resource move**. Il seguente esempio illustra come spostare una cache Redis in un nuovo gruppo di risorse. Nel parametro **-i**, fornire un elenco delimitato da virgole di id di risorsa da spostare.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Uso di API REST per spostare le risorse

Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversi, eseguire:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Nel corpo della richiesta specificare il gruppo di risorse di destinazione e le risorse da spostare. Per altre informazioni sull'operazione REST di spostamento, vedere [Spostare le risorse](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## Uso del portale per spostare le risorse

È possibile spostare alcune risorse tramite il portale, tuttavia non tutti i provider di risorse che supportano l'operazione di spostamento forniscono tale funzionalità tramite il portale.

Per spostare una risorsa, selezionare la risorsa e quindi il pulsante **Sposta**.

![Spostamento della risorsa](./media/resource-group-move-resources/move-resources.png)

Specificare dove si vuole spostare la risorsa. Se insieme a tale risorsa devono essere spostate altre risorse, queste verranno elencate.

![Selezione della destinazione](./media/resource-group-move-resources/select-destination.png)

## Passaggi successivi
- Per altre informazioni sui cmdlet PowerShell per la gestione della sottoscrizione, vedere [Uso di Azure PowerShell con Resource Manager](powershell-azure-resource-manager.md).
- Per altre informazioni sulla interfaccia della riga di comando di Azure per la gestione della sottoscrizione, vedere la pagina relativa all'[uso dell'interfaccia della riga di comando di Azure con Resource Manager](xplat-cli-azure-resource-manager.md).
- Per informazioni sulle funzionalità del portale per la gestione della sottoscrizione, vedere la pagina relativa all'[uso del portale di Azure per gestire le risorse](./azure-portal/resource-group-portal.md).
- Per informazioni sull'organizzazione logica delle risorse, vedere [Uso dei tag per organizzare le risorse](resource-group-using-tags.md).

<!---HONumber=AcomDC_0420_2016-->