---
title: Spostare le risorse di Azure in una nuova sottoscrizione o in un gruppo di risorse | Microsoft Docs
description: Usare Azure Resource Manager per spostare risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: tomfitz
ms.openlocfilehash: f4d63d4ad0841244cf2548b0842eea880e27a152
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463032"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi

Questo articolo illustra come spostare le risorse di Azure in un'altra sottoscrizione o in un altro gruppo di risorse all'interno della stessa sottoscrizione. Per spostare le risorse, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per eseguire un'esercitazione, vedere [Esercitazione: Spostare le risorse di Azure in un altro gruppo di risorse o sottoscrizione](./resource-manager-tutorial-move-resources.md).

Durante l'operazione di spostamento il gruppo di origine e quello di destinazione sono bloccati. Le operazioni di scrittura ed eliminazione sono bloccate nei gruppi di risorse fino al completamento dello spostamento. Questo blocco indica che non è possibile aggiungere, aggiornare o eliminare le risorse dei gruppi di risorse, ma non che le risorse sono bloccate. Se ad esempio si sposta un Server SQL con il relativo database in un nuovo gruppo di risorse, nelle applicazioni che usano il database non si verificano tempi di inattività, poiché rimane possibile leggere e scrivere nel database.

Lo spostamento di una risorsa comporta solo il suo spostamento in un nuovo gruppo di risorse. L'operazione di spostamento non consente di modificare la località della risorsa. Il nuovo gruppo di risorse può avere un percorso diverso, ma ciò non modifica la posizione della risorsa.

