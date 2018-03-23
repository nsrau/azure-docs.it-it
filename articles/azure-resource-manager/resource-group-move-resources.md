---
title: Spostare le risorse di Azure in una nuova sottoscrizione o in un gruppo di risorse | Microsoft Docs
description: Usare Azure Resource Manager per spostare risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: c4bf3871b4636ce769dd8d84a637b88d4a00db97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi

Questo articolo illustra come spostare le risorse in una nuova sottoscrizione o in un nuovo gruppo di risorse nella stessa sottoscrizione. È possibile usare il portale, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST per spostare una risorsa. Le operazioni di spostamento descritte in questo articolo non richiedono assistenza da parte del supporto tecnico di Azure.

Durante lo spostamento di risorse, sia il gruppo di origine che il gruppo di destinazione sono bloccati durante l'operazione. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi di risorse fino al completamento dello spostamento. Questo blocco indica che non è possibile aggiungere, aggiornare o eliminare le risorse dei gruppi di risorse, ma non che le risorse sono bloccate. Se ad esempio si sposta un Server SQL con il relativo database in un nuovo gruppo di risorse, nelle applicazioni che usano il database non si verificano tempi di inattività, poiché rimane possibile leggere e scrivere nel database.

Non è possibile modificare il percorso della risorsa. Lo spostamento di una risorsa comporta solo il suo spostamento in un nuovo gruppo di risorse. Il nuovo gruppo di risorse può avere un percorso diverso, ma ciò non modifica la posizione della risorsa.

> [!NOTE]
> In questo articolo viene descritto come spostare le risorse nell'offerta di un account di Azure esistente. Se si vuole che modificare l'offerta dell'account di Azure, ad esempio effettuando l'aggiornamento da pagamento in base al consumo a pagamento anticipato, pur continuando a lavorare con le risorse esistenti, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](../billing/billing-how-to-switch-azure-offer.md).
>
>

## <a name="checklist-before-moving-resources"></a>Controllo prima di spostare le risorse

Prima di spostare una risorsa è necessario eseguire alcuni passi importanti. La verifica di queste condizioni consente di evitare errori.

1. Le sottoscrizioni di origine e di destinazione devono trovarsi nello stesso [tenant di Azure Active Directory](../active-directory/active-directory-howto-tenant.md). Per verificare che entrambe le sottoscrizioni contengano lo stesso ID tenant, usare Azure PowerShell o l'interfaccia della riga di comando di Azure.

  Per Azure PowerShell usare:

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Per l'interfaccia della riga di comando di Azure usare:

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Se gli ID tenant per le sottoscrizioni di origine e di destinazione non sono uguali, usare i metodi descritti di seguito per risolvere le differenze degli ID tenant: 

  * [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md)
  * [Come associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md)

2. Il servizio deve abilitare lo spostamento di risorse. In questo articolo sono elencati i servizi che consentono di spostare risorse e quelli che invece non lo consentono.
3. Il provider di risorse della risorsa da spostare deve essere registrato nella sottoscrizione di destinazione, altrimenti un errore indica che la **sottoscrizione non è registrata per un tipo di risorsa**. Questo problema può verificarsi se si sposta una risorsa in una nuova sottoscrizione, ma la sottoscrizione non è mai stata usata con tale tipo di risorsa.

  In PowerShell, per ottenere lo stato della registrazione usare i comandi seguenti:

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Per registrare un provider di risorse, usare:

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Nell'interfaccia della riga di comando di Azure, per ottenere lo stato della registrazione usare i comandi seguenti:

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Per registrare un provider di risorse, usare:

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

## <a name="when-to-call-support"></a>Quando chiamare il supporto

È possibile spostare la maggior parte delle risorse tramite le operazioni self-service descritte in questo articolo. Usare le operazioni self-service per:

* Spostare le risorse di Resource Manager.
* Spostare le risorse classiche in base alle [limitazioni della distribuzione classica](#classic-deployment-limitations).

Contattare il [supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se è necessario:

* Spostare le risorse in un nuovo account di Azure (e tenant di Azure Active Directory) e serve assistenza con le istruzioni nella sezione precedente.
* Spostare le risorse classiche ma si verificano problemi relativi alle limitazioni.

## <a name="services-that-can-be-moved"></a>Servizi che possono essere spostati

Di seguito sono elencati i servizi che abilitano lo spostamento in un nuovo gruppo di risorse e in una nuova sottoscrizione:

* Gestione API
* App del servizio app (app Web): vedere [Limitazioni del servizio app](#app-service-limitations)
* Application Insights
* Automazione
* Azure Cosmos DB
* Batch
* Bing Mappe
* RETE CDN
* Servizi cloud: vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Servizi cognitivi
* Content Moderator
* Data Catalog
* Data factory
* Data Lake Analytics
* Archivio Data Lake
* DNS
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
* Ricerca
* Gestione server
* Bus di servizio
* Service Fabric
* Archiviazione
* Archiviazione (classica): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Analisi di flusso: i processi di analisi di flusso non possono essere spostati durante l'esecuzione.
* Server di database SQL: il database e il server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database.
* servizio Gestione traffico
* Macchine virtuali: non è possibile spostare macchine virtuali con dischi gestiti. Vedere [Limitazioni delle macchine virtuali](#virtual-machines-limitations)
* Macchine virtuali (classiche): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Set di scalabilità di macchine virtuali: vedere [Limitazioni delle macchine virtuali](#virtual-machines-limitations)
* Reti virtuali, vedere [Limitazioni delle reti virtuali](#virtual-networks-limitations)
* Gateway VPN

## <a name="services-that-cannot-be-moved"></a>Servizi che non possono essere spostati

I servizi che attualmente non abilitano lo spostamento di una risorsa sono:

* AD Domain Services
* Servizio ibrido per l'integrità di AD
* gateway applicazione
* Servizi BizTalk
* Servizio contenitore
* Express Route
* DevTest Labs: lo spostamento in un nuovo gruppo di risorse nella stessa sottoscrizione è abilitato, ma lo spostamento tra sottoscrizioni diverse non lo è.
* Dynamics LCS
* Applicazioni gestite
* Dischi gestiti: vedere [Limitazioni delle macchine virtuali](#virtual-machines-limitations)
* Insieme di credenziali delle chiavi di Servizi di ripristino: non spostare anche le risorse di calcolo, rete e archiviazione associate con l'insieme di credenziali di Servizi di ripristino, vedere [Limitazioni dei servizi di ripristino](#recovery-services-limitations).
* Sicurezza
* Gestione dispositivi StorSimple
* Reti virtuali (classiche): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Limitazioni delle macchine virtuali

I dischi gestiti non supportano lo spostamento. A causa di questa restrizione, non è possibile spostare le risorse correlate seguenti:

* Dischi gestiti
* Macchine virtuali con dischi gestiti
* Immagini create da dischi gestiti
* Snapshot creati da dischi gestiti
* Set di disponibilità con macchine virtuali con dischi gestiti

Non è possibile spostare da un gruppo di risorse o una sottoscrizione a un'altra macchine virtuali create a partire da risorse Marketplace con piani assegnati. Sottoporre a deprovisioning le macchine virtuali nella sottoscrizione in cui si trovano e distribuirle di nuovo nella nuova sottoscrizione.

Le macchine virtuali con certificato archiviato in Key Vault possono essere spostate in un nuovo gruppo di risorse nella stessa sottoscrizione, ma non da una sottoscrizione a un'altra.

## <a name="virtual-networks-limitations"></a>Limitazioni delle reti virtuali

Per spostare una rete virtuale con peering, è prima necessario disabilitare il peering. Dopo la disabilitazione del peering è possibile spostare la rete virtuale. Riabilitare il peering della rete virtuale dopo lo spostamento.

Non è possibile spostare una rete virtuale in un'altra sottoscrizione se la rete virtuale contiene una subnet con collegamenti di navigazione delle risorse. Se ad esempio una risorsa Cache Redis è distribuita in una subnet, tale subnet ha un collegamento di navigazione della risorsa.

## <a name="app-service-limitations"></a>Limitazioni del servizio app

Le limitazioni per lo spostamento delle risorse del Servizio app variano a seconda che lo spostamento avvenga all'interno di una sottoscrizione o a una nuova sottoscrizione.

### <a name="moving-within-the-same-subscription"></a>Spostamento all'interno della stessa sottoscrizione

Quando si sposta un'app Web _nella stessa sottoscrizione_, non è possibile spostare i certificati SSL caricati. È comunque possibile spostare un'app Web nel nuovo gruppo di risorse senza spostare il relativo certificato SSL caricato mantenendo effettiva la funzionalità SSL dell'app. 

Se si desidera spostare il certificato SSL con l'app Web, attenersi alla procedura seguente:

1.  Eliminare il certificato caricato dall'app Web.
2.  Spostare l'app Web.
3.  Caricare il certificato nell'app Web spostata.

### <a name="moving-across-subscriptions"></a>Spostamento tra sottoscrizioni

Quando si sposta un'app Web _tra sottoscrizioni_, si applicano le limitazioni seguenti:

- Il gruppo di risorse di destinazione non deve contenere risorse del servizio app esistenti. Le risorse del servizio app includono:
    - App Web
    - Piani di servizio app
    - Certificati SSL importati o caricati
    - Ambienti del servizio app
- Tutte le risorse del servizio app nel gruppo di risorse devono essere spostate insieme.
- Le risorse del servizio app possono essere spostate solo dal gruppo di risorse in cui sono state originariamente create. Se una risorsa del servizio app non si trova più nel gruppo di risorse originale, deve essere spostata nuovamente in tale gruppo prima di poter essere spostata tra le sottoscrizioni. 

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

Per spostare le risorse classiche in una nuova sottoscrizione, usare le operazioni REST specifiche per le risorse classiche. Per usare REST, seguire questa procedura:

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

## <a name="recovery-services-limitations"></a>Limitazioni dei servizi di ripristino

Lo spostamento non è abilitato per le risorse di archiviazione, di rete o di calcolo usate per configurare il ripristino di emergenza con Azure Site Recovery.

Ad esempio, si supponga di avere configurato la replica delle macchine locali su un account di archiviazione (Storage1) e di desiderare che il computer protetto venga avviato dopo il failover in Azure come macchina virtuale (VM1) collegata a una rete virtuale (Network1). Non è possibile spostare una di queste risorse di Azure - Storage1 VM1 e Network1 - nei gruppi di risorse all'interno della stessa sottoscrizione o tra le sottoscrizioni.

Per spostare una VM registrata in **Backup di Azure** tra gruppi di risorse:
 1. Interrompere temporaneamente il backup e conservare i dati di backup
 2. Spostare la VM nel gruppo di risorse di destinazione
 3. Riproteggerla con lo stesso o con un nuovo insieme di credenziali Gli utenti possono eseguire il ripristino dai punti di ripristino disponibili creati prima dell'operazione di spostamento.
Se l'utente sposta la macchina virtuale sottoposta a backup tra sottoscrizioni, i passaggi 1 e 2 restano invariati. Nel passaggio 3, è necessario proteggere la macchina virtuale in un nuovo insieme di credenziali presente o creato nella sottoscrizione di destinazione. L'insieme di credenziali di Servizi di ripristino non supporta i backup tra più sottoscrizioni.

## <a name="hdinsight-limitations"></a>Limitazioni di HDInsight

È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.

Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

## <a name="search-limitations"></a>Limitazioni del servizio di ricerca

Non è possibile spostare contemporaneamente più risorse del servizio di ricerca che si trovano nella stessa area.
In tal caso, è necessario spostarle separatamente.

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

Per spostare le risorse esistenti in un gruppo di risorse o in una sottoscrizione diversa, usare il comando [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) . L'esempio seguente illustra come spostare più risorse in un nuovo gruppo di risorse.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Per eseguire lo spostamento in una nuova sottoscrizione, includere un valore per il parametro `DestinationSubscriptionId`.

## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Per spostare risorse esistenti in un altro gruppo di risorse o un'altra sottoscrizione, usare il comando [az resource move](/cli/azure/resource?view=azure-cli-latest#az_resource_move). Fornire gli ID risorsa delle risorse da spostare. L'esempio seguente illustra come spostare più risorse in un nuovo gruppo di risorse. Nel parametro `--ids` inserire un elenco delimitato da spazi di ID di risorse da spostare.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Per spostare in una nuova sottoscrizione, inserire il parametro `--destination-subscription-id`.

## <a name="use-rest-api"></a>Usare l'API REST

Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversi, eseguire:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

Nel corpo della richiesta specificare il gruppo di risorse di destinazione e le risorse da spostare. Per altre informazioni sull'operazione REST di spostamento, vedere [Spostare le risorse](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui cmdlet di PowerShell per la gestione della sottoscrizione, vedere [Uso di Azure PowerShell con Resource Manager](powershell-azure-resource-manager.md).
* Per informazioni sui comandi dell'interfaccia della riga di comando di Azure per la gestione della sottoscrizione, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* Per informazioni sulle funzionalità del portale per la gestione della sottoscrizione, vedere [Gestire le risorse di Azure mediante il portale](resource-group-portal.md).
* Per informazioni sull'organizzazione logica delle risorse, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).
