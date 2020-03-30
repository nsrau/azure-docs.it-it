---
title: Personalizzare i cluster di Azure HDInsight usando le azioni scriptCustomize Azure HDInsight clusters by using script actions
description: Aggiungere componenti personalizzati ai cluster HDInsight usando le azioni script. Le azioni script sono script Bash che possono essere usati per personalizzare la configurazione del cluster o aggiungere servizi e utilità come Hue, Solr o R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 12e6892930afe8ba9c7bad9b05fd39eeaf8835fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272500"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Personalizzare i cluster di Azure HDInsight usando le azioni scriptCustomize Azure HDInsight clusters by using script actions

Azure HDInsight offre un metodo di configurazione denominato **azioni script** che richiamano script personalizzati per personalizzare il cluster. Questi script vengono usati per installare i componenti aggiuntivi e modificare le impostazioni di configurazione. Le azioni script possono essere usate durante o dopo la creazione del cluster.

Le azioni script possono anche essere pubblicate in Azure Marketplace come applicazione HDInsight. Per altre informazioni sulle applicazioni HDInsight, vedere [Pubblicare un'applicazione HDInsight in Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Autorizzazioni

Per un cluster HDInsight aggiunto a un dominio, sono necessarie due autorizzazioni di Apache Ambari per l'uso di azioni script con il cluster:

* **AMBARI. ESEGUIRE\_\_IL COMANDO PERSONALIZZATO**. il ruolo di amministratore di Ambari ha questa autorizzazione per impostazione predefinita.
* **CLUSTER. ESEGUIRE\_\_IL COMANDO PERSONALIZZATO**. sia l'amministratore del cluster HDInsight che l'amministratore di Ambari hanno questa autorizzazione per impostazione predefinita.

Per altre informazioni sull'uso delle autorizzazioni con HDInsight aggiunto a un dominio, vedere [Gestire i cluster HDInsight con Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Controllo di accesso

Se non si è l'amministratore o il proprietario della sottoscrizione di Azure, l'account deve disporre almeno dell'accesso Collaboratore al gruppo di risorse contenente il cluster HDInsight.

Se si crea un cluster HDInsight, è necessario che un utente con almeno l'accesso Collaboratore alla sottoscrizione di Azure abbia registrato in precedenza il provider per HDInsight. La registrazione del provider viene eseguita quando un utente con accesso Collaboratore alla sottoscrizione crea una risorsa per la prima volta nella sottoscrizione stessa. Questa operazione può essere eseguita anche senza creare una risorsa se si [registra un provider tramite REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Ottenere altre informazioni sull'uso della gestione degli accessi:

* [Introduzione alla gestione degli accessi nel portale di Azure](../role-based-access-control/overview.md)
* [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Informazioni sulle azioni script

Un'azione di script è uno script Bash eseguito sui nodi di un cluster HDInsight. Di seguito sono riportate le caratteristiche e funzionalità delle azioni script:

* Devono essere archiviate in un URI accessibile dal cluster HDInsight. Di seguito vengono indicate alcune tra le posizioni di archiviazione possibili:

    * Per i cluster regolari:

      * ADLS Gen1: l'entità servizio usata da HDInsight per accedere a Data Lake Storage deve disporre dell'accesso in lettura allo script. Il formato dell'URI per gli script archiviati in Data Lake Storage Gen1 è `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Un BLOB in un account di archiviazione di Azure che rappresenta l'account di archiviazione primario o aggiuntivo per il cluster HDInsight. HDInsight può accedere a entrambi i tipi di account di archiviazione durante la creazione del cluster.

        > [!IMPORTANT]  
        > Non ruotare la chiave di archiviazione in questo account di archiviazione di Azure, in quanto causerà l'esito negativo delle azioni script successive con script archiviati.

      * Un servizio di condivisione di file pubblico accessibile tramite http:// percorsi. Esempi sono BLOB di Azure, GitHub, OneDrive.Examples are Azure Blob, GitHub, OneDrive. Per gli URI di esempio, vedere [Script di esempio di azione script](#example-script-action-scripts).

     * Per i cluster con ESP, sono supportati gli URI wasb:// o wasbs:// o http[s]://.

* Può essere limitato per l'esecuzione solo in determinati tipi di nodo, ad esempio i nodi head o i nodi di lavoro.

* Possono essere persistenti o ad hoc.

    Le azioni script persistenti devono avere un nome univoco. Gli script persistenti vengono usati per personalizzare i nuovi nodi di lavoro aggiunti al cluster tramite operazioni di ridimensionamento. Uno script persistente può anche applicare modifiche a un altro tipo di nodo durante operazioni di ridimensionamento, ad esempio un nodo head.

    Gli script ad hoc non sono persistenti. Le azioni script usate durante la creazione di un cluster vengono automaticamente rese persistenti. Non vengono infatti applicati ai nodi di lavoro aggiunti al cluster dopo l'esecuzione dello script. È poi possibile alzare di livello uno script ad hoc per renderlo persistente o abbassare di livello uno script persistente per renderlo ad hoc. Gli script che hanno esito negativo non vengono resi persistenti, anche in presenza di indicazioni specifiche in tal senso.

* Possono accettare parametri usati dallo script durante l'esecuzione.

* Eseguire lo script con privilegi a livello radice nei nodi del cluster.

* Può essere usato tramite il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o HDInsight .NET SDK.

Il cluster mantiene una cronologia di tutti gli script eseguiti. La cronologia è utile quando è necessario trovare l'ID di uno script per le operazioni di innalzamento o abbassamento di livello.

> [!IMPORTANT]  
> Non esiste un metodo automatico per annullare le modifiche apportate da un'azione script. Annullare manualmente le modifiche o fornire uno script che le inverta.

### <a name="script-action-in-the-cluster-creation-process"></a>Azione script nel processo di creazione di cluster

Le azioni script usate durante la creazione del cluster sono leggermente diverse da quelle eseguite in un cluster esistente:

* Lo script viene reso automaticamente persistente.

* Un errore nello script può causare l'esito negativo del processo di creazione del cluster.

Il diagramma seguente illustra quando viene eseguita l'azione script durante il processo di creazione:

![Personalizzazione di cluster HDInsight e fasi durante la creazione di un cluster][img-hdi-cluster-states]

Lo script viene eseguito durante la configurazione di HDInsight. Lo script viene eseguito in parallelo in tutti i nodi specificati nel cluster. Viene eseguito con privilegi a livello radice sui nodi.

È possibile eseguire operazioni come l'arresto e l'avvio di servizi, inclusi quelli correlati ad Apache Hadoop. Se si arrestano i servizi, assicurarsi che il servizio Ambari e altri servizi correlati ad Hadoop siano in esecuzione prima che sia completato lo script. Questi servizi sono necessari per determinare correttamente l'integrità e lo stato del cluster durante la creazione.

Durante la creazione del cluster, è possibile usare più azioni script alla volta. Questi script vengono richiamati nell'ordine in cui sono stati specificati.

> [!IMPORTANT]  
> Le azioni script devono terminare entro 60 minuti o stime. Durante il provisioning del cluster, lo script viene eseguito contemporaneamente ad altri processi di installazione e configurazione. In caso di concorrenza per risorse come il tempo di CPU o la larghezza di banda di rete, lo script potrebbe richiedere più tempo per completare l'operazione rispetto al tempo che impiegherebbe in un ambiente di sviluppo.
>
> Per ridurre al minimo il tempo necessario per eseguire lo script, evitare attività come il download e la compilazione di applicazioni dall'origine. Precompilare le applicazioni e archiviare i file binari in Archiviazione di Azure.

### <a name="script-action-on-a-running-cluster"></a>Azione script in un cluster in esecuzione

Un errore in uno script eseguito in un cluster già in esecuzione non determina automaticamente uno stato di errore del cluster. Al termine di uno script, il cluster deve tornare a uno stato In esecuzione. Anche se lo stato del cluster è In esecuzione, lo script non riuscito potrebbe includere attività interrotte. Ad esempio, uno script potrebbe eliminare file richiesti dal cluster.

Le azioni script vengono eseguite con privilegi a livello radice. Assicurarsi di comprendere le operazioni eseguite da uno script prima di applicarlo al cluster.

Quando si applica uno script a un cluster, lo stato del cluster passa da **In esecuzione** ad **Accettato**. Quindi passa a **Configurazione di HDInsight** e infine di nuovo a **In esecuzione** per gli script con esito positivo. Lo stato dello script viene registrato nella cronologia delle azioni script. Queste informazioni indicano se lo script ha avuto esito positivo o negativo. Ad esempio, il cmdlet di PowerShell `Get-AzHDInsightScriptActionHistory` mostra lo stato di uno script. Verranno restituite informazioni simili al testo seguente:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Se si cambia la password dell'utente del cluster (admin) dopo la creazione del cluster stesso, le azioni script eseguite su questo cluster potrebbero avere esito negativo. In presenza di azioni script persistenti destinate a nodi di lavoro, questi script potrebbero avere esito negativo se si ridimensiona il cluster.

## <a name="example-script-action-scripts"></a>Script di esempio di azione script

Gli script di azione script possono essere usati tramite le utilità seguenti:

* Portale di Azure
* Azure PowerShell
* Interfaccia della riga di comando di Azure
* HDInsight .NET SDK

HDInsight fornisce script di esempio per installare i componenti seguenti nei cluster HDInsight:

| Nome | Script |
| --- | --- |
| Aggiungere un account di archiviazione di Azure |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Vedere [Aggiungere altri account di archiviazione a HDInsight](hdinsight-hadoop-add-storage.md). |
| Installare Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Vedere [Installare e usare Hue nei cluster Hadoop di HDInsight](hdinsight-hadoop-hue-linux.md). |
| Precaricare le librerie Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Vedere [Aggiungere librerie Apache Hive personalizzate durante la creazione del cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Azione script durante la creazione del clusterScript action during cluster creation

In questa sezione vengono illustrate le diverse modalità d'uso delle azioni script durante la creazione di un cluster HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Usare un'azione script durante la creazione di un cluster dal portale di Azure

1. Iniziare a creare un cluster come descritto in Creare cluster basati su Linux in HDInsight usando il portale di Azure.Start to create a cluster as described in [Create Linux-based clusters in HDInsight by using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md). Dalla scheda **Configurazione e prezzi,** selezionare **: Aggiungi azione script**.

    ![Azione script cluster del portale di AzureAzure portal cluster script action](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Usare la voce __Seleziona uno script__ per selezionare uno script pronto. Per usare uno script personalizzato, selezionare __Personalizzato__. Specificare quindi __Nome__ e __URI script Bash__ per lo script.

    ![Aggiungere uno script nel modulo di selezione script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    La tabella seguente illustra gli elementi nel modulo:

    | Proprietà | valore |
    | --- | --- |
    | Selezionare uno script | Per utilizzare uno script personalizzato, selezionare __Personalizzato__. In caso contrario, selezionare uno degli script disponibili. |
    | Nome |Specificare un nome per l'azione script. |
    | URI script Bash |Specificare l'URI dello script. |
    | Capo/Operaio/Custode zoologico |Specificare i nodi su cui viene eseguito lo script: **Head**, **Worker**, o **zooKeeper**. |
    | Parametri |Specificare i parametri, se richiesti dallo script. |

    Usare la voce __Salvare questa azione script in modo permanente__ per assicurarsi che lo script venga applicato durante le operazioni di ridimensionamento.

1. Selezionare __Crea__ per salvare lo script. È quindi possibile usare __+ Invia nuovo__ per aggiungere un altro script.

    ![HDInsight più azioni di scriptHDInsight multiple script actions](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Al termine dell'aggiunta di script, si torna alla scheda **Configurazione e prezzi.**

1. Completare i passaggi rimanenti per la creazione del cluster come di consueto.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Usare un'azione script dai modelli di Azure Resource Manager

Le azioni script possono essere usate con i modelli di Azure Resource Manager. Per un esempio, vedere [Create HDInsight Linux Cluster and run a script action](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/) (Creare un cluster Linux HDInsight ed eseguire un'azione script).

In questo esempio, l'azione script viene aggiunta tramite il codice seguente:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Ottenere altre informazioni su come distribuire un modello:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Usare un'azione script durante la creazione di un cluster da Azure PowerShell

In questa sezione si utilizza il cmdlet [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) per richiamare script per personalizzare un cluster. Prima di iniziare, assicurarsi di installare e configurare Azure PowerShell. Per utilizzare questi comandi di PowerShell, è necessario il [modulo A .](https://docs.microsoft.com/powershell/azure/overview)

Lo script seguente mostra come applicare un'azione script durante la creazione di un cluster con PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

La creazione del cluster può richiedere alcuni minuti.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Usare un'azione script durante la creazione di un cluster da HDInsight .NET SDK

HDInsight .NET SDK fornisce librerie client che semplificano l'uso di HDInsight da un'applicazione .NET. Per un esempio di codice, consultate [Azioni script](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Azione di script in un cluster in esecuzione

Questa sezione illustra come applicare azioni script a un cluster in esecuzione.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Applicare un'azione script a un cluster in esecuzione dal portale di Azure

1. Accedere al [portale](https://portal.azure.com) di Azure e individuare il cluster.

1. Nella visualizzazione predefinita, in **Impostazioni**, selezionare **Azioni script**.

1. Nella parte superiore della pagina **Azioni script** selezionare **+ Invia nuova**.

    ![Aggiungere uno script a un cluster in esecuzione](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Usare la voce __Seleziona uno script__ per selezionare uno script pronto. Per usare uno script personalizzato, selezionare __Personalizzato__. Specificare quindi __Nome__ e __URI script Bash__ per lo script.

    ![Aggiungere uno script nel modulo di selezione script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    La tabella seguente illustra gli elementi nel modulo:

    | Proprietà | valore |
    | --- | --- |
    | Selezionare uno script | Per usare uno script personalizzato, selezionare __Personalizzato__. In caso contrario, selezionare uno degli script disponibili. |
    | Nome |Specificare un nome per l'azione script. |
    | URI script Bash |Specificare l'URI dello script. |
    | Head/Worker/Zookeeper |Specificare i nodi su cui viene eseguito lo script: **Head**, **Worker**, o **zooKeeper**. |
    | Parametri |Specificare i parametri, se richiesti dallo script. |

    Usare la voce __Salvare questa azione script in modo permanente__ per assicurarsi che lo script venga applicato durante le operazioni di ridimensionamento.

1. Infine, selezionare il pulsante **Crea** per applicare lo script al cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Applicare un'azione script a un cluster in esecuzione da Azure PowerShell

Per utilizzare questi comandi di PowerShell, è necessario il [modulo A .](https://docs.microsoft.com/powershell/azure/overview) L'esempio seguente mostra come applicare un'azione script a un cluster in esecuzione:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Al termine dell'operazione, vengono visualizzate informazioni simili alle seguenti:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Applicare un'azione script a un cluster in esecuzione dall'interfaccia della riga di comando di Azure

Prima di iniziare, assicurarsi di installare e configurare l'interfaccia della riga di comando di Azure. Assicurarsi di disporre della versione più recente. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Eseguire l'autenticazione alla sottoscrizione di Azure:

    ```azurecli
    az login
    ```

1. Applicare un'azione script a un cluster in esecuzione:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    I ruoli `headnode` `workernode`validi `zookeepernode` `edgenode`sono , , , . Se lo script deve essere applicato a diversi tipi di nodo, separare i ruoli da uno spazio. Ad esempio: `--roles headnode workernode`.

    Per salvare lo script in modo permanente, aggiungere `--persist-on-success`. È anche possibile salvare lo script in modo permanente in un secondo momento usando `az hdinsight script-action promote`.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Applicare un'azione script a un cluster in esecuzione usando l'API REST

Vedere [Cluster REST API in Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx) (API REST Cluster in Azure HDInsight).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Applicare un'azione script a un cluster in esecuzione da HDInsight .NET SDK

Per un esempio dell'uso di .NET SDK per applicare script a un cluster, vedere [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Applicare un'azione script su un cluster HDInsight basato su Linux in esecuzione).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Visualizzare la cronologia e alzare e abbassare di livello le azioni script

### <a name="the-azure-portal"></a>Portale di Azure

1. Accedere al [portale](https://portal.azure.com) di Azure e individuare il cluster.

1. Nella visualizzazione predefinita, in **Impostazioni**, selezionare **Azioni script**.

1. Una cronologia degli script per il cluster viene visualizzata nella sezione Azioni script. Queste informazioni includono un elenco degli script persistenti. Lo screenshot seguente mostra che è stato eseguito lo script Solr nel cluster. Lo screenshot non mostra script persistenti.

    ![Le azioni dello script del portale inviano la cronologia](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Selezionare uno script nella cronologia per visualizzare la sezione **Proprietà** per lo script. Nella parte superiore della schermata è possibile eseguire di nuovo lo script o alzarlo di livello.

    ![Alzare di livello le proprietà delle azioni script](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. È anche possibile selezionare i puntini di sospensione **...** a destra delle voci nella sezione Azioni script per eseguire azioni.

    ![Eliminazione delle azioni di script persistentiPersisted script actions delete](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet | Funzione |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Recuperare informazioni sulle azioni script persistenti. Questo cmdlet non annulla le azioni eseguite da uno script, ma rimuove solo il flag persistente.|
| `Get-AzHDInsightScriptActionHistory` |Recuperare una cronologia delle azioni script applicate al cluster o i dettagli di uno script specifico. |
| `Set-AzHDInsightPersistedScriptAction` |Alzare di livello un'azione script ad hoc per renderla un'azione script persistente. |
| `Remove-AzHDInsightPersistedScriptAction` |Abbassare di livello un'azione script persistente per renderla un'azione script ad hoc. |

Lo script di esempio seguente mostra come usare i cmdlet per alzare di livello e poi abbassare di livello uno script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

| Comando | Descrizione |
| --- | --- |
| [az hdinsight script-action delete](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Elimina un'azione di script persistente specificata del cluster. Questo comando non annulla le azioni eseguite da uno script, ma rimuove solo il flag persistente.|
|[az hdinsight script-action execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Eseguire azioni script sul cluster HDInsight specificato.|
| [az hdinsight elenco di script-azione](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Elenca tutte le azioni di script persistenti per il cluster specificato. |
|[az hdinsight script-action elenco-esecuzione-storia](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Elenca la cronologia di esecuzione di tutti gli script per il cluster specificato.|
|[az hdinsight script-action promuovere](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Alza di livello l'esecuzione dello script ad hoc specificata in uno script persistente.|
|[az hdinsight script-action show-execution-details](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Ottiene il dettaglio di esecuzione dello script per l'ID di esecuzione dello script specificato.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Per un esempio relativo all'uso di .NET SDK per recuperare la cronologia degli script da un cluster e alzare o abbassare di livello gli script, vedere [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Applicare un'azione script su un cluster HDInsight basato su Linux in esecuzione).

> [!NOTE]  
> Questo esempio mostra anche come installare un'applicazione HDInsight mediante .NET SDK.

## <a name="support-for-open-source-software"></a>Supporto per software open source

Il servizio Microsoft Azure HDInsight usa un ecosistema di tecnologie open source ispirate ad Apache Hadoop. Microsoft Azure offre un livello di supporto generale per le tecnologie open source. Per altre informazioni, vedere la sezione **Ambito del supporto** in [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/). Il servizio HDInsight offre un livello di supporto aggiuntivo per i componenti predefiniti.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

* **Componenti predefiniti**. Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. A questa categoria appartengono i componenti seguenti:

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
  * Il linguaggio di query Hive [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/).

    Un elenco completo dei componenti del cluster è disponibile in [Componenti e versioni di Apache Hadoop disponibili in HDInsight.](hdinsight-component-versioning.md)

* **Componenti personalizzati**. Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato da lui stesso.

> [!WARNING]  
> I componenti forniti con il cluster HDInsight sono completamente supportati. Il supporto tecnico Microsoft aiuta a isolare e risolvere i problemi legati a tali componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che il supporto tecnico Microsoft sia in grado di risolvere il problema oppure che richieda di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. Possono essere usati molti siti di community. Ad esempio, il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e [Stack Overflow](https://stackoverflow.com).
>
> Anche per i progetti Apache sono disponibili siti specifici nel [sito Web di Apache](https://apache.org). Ad esempio, [Hadoop](https://hadoop.apache.org/).

Il servizio HDInsight permette di usare i componenti personalizzati in molti modi. Indipendentemente dal modo in cui un componente viene usato o installato nel cluster, verrà applicato lo stesso livello di supporto. L'elenco seguente illustra i modi più comuni per usare i componenti personalizzati nei cluster HDInsight:

1. **Invio di lavori**. È possibile inviare al cluster processi Hadoop o di altro tipo che eseguono o usano componenti personalizzati.

2. **Personalizzazione del cluster**. Durante la creazione di un cluster, è possibile specificare impostazioni aggiuntive e componenti personalizzati, che verranno installati nei nodi del cluster.

3. **Esempi**. Microsoft e altri utenti possono fornire esempi relativi all'uso dei componenti personalizzati più diffusi nei cluster HDInsight. Questi esempi vengono forniti senza supporto.

## <a name="troubleshooting"></a>Risoluzione dei problemi

È possibile usare l'interfaccia utente Web Ambari per visualizzare le informazioni registrate dalle azioni script. Se lo script ha esito negativo durante la creazione del cluster, i log sono disponibili anche nell'account di archiviazione predefinito associato al cluster. Questa sezione include informazioni su come recuperare i log usando entrambe le opzioni.

### <a name="the-apache-ambari-web-ui"></a>Interfaccia utente Web di Apache Ambari

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net`passare `CLUSTERNAME` a , dove è il nome del cluster.

1. Nella barra nella parte superiore della pagina fare clic sulla voce **ops**. Viene visualizzato un elenco delle operazioni correnti e precedenti eseguite nel cluster tramite Ambari.

    ![Barra nell'interfaccia utente di Ambari con selezionato ops](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

1. Trovare le voci con **run\_customscriptaction** nella colonna **Operations**. Queste voci vengono create quando si eseguono le azioni script.

    ![Operazioni di azione script Apache Ambari](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Selezionare la voce **run\customscriptaction** ed eseguire il drill-down dei collegamenti per visualizzare l'output di **STDOUT** e **STDERR**. Questo output viene generato all'esecuzione dello script e potrebbe contenere informazioni utili.

### <a name="access-logs-from-the-default-storage-account"></a>Accesso ai log dall'account di archiviazione predefinito

Se la creazione del cluster non riesce a causa di un errore nello script, i log vengono conservati nell'account di archiviazione cluster.

* I log di archiviazione sono disponibili in `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Registri azioni script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    In questa directory i log sono organizzati per **nodi head**, **nodi di lavoro** e **nodi Zookeeper**. Vedere gli esempi seguenti:

    * **Nodo Head**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Nodo lavoratore**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Nodo di guardiano dello zoo**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Tutti i file **stdout** e **stderr** dell'host corrispondente vengono caricati nell'account di archiviazione. Per ogni azione script esiste un file **output-\*.txt** e un file **errors-\*.txt**. Il file **output-*.txt** contiene informazioni relative all'URI dello script che è stato eseguito nell'host. Il testo seguente è un esempio di queste informazioni:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* È possibile creare più volte un cluster dell'azione script con lo stesso nome. In questo caso, è possibile distinguere i log corrispondenti in base al nome della cartella **DATE**. Ad esempio, la struttura di cartelle per un cluster, **mycluster**, creato in diverse date sarà simile alle voci di log seguenti:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se in uno stesso giorno si creano più cluster dell'azione script con lo stesso nome, è possibile usare il prefisso univoco per identificare i file di registro corrispondenti.

* Se si crea un cluster verso le 00.00 (mezzanotte), è possibile che i file di log si estendano su due giorni. In questo caso, per lo stesso cluster vengono visualizzate due diverse cartelle della data.

* Il caricamento dei file di log nel contenitore predefinito può richiedere fino a cinque minuti, soprattutto per i cluster di grandi dimensioni. Se si vuole accedere ai log, quindi, è opportuno non eliminare immediatamente il cluster in caso di esito negativo di un'azione script.

### <a name="ambari-watchdog"></a>Watchdog Ambari

> [!WARNING]  
> Non modificare la password del watchdog Ambari (hdinsightwatchdog) nel cluster HDInsight basato su Linux. La modifica della password per questo account rende impossibile eseguire nuove azioni script nel cluster HDInsight.

### <a name="cant-import-name-blobservice"></a>Non è possibile importare il nome BlobService

__I sintomi__. l'azione script non riesce. Quando si visualizza l'operazione in Ambari, viene visualizzato un errore simile al seguente:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Causa__. Questo errore si verifica se si aggiorna il client di archiviazione di Azure Python incluso con il cluster HDInsight. HDInsight prevede l'uso della versione 0.20.0 del client di archiviazione di Azure.

__Risoluzione__. Per risolvere questo errore, connettersi manualmente a ogni nodo del cluster tramite `ssh`. Eseguire il comando seguente per reinstallare la versione del client di archiviazione corretta:

```bash
sudo pip install azure-storage==0.20.0
```

Per informazioni sulla connessione al cluster con SSH, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>La cronologia non mostra gli script usati durante la creazione di un cluster

Se il cluster è stato creato prima del 15 marzo 2016, potrebbe non essere visualizzata una voce nella cronologia delle azioni script. Ridimensionando il cluster, gli script verranno visualizzati nella cronologia delle azioni script.

Sussistono due eccezioni:

* Il cluster è stato creato prima del 1° settembre 2015. Le azioni script sono state introdotte in questa data. Per i cluster creati prima di tale data non possono quindi essere state usate le azioni script.

* Sono state usate più azioni script durante la creazione del cluster. Oppure è stato usato lo stesso nome per più script oppure lo stesso nome e lo stesso URI ma parametri diversi per più script. In questi casi si riceve l'errore seguente:

    Non è possibile eseguire nuove azioni script nel cluster a causa di nomi di script in conflitto negli script esistenti. I nomi di script specificati durante la creazione del cluster devono essere tutti univoci. Gli script esistenti vengono eseguiti durante il ridimensionamento.

## <a name="next-steps"></a>Passaggi successivi

* [Sviluppare script di azioni script per HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Add additional storage to an HDInsight cluster](hdinsight-hadoop-add-storage.md) (Aggiungere altra memoria a un cluster HDInsight)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Fasi durante la creazione di un cluster"