> [!NOTE]
> In questo articolo viene descritto come spostare le risorse nell'offerta di un account di Azure esistente. Se si desidera effettivamente modificare l'offerta dell'account Azure (ad esempio effettuando l'aggiornamento da gratuito a pagamento a consumo) è necessario convertire la sottoscrizione.
> * Per aggiornare una versione di valutazione gratuita, vedere [Aggiornare la versione di valutazione gratuita o la sottoscrizione di Azure per Microsoft Imagine alla sottoscrizione con pagamento in base al consumo](..//billing/billing-upgrade-azure-subscription.md).
> * Per modificare un account con pagamento in base al consumo, vedere [Modificare la sottoscrizione Azure con pagamento in base al consumo in un'offerta diversa](../billing/billing-how-to-switch-azure-offer.md).
> * Se non è possibile convertire la sottoscrizione, [creare una richiesta di supporto tecnico di Azure](../azure-supportability/how-to-create-azure-support-request.md). Selezionare **Gestione delle sottoscrizioni** per il tipo di problema.

## <a name="when-to-call-azure-support"></a>Quando chiamare il supporto tecnico di Azure

È possibile spostare la maggior parte delle risorse tramite le operazioni self-service descritte in questo articolo. Usare le operazioni self-service per:

* Spostare le risorse di Resource Manager.
* Spostare le risorse classiche in base alle [limitazioni della distribuzione classica](#classic-deployment-limitations).

Contattare il [supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se è necessario:

* Spostare le risorse in un nuovo account di Azure (e tenant di Azure Active Directory) e serve assistenza con le istruzioni nella sezione precedente.
* Spostare le risorse classiche ma si verificano problemi relativi alle limitazioni.

## <a name="services-that-can-be-moved"></a>Servizi che possono essere spostati

L'elenco seguente fornisce un riepilogo generale dei servizi di Azure che possono essere spostati in un nuovo gruppo di risorse e sottoscrizione. Per maggiori dettagli vedere [Move operation support for resources](move-support-resources.md) (Spostare il supporto operativo per le risorse).

* Analysis Services
* Gestione API
* App del servizio app (app Web): vedere [Limitazioni del servizio app](#app-service-limitations)
* Certificati del servizio app - vedere [Limitazioni del certificato del servizio app](#app-service-certificate-limitations)
* Automazione
* Azure Active Directory B2C
* Azure Cosmos DB
* Esplora dati di Azure
* Database di Azure per MySQL
* Database di Azure per PostgreSQL
* Azure DevOps: le organizzazioni di Azure DevOps con gli acquisti di estensione non Microsoft devono [annullare gli acquisti](https://go.microsoft.com/fwlink/?linkid=871160) prima di spostare l'account tra le sottoscrizioni.
* Mappe di Azure
* Servizio di inoltro di Azure
* Azure Stack - registrazioni
* Batch
* Servizi BizTalk
* Servizio bot
* RETE CDN
* Servizi cloud: vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Servizi cognitivi
* Registro contenitori: non è possibile spostare un registro contenitori quando è abilitata la replica geografica.
* Content Moderator
* Gestione costi
* Customer Insights
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Griglia di eventi
* Hub eventi
* Frontdoor
* Cluster HDInsight - vedere [Limitazioni di HDInsight](#hdinsight-limitations)
* Iot Central
* Hub IoT
* Key Vault: le istanze di Key Vault usate per la crittografia dei dischi non possono essere spostate in gruppi di risorse presenti nella stessa sottoscrizione o in sottoscrizioni diverse.
* Servizi di bilanciamento del carico: è possibile spostare il servizio di bilanciamento del carico con SKU Basic. Il servizio di bilanciamento del carico dello SKU Standard non può essere spostato.
* Log Analytics
* App per la logica
* Machine Learning: i servizi Web di Machine Learning Studio possono essere spostati in un nuovo gruppo di risorse nella stessa sottoscrizione, ma non in un'altra sottoscrizione. Altre risorse di Machine Learning possono essere spostate da una sottoscrizione all'altra.
* Dischi gestiti: vedere [Limitazioni delle macchine virtuali](#virtual-machines-limitations)
* Identità gestita assegnata dall'utente
* Servizi multimediali
* Monitoraggio: verificare che lo spostamento nella nuova sottoscrizione non comporti il superamento delle [quote di sottoscrizione](../azure-subscription-service-limits.md#monitor-limits).
* Hub di notifica
* Operational Insights
* Operations Management
* Dashboard del portale
* Power BI - sia Power BI Embedded che Raccolta di aree di lavoro di Power BI
* IP pubblico: è possibile spostare l'IP pubblico con SKU Basic. L'indirizzo IP pubblico dello SKU Standard non può essere spostato.
* Insieme di credenziali di Servizi di ripristino: registrare la sottoscrizione per l'[anteprima pubblica limitata](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault).
* Cache Redis di Azure: se l'istanza di Cache Redis di Azure è configurata con una rete virtuale, l'istanza non può essere spostata in una sottoscrizione diversa. Vedere [Limitazioni delle reti virtuali](#virtual-networks-limitations).
* Utilità di pianificazione
* Ricerca: non è possibile spostare più risorse di Ricerca in aree diverse in un'unica operazione. Al contrario, è possibile spostarle con operazioni separate.
* Bus di servizio
* Service Fabric
* Service Fabric Mesh
* Servizio SignalR
* Storage: gli account di archiviazione in aree diverse non possono essere spostati nella stessa operazione. Usare invece operazioni separate per ogni area.
* Archiviazione (classica): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Analisi di flusso: i processi di analisi di flusso non possono essere spostati durante l'esecuzione.
* Server di database SQL: il database e il server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database. Questo comportamento si applica al database SQL di Azure e ai database di Azure SQL Data Warehouse.
* Time Series Insights
* Gestione traffico
* Macchine virtuali: per le macchine virtuali con dischi gestiti vedere [Limitazioni delle macchine virtuali](#virtual-machines-limitations)
* Macchine virtuali (classiche): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* Set di scalabilità di macchine virtuali: vedere [Limitazioni delle macchine virtuali](#virtual-machines-limitations)
* Reti virtuali, vedere [Limitazioni delle reti virtuali](#virtual-networks-limitations)
* Gateway VPN

### <a name="services-that-cannot-be-moved"></a>Servizi che non possono essere spostati

L'elenco seguente fornisce un riepilogo generale dei servizi di Azure che non possono essere spostati in un nuovo gruppo di risorse e sottoscrizione. Per maggiori dettagli vedere [Move operation support for resources](move-support-resources.md) (Spostare il supporto operativo per le risorse).

* AD Domain Services
* Servizio ibrido per l'integrità di AD
* Gateway applicazione
* Migrazione del database di Azure
* Azure Databricks
* Firewall di Azure
* Azure Migrate
* Certificati: i certificati del servizio app possono essere spostati, ma i certificati caricati presentano alcune [limitazioni](#app-service-limitations).
* Istanze di Container
* Servizio contenitore
* Data Box
* Dev Spaces
* Dynamics LCS
* ExpressRoute
* Kubernetes Service
* Servizi lab: lo spostamento in un nuovo gruppo di risorse nella stessa sottoscrizione è abilitato, ma lo spostamento tra sottoscrizioni non lo è.
* Applicazioni gestite
* Genomica di Microsoft
* NetApp
* SAP HANA in Azure
* Security
* Site Recovery
* Gestione dispositivi StorSimple
* Reti virtuali (classiche): vedere [Limitazioni della distribuzione classica](#classic-deployment-limitations)

## <a name="limitations"></a>Limitazioni

Questa sezione illustra come gestire scenari complessi per lo spostamento di risorse. Esistono le limitazioni seguenti:

* [Limitazioni delle macchine virtuali](#virtual-machines-limitations)
* [Limitazioni delle reti virtuali](#virtual-networks-limitations)
* [Limitazioni del servizio app](#app-service-limitations)
* [Limitazioni del certificato del servizio app](#app-service-certificate-limitations)
* [Limitazioni della distribuzione classica](#classic-deployment-limitations)
* [Limitazioni di Servizi di ripristino](#recovery-services-limitations)
* [Limitazioni di HDInsight](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>Limitazioni delle macchine virtuali

Dal 24 settembre 2018 è possibile spostare i dischi gestiti. È supportato lo spostamento di macchine virtuali con dischi gestiti, immagini gestite, snapshot gestiti e set di disponibilità con macchine virtuali che usano dischi gestiti.

Non sono ancora supportati gli scenari seguenti:

* Le macchine virtuali con certificato archiviato in Key Vault possono essere spostate in un nuovo gruppo di risorse nella stessa sottoscrizione, ma non da una sottoscrizione a un'altra.
* I dischi gestiti in zone di disponibilità non possono essere spostati in una sottoscrizione diversa.
* Il set di scalabilità di macchine virtuali con il servizio di bilanciamento del carico dello SKU Standard o l'indirizzo IP pubblico dello SKU Standard non può essere spostato.
* Non è possibile spostare da un gruppo di risorse o una sottoscrizione a un'altra macchine virtuali create a partire da risorse Marketplace con piani assegnati. Sottoporre a deprovisioning le macchine virtuali nella sottoscrizione in cui si trovano e distribuirle di nuovo nella nuova sottoscrizione.

Per spostare le macchine virtuali configurate con Backup di Azure, usare la soluzione alternativa seguente:

* Individuare la località della macchina virtuale.
* Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<location of your VM>_1`, ad esempio AzureBackupRG_westus2_1
* Nel portale di Azure selezionare quindi l'opzione "Mostra tipi nascosti"
* In PowerShell usare il cmdlet `Get-AzureRmResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Nella CLI usare il `az resource list -g AzureBackupRG_<location of your VM>_1`
* Trovare la risorsa di tipo `Microsoft.Compute/restorePointCollections` con modello di denominazione `AzureBackup_<name of your VM that you're trying to move>_###########`
* Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.
* Al termine dell'operazione, sarà possibile spostare la macchina virtuale. È possibile spostare l'insieme di credenziali e la macchina virtuale nella sottoscrizione di destinazione. Dopo lo spostamento è possibile continuare a eseguire i backup senza alcuna perdita di dati.
* Per informazioni sullo spostamento degli insiemi di credenziali di Servizi di ripristino per il backup, vedere [Limitazioni di Servizi di ripristino](#recovery-services-limitations).

### <a name="virtual-networks-limitations"></a>Limitazioni delle reti virtuali

Quando si esegue lo spostamento di una rete virtuale, è necessario spostare anche le relative risorse dipendenti. Per i gateway VPN, è necessario spostare gli indirizzi IP, i gateway di rete virtuale e tutte le risorse di connessione associata. I gateway di rete locali possono trovarsi in un gruppo di risorse diverso.

Per spostare una rete virtuale con peering, è prima necessario disabilitare il peering. Dopo la disabilitazione del peering è possibile spostare la rete virtuale. Riabilitare il peering della rete virtuale dopo lo spostamento.

Non è possibile spostare una rete virtuale in un'altra sottoscrizione se la rete virtuale contiene una subnet con collegamenti di navigazione delle risorse. Se un'istanza di Cache Redis di Azure viene distribuita in una subnet, in tale subnet è presente un collegamento di navigazione per la risorsa.

### <a name="app-service-limitations"></a>Limitazioni del servizio app

Le limitazioni per lo spostamento delle risorse del Servizio app di Azure variano a seconda che lo spostamento avvenga all'interno di una sottoscrizione o a una nuova sottoscrizione. Se l'app Web usa un certificato del servizio app, vedere [Limitazioni del certificato del servizio app](#app-service-certificate-limitations)

#### <a name="moving-within-the-same-subscription"></a>Spostamento all'interno della stessa sottoscrizione

Quando si sposta un'app Web _nella stessa sottoscrizione_, non è possibile spostare i certificati SSL di terze parti. È comunque possibile spostare un'app Web nel nuovo gruppo di risorse senza spostare il relativo certificato di terze parti mantenendo effettiva la funzionalità SSL dell'app.

Se si desidera spostare il certificato SSL con l'app Web, attenersi alla procedura seguente:

1. Eliminare il certificato di terze parti dall'app Web, ma conservare una copia del certificato
2. Spostare l'app Web.
3. Caricare il certificato di terze parti nell'app Web spostata.

#### <a name="moving-across-subscriptions"></a>Spostamento tra sottoscrizioni

Quando si sposta un'app Web _tra sottoscrizioni_, si applicano le limitazioni seguenti:

- Il gruppo di risorse di destinazione non deve contenere risorse del servizio app esistenti. Le risorse del servizio app includono:
    - App Web
    - Piani di servizio app
    - Certificati SSL importati o caricati
    - Ambienti del servizio app
- Tutte le risorse del servizio app nel gruppo di risorse devono essere spostate insieme.
- Le risorse del servizio app possono essere spostate solo dal gruppo di risorse in cui sono state originariamente create. Se una risorsa del servizio app non si trova più nel gruppo di risorse originale, deve essere spostata nuovamente in tale gruppo prima di poter essere spostata tra le sottoscrizioni.

### <a name="app-service-certificate-limitations"></a>Limitazioni del certificato del servizio app

È possibile spostare il certificato del servizio app in un nuovo gruppo di risorse oppure in una nuova sottoscrizione. Se il certificato del servizio app è associato a un'app Web, è necessario eseguire alcuni passaggi prima di spostare le risorse in una nuova sottoscrizione. Eliminare l'associazione SSL e il certificato privato dall'app Web prima di spostare le risorse. Il certificato del servizio app non deve essere eliminato, è sufficiente eliminare solo il certificato privato nell'app Web.

### <a name="classic-deployment-limitations"></a>Limitazioni della distribuzione classica

Le opzioni per lo spostamento delle risorse distribuite con il modello classico variano a seconda che lo spostamento avvenga all'interno di una sottoscrizione o a una nuova sottoscrizione.

#### <a name="same-subscription"></a>Stessa sottoscrizione

Quando si spostano risorse da un gruppo di risorse a un altro gruppo di risorse nella stessa sottoscrizione, sono valide le restrizioni seguenti:

* Le reti virtuali (classiche) non possono essere spostate.
* Le macchine virtuali (classiche) devono essere spostate con il servizio cloud.
* Il servizio cloud può essere spostato solo quando lo spostamento include tutte le macchine virtuali del servizio stesso.
* È possibile spostare un solo servizio cloud alla volta.
* È possibile spostare un solo account di archiviazione (classico) alla volta.
* Un account di archiviazione (classico) non può essere spostato nella stessa operazione di spostamento che include una macchina virtuale o un servizio cloud.

Per spostare le risorse classiche in un nuovo gruppo di risorse all'interno della stessa sottoscrizione usare le operazioni di spostamento standard tramite il [portale](#use-portal), [Azure PowerShell](#use-powershell), l'[interfaccia della riga di comando di Azure](#use-azure-cli) o l'[API REST](#use-rest-api). Usare le stesse operazioni eseguite per lo spostamento di risorse di Resource Manager.

#### <a name="new-subscription"></a>Nuova sottoscrizione

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

### <a name="recovery-services-limitations"></a>Limitazioni dei servizi di ripristino

 Per spostare un insieme di credenziali di Servizi di ripristino, registrare la sottoscrizione per l'[anteprima pubblica limitata](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault).

Attualmente, è possibile spostare un solo insieme di credenziali di Servizi di ripristino per ogni area contemporaneamente. Non è possibile spostare insiemi di credenziali di cui eseguire il backup di file di Azure, Sincronizzazione file di Azure o SQL in macchine virtuali IaaS.

Se non si sposta una macchina virtuale con l'insieme di credenziali, i punti di ripristino delle macchine virtuali correnti rimangono nell'insieme di credenziali fino alla scadenza. Sia che macchina virtuale venga spostata nell'insieme di credenziali o meno, è possibile ripristinare la macchina virtuale dalla cronologia di backup nell'insieme di credenziali.

L'insieme di credenziali di Servizi di ripristino non supporta i backup tra più sottoscrizioni. Se si sposta un insieme di credenziali con i dati di backup di macchine virtuali tra sottoscrizioni diverse, è necessario spostare le macchine virtuali nella stessa sottoscrizione e usare lo stesso gruppo di risorse di destinazione per continuare l'esecuzione dei backup.

I criteri di backup definiti per l'insieme di credenziali vengono mantenuti dopo lo spostamento dell'insieme di credenziali. Dopo lo spostamento, la creazione di report e il monitoraggio devono essere nuovamente impostati per l'insieme di credenziali.

Per spostare una macchina virtuale in una nuova sottoscrizione senza spostare l'insieme di credenziali di Servizi di ripristino:

 1. Interrompere temporaneamente il backup
 1. [Eliminare il punto di ripristino](#virtual-machines-limitations). Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.
 1. Spostare le macchine virtuali nella nuova sottoscrizione
 1. Proteggerle nuovamente con un nuovo insieme di credenziali nella sottoscrizione

Lo spostamento non è abilitato per le risorse di archiviazione, di rete o di calcolo usate per configurare il ripristino di emergenza con Azure Site Recovery. Ad esempio, si supponga di avere configurato la replica delle macchine locali su un account di archiviazione (Storage1) e di desiderare che il computer protetto venga avviato dopo il failover in Azure come macchina virtuale (VM1) collegata a una rete virtuale (Network1). Non è possibile spostare una di queste risorse di Azure - Storage1 VM1 e Network1 - nei gruppi di risorse all'interno della stessa sottoscrizione o tra le sottoscrizioni.

### <a name="hdinsight-limitations"></a>Limitazioni di HDInsight

È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.

Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

## <a name="checklist-before-moving-resources"></a>Controllo prima di spostare le risorse

Prima di spostare una risorsa, è necessario eseguire alcuni passi importanti. La verifica di queste condizioni consente di evitare errori.

1. Le sottoscrizioni di origine e di destinazione devono trovarsi nello stesso [tenant di Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Per verificare che entrambe le sottoscrizioni contengano lo stesso ID tenant, usare Azure PowerShell o l'interfaccia della riga di comando di Azure.

  Per Azure PowerShell usare:

  ```azurepowershell-interactive
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
  * [Come associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Il provider di risorse della risorsa da spostare deve essere registrato nella sottoscrizione di destinazione, altrimenti un errore indica che la **sottoscrizione non è registrata per un tipo di risorsa**. Questo errore può verificarsi se si sposta una risorsa in una nuova sottoscrizione, ma la sottoscrizione non è mai stata usata con tale tipo di risorsa.

  In PowerShell, per ottenere lo stato della registrazione usare i comandi seguenti:

  ```azurepowershell-interactive
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Per registrare un provider di risorse, usare:

  ```azurepowershell-interactive
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

1. L'account che sposta le risorse deve avere almeno le autorizzazioni seguenti:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** sul gruppo di risorse di origine.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** sul gruppo di risorse di destinazione.

1. Prima di spostare le risorse, controllare le quote della sottoscrizione in cui si desidera spostare le risorse. Se lo spostamento di risorse causa il superamento dei limiti della sottoscrizione, è necessario verificare se è possibile richiedere un aumento della quota. Per un elenco dei limiti e su come richiedere un aumento, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

1. Quando possibile, suddividere spostamenti di grandi dimensioni in operazioni di spostamento separate. Resource Manager restituisce immediatamente un errore quando sono presenti più di 800 risorse in un'unica operazione. Anche lo spostamento di meno di 800 risorse può non riuscire a causa di un timeout.

1. Il servizio deve abilitare lo spostamento di risorse. Per determinare se lo spostamento avrà esito positivo, [convalidare la richiesta di spostamento](#validate-move). Vedere le sezioni riportate di seguito in questo articolo riguardante i [servizi che consentono di spostare risorse](#services-that-can-be-moved) e [i servizi che invece non lo consentono](#services-that-cannot-be-moved).

## <a name="validate-move"></a>Convalidare lo spostamento

L'[operazione di convalida dello spostamento](/rest/api/resources/resources/validatemoveresources) consente di testare lo scenario di spostamento senza realmente spostare le risorse. Usare questa operazione per determinare se lo spostamento avrà esito positivo. Per eseguire questa operazione, è necessario:

* il nome del gruppo di risorse di origine
* l'ID risorsa della risorsa del gruppo di risorse di destinazione
* l'ID risorsa di ogni risorsa da spostare
* il [token di accesso](/rest/api/azure/#acquire-an-access-token) per l'account

Inviare la richiesta seguente:

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

Con un corpo della richiesta:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se la richiesta viene formattata correttamente, l'operazione restituisce:

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Il codice di stato 202 indica che la richiesta di convalida è stata accettata, ma non è stato ancora determinato se l'operazione di spostamento avrà esito positivo. Il valore `location` contiene un URL che si usa per controllare lo stato dell'operazione a esecuzione prolungata.  

Per controllare lo stato, inviare la richiesta seguente:

```
GET <location-url>
Authorization: Bearer <access-token>
```

Mentre l'operazione è ancora in esecuzione, si continua a ricevere il codice di stato 202. Attendere il numero di secondi indicato nel valore `retry-after` prima di riprovare. Se l'operazione di spostamento ha esito positivo, viene visualizzato il codice di stato 204. Se la convalida dello spostamento ha esito negativo, viene visualizzato un messaggio di errore, ad esempio:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="move-resources"></a>Spostare le risorse

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />Uso del portale di Azure

Per spostare le risorse, selezionare il gruppo con tali risorse, quindi usare il pulsante **Sposta**.

![Spostare le risorse](./media/resource-group-move-resources/select-move.png)

Selezionare se si desidera spostare le risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione.

Selezionare le risorse da spostare e il gruppo di risorse di destinazione. Confermare di dover aggiornare gli script per queste risorse e selezionare **OK**. Se si seleziona l'icona del comando Modifica sottoscrizione nel passaggio precedente, è necessario anche selezionare la sottoscrizione di destinazione.

![Selezione della destinazione](./media/resource-group-move-resources/select-destination.png)

In **Notifiche**si nota che è in corso l'operazione di spostamento.

![Visualizzare lo stato dello spostamento](./media/resource-group-move-resources/show-status.png)

Al completamento dell'operazione si riceverà la notifica del risultato.

![Visualizzare il risultato dello spostamento](./media/resource-group-move-resources/show-result.png)

### <a name="by-using-azure-powershell"></a>Usando Azure PowerShell

Per spostare le risorse esistenti in un gruppo di risorse o in una sottoscrizione diversa, usare il comando [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) . L'esempio seguente illustra come spostare diverse risorse in un nuovo gruppo di risorse.

```azurepowershell-interactive
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Per eseguire lo spostamento in una nuova sottoscrizione, includere un valore per il parametro `DestinationSubscriptionId`.

### <a name="by-using-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Per spostare risorse esistenti in un altro gruppo di risorse o un'altra sottoscrizione, usare il comando [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move). Fornire gli ID risorsa delle risorse da spostare. L'esempio seguente illustra come spostare diverse risorse in un nuovo gruppo di risorse. Nel parametro `--ids` inserire un elenco delimitato da spazi di ID di risorse da spostare.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Per spostare in una nuova sottoscrizione, inserire il parametro `--destination-subscription-id`.

### <a name="by-using-rest-api"></a>Uso dell'API REST

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
