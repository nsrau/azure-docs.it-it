<properties 
	pageTitle="Spostare risorse a un nuovo gruppo di risorse | Microsoft Azure" 
	description="Usare Azure Resource Manager per spostare risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione." 
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
	ms.date="05/12/2016" 
	ms.author="tomfitz"/>

# Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi

In questo argomento viene illustrato come spostare le risorse da un gruppo di risorse a un altro. È anche possibile spostare le risorse in una nuova sottoscrizione che però deve essere presente all'interno dello stesso [tenant](./active-directory/active-directory-howto-tenant.md). Potrebbe essere necessario spostare le risorse quando si decide che:

1. Ai fini della fatturazione, è necessario che una risorsa si trovi in una sottoscrizione diversa.
2. Una risorsa non condivide più lo stesso ciclo di vita stesso delle risorse con cui era stata precedentemente raggruppata. Si desidera spostare la risorsa in un nuovo gruppo di risorse in modo da poterla gestire separatamente rispetto alle altre risorse.

Durante lo spostamento di risorse, sia il gruppo di origine che il gruppo di destinazione sono bloccati per la durata dell'operazione. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi fino al completamento dello spostamento.

Non è possibile modificare il percorso della risorsa. Lo spostamento di una risorsa comporta solo il suo spostamento in un nuovo gruppo di risorse. Il nuovo gruppo di risorse può avere un percorso diverso, ma ciò non modifica la posizione della risorsa.

## Controllo prima di spostare le risorse

Prima di spostare una risorsa è necessario eseguire alcuni passi importanti. La verifica di queste condizioni consente di evitare errori.

1. Il servizio deve supportare lo spostamento di risorse. Vedere l'elenco seguente per informazioni sui [servizi che supportano lo spostamento di risorse](#services-that-support-move).
2. Il provider di risorse della risorsa da spostare deve essere registrato nella sottoscrizione di destinazione, altrimenti un errore indicherà che **la sottoscrizione non è registrata per un tipo di risorsa**. Questo problema può verificarsi se si sposta una risorsa in una nuova sottoscrizione, ma la sottoscrizione non è mai stata usata con tale tipo di risorsa. Per informazioni su come controllare lo stato della registrazione e registrare i provider di risorse, vedere [Provider e tipi di risorse](../resource-manager-supported-services/#resource-providers-and-types).
3. Se si usa Azure PowerShell o l'interfaccia della riga di comando di Azure, assicurarsi di usare la versione più recente. Per aggiornare la versione, eseguire l'Installazione guidata piattaforma Web Microsoft e verificare se è disponibile una nuova versione. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e [Installare il CLI di Azure](xplat-cli-install.md).
4. Prima di spostare un'app del Servizio app è consigliabile leggere [Limitazioni del servizio app](#app-service-limitations).
5. Prima di spostare risorse distribuite con il modello classico è consigliabile leggere [Limitazioni della distribuzione classica](#classic-deployment-limitations).

## Servizi che supportano lo spostamento

Di seguito sono elencati i servizi che supportano lo spostamento in un gruppo di risorse e in una sottoscrizione nuovi:

- Gestione API
- App del servizio app: vedere [Limitazioni del servizio app](#app-service-limitations).
- Automazione
- Batch
- RETE CDN
- Servizi cloud: vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations).
- Data factory
- DocumentDB
- Cluster HDInsight
- Insieme di credenziali di chiave
- Mobile Engagement
- Hub di notifica
- Operational Insights
- Cache Redis
- Utilità di pianificazione
- Search
- Archiviazione (classica): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations).
- Server di database SQL: il database e il server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database.
- Macchine virtuali (classiche): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations).

## Servizi che supportano parzialmente lo spostamento

I servizi che supportano lo spostamento di un nuovo gruppo di risorse, ma non una nuova sottoscrizione sono:

- Reti virtuali

## Servizi che non supportano lo spostamento

I servizi che attualmente non supportano lo spostamento di una risorsa sono:

- Application Insights
- Express Route
- Archiviazione
- Macchine virtuali
- Reti virtuali (classiche): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations).

## Limitazioni del servizio app

Quando si usano le app del servizio app non è possibile spostare solo un piano di servizio app. Per spostare le app del servizio app, le opzioni disponibili sono:

- Spostare il piano di servizio app e tutte le altre risorse del servizio app del gruppo di risorse in un nuovo gruppo di risorse che non dispone di risorse del servizio app. È necessario spostare anche le risorse del servizio app non associate al piano di servizio app. 
- Spostare le app in un gruppo di risorse diverso, ma mantenere tutti i piani di servizio app nel gruppo di risorse originale.

Se il gruppo di risorse originale include anche una risorsa Application Insights non è possibile spostare tale risorsa, perché attualmente Application Insights non supporta l'operazione di spostamento. Se si include la risorsa Application Insights quando si spostano le app del servizio app, l'intera operazione di spostamento non riuscirà. Tuttavia per il corretto funzionamento dell'app non è necessario che la risorsa Application Insights e il piano di servizio app risiedano nello stesso gruppo di risorse in cui si trova l'app stessa.

Se ad esempio il gruppo di risorse contiene:

- **web-a** associata con **plan-a** e **app-insights-a**
- **web-b** associata con **plan-b** e **app-insights-b**

Le opzioni possibili sono:

- Spostare **web-a**, **plan-a**, **web-b** e **plan-b**
- Spostare **web-a** e **web-b**
- Spostare **web-a**
- Spostare **web-b**

Con tutte le altre combinazioni si sposterebbe un tipo di risorsa non spostabile (Application Insights) o si lascerebbe dove si trova un tipo di risorsa che non può essere lasciato nella stessa posizione quando si sposta un piano di servizio app (qualsiasi tipo di risorsa del servizio app).

Se l'app web si trova in un gruppo di risorse diverso rispetto al piano di servizio app corrispondente ma si vuole spostare entrambi gli elementi in un nuovo gruppo di risorse, è necessario eseguire lo spostamento in due fasi. ad esempio:

- **web-a** si trova in **web-group**
- **plan-a** si trova in **plan-group**
- Si vuole che **web-a** e **plan-a** risiedano in **combined-group**

Per ottenere questo risultato è necessario eseguire due operazioni di spostamento distinte nell'ordine che segue:

1. Spostare **web-a** a **plan-group**
2. Spostare **web-a** e **plan-a** a **combined-group**.

## Limitazioni della distribuzione classica

Le opzioni per lo spostamento delle risorse distribuite con il modello classico variano a seconda che lo spostamento avvenga all'interno di una sottoscrizione o a una nuova sottoscrizione.

Quando si spostano risorse da un gruppo di risorse a un altro **nella stessa sottoscrizione** sono valide le restrizioni seguenti:

- Le reti virtuali (classiche) non possono essere spostate.
- Le macchine virtuali (classiche) devono essere spostate con il servizio cloud. 
- Il servizio cloud può essere spostato solo quando lo spostamento include tutte le macchine virtuali del servizio stesso.
- È possibile spostare un solo servizio cloud alla volta.
- È possibile spostare un solo account di archiviazione (classico) alla volta.
- Un account di archiviazione (classico) non può essere spostato nella stessa operazione di spostamento che include una macchina virtuale o un servizio cloud.

Quando si spostano risorse a una **nuova sottoscrizione** sono valide le restrizioni seguenti:

- Tutte le risorse classiche della sottoscrizione vanno spostate nella stessa operazione.
- Lo spostamento può essere richiesto solo tramite il portale o tramite un'API REST separata per gli spostamenti di risorse classiche. I comandi di spostamento standard di Resource Manager non funzionano quando si spostano risorse classiche a una nuova sottoscrizione. Le sezioni seguenti visualizzano le procedure per usare il portale o l'API REST.

## Uso del portale per spostare le risorse

È possibile spostare alcune risorse tramite il portale, tuttavia non tutti i provider di risorse che supportano l'operazione di spostamento forniscono tale funzionalità tramite il portale.

Per spostare una risorsa, selezionare la risorsa e quindi il pulsante **Sposta**.

![Spostamento della risorsa](./media/resource-group-move-resources/move-resources.png)

Specificare la sottoscrizione e il gruppo di risorse di destinazione per lo spostamento della risorsa. Se insieme a tale risorsa devono essere spostate altre risorse, queste verranno elencate.

![Selezione della destinazione](./media/resource-group-move-resources/select-destination.png)

In **Notifiche** si noterà che è in corso l'operazione di spostamento.

![Visualizzare lo stato dello spostamento](./media/resource-group-move-resources/show-status.png)

