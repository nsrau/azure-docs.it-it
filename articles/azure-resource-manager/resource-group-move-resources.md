---
title: Spostare le risorse di Azure in una nuova sottoscrizione o in un gruppo di risorse | Microsoft Docs
description: Usare Azure Resource Manager per spostare risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: a7771c2cee3f2820db014a4e5e8620442eea3dd1
ms.lasthandoff: 02/21/2017


---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi
Questo argomento illustra come spostare le risorse in una nuova sottoscrizione o in un nuovo gruppo di risorse nella stessa sottoscrizione. È possibile usare il portale, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST per spostare una risorsa. Le operazioni di spostamento descritte in questo argomento non richiedono assistenza da parte del supporto tecnico di Azure.

Durante lo spostamento di risorse, sia il gruppo di origine che il gruppo di destinazione sono bloccati durante l'operazione. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi di risorse fino al completamento dello spostamento. Questo blocco indica che non è possibile aggiungere, aggiornare o eliminare le risorse dei gruppi di risorse, ma non che le risorse sono bloccate. Se ad esempio si sposta un Server SQL con il relativo database in un nuovo gruppo di risorse, nelle applicazioni che usano il database non si verificano tempi di inattività, poiché rimane possibile leggere e scrivere nel database. 

Non è possibile modificare il percorso della risorsa. Lo spostamento di una risorsa comporta solo il suo spostamento in un nuovo gruppo di risorse. Il nuovo gruppo di risorse può avere un percorso diverso, ma ciò non modifica la posizione della risorsa.

> [!NOTE]
> In questo articolo viene descritto come spostare le risorse nell'offerta di un account di Azure esistente. Se si vuole che modificare l'offerta dell'account di Azure, ad esempio effettuando l'aggiornamento da pagamento in base al consumo a pagamento anticipato, pur continuando a lavorare con le risorse esistenti, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](../billing/billing-how-to-switch-azure-offer.md). 
> 
> 

## <a name="checklist-before-moving-resources"></a>Controllo prima di spostare le risorse
Prima di spostare una risorsa è necessario eseguire alcuni passi importanti. La verifica di queste condizioni consente di evitare errori.

1. Le sottoscrizioni di origine e di destinazione devono trovarsi all'interno dello stesso [tenant di Active Directory](../active-directory/active-directory-howto-tenant.md). Per verificare che entrambe le sottoscrizioni contengano lo stesso ID tenant, usare Azure PowerShell o l'interfaccia della riga di comando di Azure.

  Per Azure PowerShell usare:

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName "Example Subscription").TenantId
  ```

  Per l'interfaccia della riga di comando di Azure 2.0, usare:

  ```azurecli
  az account show --subscription "Example Subscription" --query tenantId
  ```

  Se gli ID tenant per le sottoscrizioni di origine e di destinazione non sono uguali, è possibile tentare di modificare la directory della sottoscrizione. Tuttavia, questa opzione è disponibile solo per gli amministratori del servizio sono registrati con un account Microsoft (non un account aziendale). Per tentare di modificare la directory, accedere al [portale classico](https://manage.windowsazure.com/), selezionare **Impostazioni**, quindi la sottoscrizione. Se l'icona **Modifica directory** è disponibile, selezionarla per modificare l'Active Directory associato. 

  ![modifica directory](./media/resource-group-move-resources/edit-directory.png) 

  Se questa icona non è disponibile, è necessario contattare il supporto per spostare le risorse in un nuovo tenant.

2. Il servizio deve abilitare lo spostamento di risorse. In questo argomento sono elencati i servizi che consentono di spostare risorse e quelli che invece non lo permettono.
3. Il provider di risorse della risorsa da spostare deve essere registrato nella sottoscrizione di destinazione, altrimenti un errore indica che la **sottoscrizione non è registrata per un tipo di risorsa**. Questo problema può verificarsi se si sposta una risorsa in una nuova sottoscrizione, ma la sottoscrizione non è mai stata usata con tale tipo di risorsa. Per informazioni su come controllare lo stato della registrazione e registrare i provider di risorse, vedere [Provider e tipi di risorse](resource-manager-supported-services.md#resource-providers-and-types).

## <a name="when-to-call-support"></a>Quando chiamare il supporto
È possibile spostare la maggior parte delle risorse tramite le operazioni self-service descritte in questo argomento. Usare le operazioni self-service per:

* Spostare le risorse di Resource Manager.
* Spostare le risorse classiche in base alle [limitazioni della distribuzione classica](#classic-deployment-limitations). 

Chiamare il supporto quando è necessario:

* Spostare le risorse in un nuovo account Azure (e tenant di Active Directory).
* Spostare le risorse classiche ma si verificano problemi relativi alle limitazioni.

## <a name="services-that-enable-move"></a>Servizi che abilitano lo spostamento
Di seguito sono elencati i servizi che attualmente abilitano lo spostamento in un gruppo di risorse e in una sottoscrizione nuovi:

* Gestione API
* App del servizio app (app Web): vedere [Limitazioni del servizio app](#app-service-limitations)
* Automazione
* Batch
* Bing Mappe
* RETE CDN
* Servizi cloud: vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Servizi cognitivi
* Servizio contenitore
* Content Moderator
* Data Catalog
* Data factory
* Analisi Data Lake
* Archivio Data Lake
* DNS
* DocumentDB
* Hub eventi
* Cluster HDInsight - vedere [Limitazioni di HDInsight](#hdinsight-limitations)
* Hub IoT
* Insieme di credenziali di chiave 
* Servizi di bilanciamento del carico
* App per la logica
* Machine Learning
* Servizi multimediali
* Mobile Engagement
* Hub di notifica
* Operational Insights
* Operations Management
* Power BI
* Cache Redis
* Utilità di pianificazione
* Search
* Gestione server
* Bus di servizio
* Service Fabric
* Archiviazione
* Archiviazione (classica): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Analisi dei flussi
* Server di database SQL: il database e il server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database.
* Gestione traffico
* Macchine virtuali: non è supportato lo spostamento in una nuova sottoscrizione se i relativi certificati sono archiviati in un insieme di credenziali delle chiavi
* Macchine virtuali (classiche): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Reti virtuali

> [!NOTE] 
> Attualmente non è possibile spostare una rete virtuale che contiene un Gateway VPN fino a quando il gateway non sia stato rimosso temporaneamente. Dopo la rimozione, la rete virtuale può essere spostata e il gateway può essere creato.
>
 
## <a name="services-that-do-not-enable-move"></a>Servizi che non abilitano lo spostamento
I servizi che attualmente non abilitano lo spostamento di una risorsa sono:

* Servizio ibrido per l'integrità di AD
* gateway applicazione
* Application Insights
* Servizi BizTalk
* Express Route
* DevTest Labs: lo spostamento al nuovo gruppo di risorse nella stessa sottoscrizione è abilitato, ma lo spostamento della sottoscrizione incrociato non è abilitato.
* Dynamics LCS
* Insieme di credenziali delle chiavi di Servizi di ripristino: non spostare anche le risorse di calcolo, rete e archiviazione associate con l'insieme di credenziali di Servizi di ripristino, vedere [Limitazioni dei servizi di ripristino](#recovery-services-limitations).
* Sicurezza
* Macchine virtuali con certificato archiviato in insieme di credenziali delle chiavi
* Set di scalabilità di macchine virtuali
* Reti virtuali (classiche): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Gateway VPN

## <a name="app-service-limitations"></a>Limitazioni del servizio app
Quando si usano le app del servizio app non è possibile spostare solo un piano di servizio app. Per spostare le app del servizio app, le opzioni disponibili sono:

* Spostare il piano di servizio app e tutte le altre risorse del servizio app del gruppo di risorse in un nuovo gruppo di risorse che non dispone di risorse del servizio app. In base a questo requisito è necessario spostare anche le risorse del servizio app non associate al piano di servizio app. 
* Spostare le app in un gruppo di risorse diverso, ma mantenere tutti i piani di servizio app nel gruppo di risorse originale.

Se il gruppo di risorse originale include anche una risorsa Application Insights non è possibile spostarla, perché attualmente Application Insights non abilita l'operazione di spostamento. Se si include la risorsa Application Insights quando si spostano le app del servizio app, l'intera operazione di spostamento non riesce. Tuttavia per il corretto funzionamento dell'app non è necessario che la risorsa Application Insights e il piano di servizio app risiedano nello stesso gruppo di risorse in cui si trova l'app stessa.

Se ad esempio il gruppo di risorse contiene:

* **web-a** associata a **plan-a** e **app-insights-a**
* **web-b** associata a **plan-b** e **app-insights-b**

Le opzioni possibili sono:

* Spostare **web-a**, **plan-a**, **web-b** e **plan-b**
* Spostare **web-a** e **web-b**
* Spostare **web-a**
* Spostare **web-b**

Con tutte le altre combinazioni si sposterebbe un tipo di risorsa non spostabile (Application Insights) o si lascerebbe dove si trova un tipo di risorsa che non può essere lasciato nella stessa posizione quando si sposta un piano di servizio app (qualsiasi tipo di risorsa del servizio app).

Se l'app web si trova in un gruppo di risorse diverso rispetto al piano di servizio app corrispondente ma si vuole spostare entrambi gli elementi in un nuovo gruppo di risorse, è necessario eseguire lo spostamento in due fasi. Ad esempio:

* **web-a** si trova in **web-group**
* **plan-a** si trova in **plan-group**
* Si vuole che **web-a** e **plan-a** risiedano in **combined-group**

Per ottenere questo risultato è necessario eseguire due operazioni di spostamento distinte nell'ordine che segue:

1. Spostare **web-a** in **plan-group**
2. Spostare **web-a** e **plan-a** in **combined-group**.

È possibile spostare un certificato del servizio app in un nuovo gruppo di risorse o una nuova sottoscrizione senza problemi. Se l'app Web include un certificato SSL acquistato esternamente e caricato nell'app, tuttavia, è necessario eliminare il certificato prima di spostare l'app Web. Ad esempio, è possibile eseguire i passaggi seguenti:

1. Eliminare il certificato caricato dall'App Web
2. Spostare l'App Web
3. Caricare il certificato nell'App Web

## <a name="recovery-services-limitations"></a>Limitazioni dei servizi di ripristino
Lo spostamento non è abilitato per le risorse di archiviazione, di rete o di calcolo usate per configurare il ripristino di emergenza con Azure Site Recovery. 

Ad esempio, si supponga di avere configurato la replica delle macchine locali su un account di archiviazione (Storage1) e di desiderare che il computer protetto venga avviato dopo il failover in Azure come macchina virtuale (VM1) collegata a una rete virtuale (Network1). Non è possibile spostare una di queste risorse di Azure - Storage1 VM1 e Network1 - nei gruppi di risorse all'interno della stessa sottoscrizione o tra le sottoscrizioni.

## <a name="hdinsight-limitations"></a>Limitazioni di HDInsight

È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.

Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

## <a name="classic-deployment-limitations"></a>Limitazioni della distribuzione classica
Le opzioni per lo spostamento delle risorse distribuite con il modello classico variano a seconda che lo spostamento avvenga all'interno di una sottoscrizione o a una nuova sottoscrizione. 

### <a name="same-subscription"></a>Stessa sottoscrizione
Quando si spostano risorse da un gruppo di risorse a un altro gruppo di risorse nella stessa sottoscrizione, sono valide le restrizioni seguenti:

* Le reti virtuali (classiche) non possono essere spostate.
* Le macchine virtuali (classiche) devono essere spostate con il servizio cloud. 
* Il servizio cloud può essere spostato solo quando lo spostamento include tutte le macchine virtuali del servizio stesso.
* È possibile spostare un solo servizio cloud alla volta.
* È possibile spostare un solo account di archiviazione (classico) alla volta.
* Un account di archiviazione (classico) non può essere spostato nella stessa operazione di spostamento che include una macchina virtuale o un servizio cloud.

Per spostare le risorse classiche in un nuovo gruppo di risorse all'interno della stessa sottoscrizione usare le operazioni di spostamento standard tramite il [portale](#use-portal), [Azure PowerShell](#use-powershell), l'[interfaccia della riga di comando di Azure](#use-azure-cli) o l'[API REST](#use-rest-api). Usare le stesse operazioni eseguite per lo spostamento di risorse di Resource Manager.

### <a name="new-subscription"></a>Nuova sottoscrizione
Quando si spostano risorse in una nuova sottoscrizione, sono valide le restrizioni seguenti:

* Tutte le risorse classiche della sottoscrizione vanno spostate nella stessa operazione.
* La sottoscrizione di destinazione non deve contenere nessuna delle altre risorse classiche.
* Lo spostamento può essere richiesto solo tramite un'API REST separata per gli spostamenti di risorse classiche. I comandi di spostamento standard di Resource Manager non funzionano quando si spostano risorse classiche a una nuova sottoscrizione.

Per spostare le risorse classiche a una nuova sottoscrizione, usare il portale o le operazioni REST specifiche di risorse classiche. Per informazioni sullo spostamento di risorse classiche tramite il portale, vedere [Usare il portale](#use-portal). Per usare REST, seguire questa procedura:

1. Controllare se la sottoscrizione di origine può partecipare a un'operazione di spostamento tra sottoscrizioni. Usare l'operazione seguente:

  ```HTTP   
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```
   
     Nel corpo della richiesta includere:

  ```json 
  {
    "role": "source"
  }
  ```
  
     La risposta per l'operazione di convalida ha il formato seguente:

  ```json 
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Controllare se la sottoscrizione di destinazione può partecipare a un'operazione di spostamento tra sottoscrizioni. Usare l'operazione seguente:

  ```HTTP 
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     Nel corpo della richiesta includere:

  ```json 
  {
    "role": "target"
  }
  ```
   
     La risposta ha lo stesso formato della convalida della sottoscrizione di origine.
3. Se entrambe le sottoscrizioni superano la convalida, spostare tutte le risorse classiche da una sottoscrizione a un'altra usando l'operazione seguente:

  ```HTTP 
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    Nel corpo della richiesta includere:

  ```json 
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

Questa operazione potrebbe richiedere alcuni minuti. 

## <a name="use-portal"></a>Usare il portale
Per spostare le risorse, selezionare il gruppo contenente queste risorse, quindi usare il pulsante **Sposta**.

![Spostare le risorse](./media/resource-group-move-resources/select-move.png)

Selezionare se si desidera spostare le risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione.

Selezionare le risorse da spostare e il gruppo di risorse di destinazione. Confermare di dover aggiornare gli script per queste risorse e selezionare **OK**. Se si seleziona l'icona del comando Modifica sottoscrizione nel passaggio precedente, è necessario anche selezionare la sottoscrizione di destinazione.

![Selezione della destinazione](./media/resource-group-move-resources/select-destination.png)

In **Notifiche**si nota che è in corso l'operazione di spostamento.

![Visualizzare lo stato dello spostamento](./media/resource-group-move-resources/show-status.png)

Al completamento dell'operazione si riceverà la notifica del risultato.

![Visualizzare il risultato dello spostamento](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Usare PowerShell
Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversi, usare il comando `Move-AzureRmResource`.

Nel primo esempio viene illustrato come spostare una risorsa in un nuovo gruppo di risorse.

```powershell
$resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId
```

Nel secondo esempio viene illustrato come spostare più risorse in un nuovo gruppo di risorse.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Per eseguire lo spostamento in una nuova sottoscrizione, includere un valore per il parametro `DestinationSubscriptionId`.

Viene richiesto di confermare che si vuole spostare la risorsa specificata.

```powershell
Confirm
Are you sure you want to move these resources to the resource group
'/subscriptions/{guid}/resourceGroups/newRG' the resources:

/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
```

## <a name="use-azure-cli-20"></a>Usare l'interfaccia della riga di comando 2.0 di Azure
Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversi, usare il comando `az resource move`. Fornire gli ID risorsa delle risorse da spostare. È possibile ottenere gli ID risorsa con il comando seguente:

```azurecli
az resource show -g sourceGroup -n storagedemo --resource-type "Microsoft.Storage/storageAccounts" --query id
```

L'esempio seguente illustra come spostare un account di archiviazione in un nuovo gruppo di risorse. Nel parametro `--ids` inserire un elenco delimitato da spazi di ID di risorse da spostare.

```azurecli
az resource move --destination-group newgroup --ids "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo"
```

Per spostare in una nuova sottoscrizione, inserire il parametro `--destination-subscription-id`.

## <a name="use-azure-cli-10"></a>Usare l'interfaccia della riga di comando di Azure 1.0
Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversi, usare il comando `azure resource move`. Fornire gli ID risorsa delle risorse da spostare. È possibile ottenere gli ID risorsa con il comando seguente:

```azurecli
azure resource list -g sourceGroup --json
```

Viene restituito il formato seguente:

```azurecli
[
  {
    "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
    "name": "storagedemo",
    "type": "Microsoft.Storage/storageAccounts",
    "location": "southcentralus",
    "tags": {},
    "kind": "Storage",
    "sku": {
      "name": "Standard_RAGRS",
      "tier": "Standard"
    }
  }
]
```

L'esempio seguente illustra come spostare un account di archiviazione in un nuovo gruppo di risorse. Nel parametro `-i` inserire un elenco delimitato da virgole di ID di risorse da spostare.

```azurecli
azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
```

Viene richiesto di confermare che si vuole spostare la risorsa specificata.

## <a name="use-rest-api"></a>Usare l'API REST
Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversi, eseguire:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 
```

Nel corpo della richiesta specificare il gruppo di risorse di destinazione e le risorse da spostare. Per altre informazioni sull'operazione REST di spostamento, vedere [Spostare le risorse](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sui cmdlet di PowerShell per la gestione della sottoscrizione, vedere [Uso di Azure PowerShell con Resource Manager](powershell-azure-resource-manager.md).
* Per informazioni sui comandi dell'interfaccia della riga di comando di Azure per la gestione della sottoscrizione, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* Per informazioni sulle funzionalità del portale per la gestione della sottoscrizione, vedere [Gestire le risorse di Azure mediante il portale](resource-group-portal.md).
* Per informazioni sull'organizzazione logica delle risorse, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).