Al completamento dell'operazione si riceverà la notifica del risultato.

![Visualizzare il risultato dello spostamento](./media/resource-group-move-resources/show-result.png)

Per un'altra opzione di spostamento di risorse a un nuovo gruppo di risorse (non a una nuova sottoscrizione), selezionare la risorsa da spostare.

![Selezionare le risorse da spostare](./media/resource-group-move-resources/select-resource.png)

Selezionare **Proprietà** per la risorsa.

![Selezionare le proprietà](./media/resource-group-move-resources/select-properties.png)

Se disponibile per questo tipo di risorsa, selezionare **Cambia il gruppo di risorse**.

![Cambiare il gruppo di risorse](./media/resource-group-move-resources/change-resource-group.png)

È possibile selezionare le risorse da spostare e il gruppo di risorse al quale spostarle.

![Spostare le risorse](./media/resource-group-move-resources/select-group.png)

Per spostare in un nuovo gruppo risorse distribuite con il modello classico è possibile usare l'icona di modifica accanto al nome del gruppo di risorse.

![Spostare le risorse classiche](./media/resource-group-move-resources/edit-rg-icon.png)

Selezionare le risorse da spostare tenendo presenti le [limitazioni della distribuzione classica](#classic-deployment-limitations). Selezionare **OK** per avviare lo spostamento.

 ![Selezionare le risorse classiche](./media/resource-group-move-resources/select-classic-resources.png)
 
 Per spostare in una nuova sottoscrizione risorse distribuite con il modello classico è possibile usare l'icona di modifica accanto al nome della sottoscrizione.
 
 ![Spostare in una nuova sottoscrizione](./media/resource-group-move-resources/edit-subscription-icon.png)
 
 Tutte le risorse classiche vengono selezionate automaticamente per lo spostamento.

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

Verrà richiesto di confermare che si vuole spostare le risorse specificate.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## Utilizzo l’interfaccia della riga di comando di Azure per spostare le risorse

Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversa, usare il comando **azure resource move**. Il seguente esempio illustra come spostare una cache Redis in un nuovo gruppo di risorse. Nel parametro **-i**, fornire un elenco delimitato da virgole di id di risorsa da spostare.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
	
Verrà richiesto di confermare che si vuole spostare la risorsa specificata.
	
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Uso di API REST per spostare le risorse

Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversi, eseguire:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Nel corpo della richiesta specificare il gruppo di risorse di destinazione e le risorse da spostare. Per altre informazioni sull'operazione REST di spostamento, vedere [Spostare le risorse](https://msdn.microsoft.com/library/azure/mt218710.aspx).

Per spostare **risorse classiche in una nuova sottoscrizione** è tuttavia necessario usare altre operazioni REST. Per verificare se una sottoscrizione può partecipare come sottoscrizione di origine o di destinazione a uno spostamento di risorse classiche tra sottoscrizioni, usare la seguente operazione:

    POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
Per la sottoscrizione di origine, usare questo corpo della richiesta:

    {
        "role": "source"
    }

Per la sottoscrizione di destinazione, usare questo corpo della richiesta:

    {
        "role": "target"
    }

La risposta per entrambe le operazioni di convalida è:

    {
        "status": "{status}",
        "reasons": [
            "reason1",
            "reason2"
        ]
    }

Per spostare tutte le risorse classiche da una sottoscrizione a un'altra usare la seguente operazione:

    POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

con il seguente corpo della richiesta:

    {
        "target": "/subscriptions/{target-subscription-id}"
    }



## Passaggi successivi
- Per informazioni sui cmdlet PowerShell per la gestione della sottoscrizione vedere [Uso di Azure PowerShell con Resource Manager](powershell-azure-resource-manager.md).
- Per informazioni sull'interfaccia della riga di comando di Azure per la gestione della sottoscrizione vedere [Uso dell'interfaccia della riga di comando di Azure con Resource Manager](xplat-cli-azure-resource-manager.md).
- Per informazioni sulle funzionalità del portale per la gestione della sottoscrizione vedere [Uso del portale di Azure per gestire le risorse](./azure-portal/resource-group-portal.md).
- Per informazioni sull'organizzazione logica delle risorse vedere [Uso dei tag per organizzare le risorse](resource-group-using-tags.md).

<!---HONumber=AcomDC_0518_2016-->