---
title: Personalizzare i cluster Azure HDInsight con azioni script
description: Aggiungere componenti personalizzati ai cluster HDInsight usando azioni script. Le azioni script sono script bash che possono essere usati per personalizzare la configurazione del cluster. In alternativa, aggiungere servizi e utilità aggiuntivi come Hue, Solr o R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 36aaee030dd5267a391dd9a235dd5f8dc0932fa0
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087092"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Personalizzare i cluster Azure HDInsight con azioni script

Azure HDInsight fornisce un metodo di configurazione denominato **azioni script** che richiama script personalizzati per personalizzare il cluster. Questi script vengono usati per installare i componenti aggiuntivi e modificare le impostazioni di configurazione. Le azioni script possono essere usate durante o dopo la creazione del cluster.

Le azioni script possono anche essere pubblicate in Azure Marketplace come applicazione HDInsight. Per altre informazioni sulle applicazioni HDInsight, vedere [Pubblicare un'applicazione HDInsight in Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Autorizzazioni

Per un cluster HDInsight aggiunto a un dominio, sono necessarie due autorizzazioni di Apache Ambari per l'uso di azioni script con il cluster:

* **AMBARI. ESEGUIRE \_ un \_ comando personalizzato**. il ruolo di amministratore di Ambari ha questa autorizzazione per impostazione predefinita.
* **Cluster. ESEGUIRE \_ un \_ comando personalizzato**. sia l'amministratore del cluster HDInsight che l'amministratore di Ambari hanno questa autorizzazione per impostazione predefinita.

Per altre informazioni sull'uso delle autorizzazioni con HDInsight aggiunto a un dominio, vedere [Gestire i cluster HDInsight con Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Controllo di accesso

Se non si è l'amministratore o il proprietario della sottoscrizione di Azure, l'account deve disporre almeno dell'accesso Collaboratore al gruppo di risorse contenente il cluster HDInsight.

Un utente con almeno l'accesso collaboratore alla sottoscrizione di Azure deve avere registrato in precedenza il provider. La registrazione del provider viene eseguita quando un utente con accesso collaboratore alla sottoscrizione crea una risorsa. Per senza creare una risorsa, vedere [registrare un provider tramite REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Ottenere altre informazioni sull'uso della gestione degli accessi:

* [Introduzione alla gestione degli accessi nel portale di Azure](../role-based-access-control/overview.md)
* [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Informazioni sulle azioni script

Un'azione di script è uno script Bash eseguito sui nodi di un cluster HDInsight. Di seguito sono riportate le caratteristiche e funzionalità delle azioni script:

* Devono essere archiviate in un URI accessibile dal cluster HDInsight. Di seguito vengono indicate alcune tra le posizioni di archiviazione possibili:

    * Per i cluster normali:

      * ADLS Gen1: l'entità servizio HDInsight USA per accedere Data Lake Storage deve avere accesso in lettura allo script. Il formato dell'URI per gli script archiviati in Data Lake Storage Gen1 è `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Un BLOB in un account di archiviazione di Azure che rappresenta l'account di archiviazione primario o aggiuntivo per il cluster HDInsight. HDInsight può accedere a entrambi i tipi di account di archiviazione durante la creazione del cluster.

        > [!IMPORTANT]  
        > Non ruotare la chiave di archiviazione in questo account di archiviazione di Azure, perché le azioni script successive con script archiviati in questa posizione avranno esito negativo.

      * Un servizio pubblico di condivisione file accessibile tramite percorsi http://. Esempi sono BLOB di Azure, GitHub, OneDrive. Per gli URI di esempio, vedere [Script di esempio di azione script](#example-script-action-scripts).

     * Per i cluster con ESP, gli URI wasb://o wasbs://o http [s]://sono supportati.

* Può essere limitato per l'esecuzione solo in determinati tipi di nodo, ad esempio i nodi head o i nodi di lavoro.

* Può essere reso permanente o `ad hoc` .

    Le azioni script persistenti devono avere un nome univoco. Gli script persistenti vengono usati per personalizzare i nuovi nodi di lavoro aggiunti al cluster tramite operazioni di ridimensionamento. Uno script persistente può anche applicare modifiche a un altro tipo di nodo durante operazioni di ridimensionamento, ad esempio un nodo head.

    `Ad hoc`gli script non sono salvati in permanenza. Le azioni script usate durante la creazione di un cluster vengono automaticamente rese persistenti. Non vengono infatti applicati ai nodi di lavoro aggiunti al cluster dopo l'esecuzione dello script. È quindi possibile innalzare `ad hoc` di livello uno script a uno script permanente o abbassare di livello uno script permanente in uno `ad hoc` script. Gli script che hanno esito negativo non vengono resi persistenti, anche in presenza di indicazioni specifiche in tal senso.

* Possono accettare parametri usati dallo script durante l'esecuzione.

* Eseguire lo script con privilegi a livello radice nei nodi del cluster.

* Può essere usato tramite l'portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o HDInsight .NET SDK.

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

È possibile eseguire operazioni come l'arresto e l'avvio dei servizi, inclusi i servizi correlati a Apache Hadoop. Se si arrestano i servizi, assicurarsi che Ambari e altri servizi correlati a Hadoop siano in esecuzione prima del completamento dello script. Questi servizi necessari determinano l'integrità e lo stato del cluster durante la creazione.

Durante la creazione del cluster, è possibile usare più azioni script alla volta. Questi script vengono richiamati nell'ordine in cui sono stati specificati.

> [!IMPORTANT]  
> È necessario completare le azioni script entro 60 minuti. Durante il provisioning del cluster, lo script viene eseguito contemporaneamente ad altri processi di installazione e configurazione. In caso di concorrenza per risorse come il tempo di CPU o la larghezza di banda di rete, lo script potrebbe richiedere più tempo per completare l'operazione rispetto al tempo che impiegherebbe in un ambiente di sviluppo.
>
> Per ridurre al minimo il tempo necessario per eseguire lo script, evitare attività come il download e la compilazione di applicazioni dall'origine. Precompilare le applicazioni e archiviare i file binari in Archiviazione di Azure.

### <a name="script-action-on-a-running-cluster"></a>Azione script in un cluster in esecuzione

Un errore di script in un cluster già in esecuzione non determina automaticamente il passaggio del cluster a uno stato di errore. Al termine di uno script, il cluster deve tornare a uno stato In esecuzione. Anche se lo stato del cluster è In esecuzione, lo script non riuscito potrebbe includere attività interrotte. Ad esempio, uno script potrebbe eliminare file richiesti dal cluster.

Le azioni script vengono eseguite con privilegi a livello radice. Assicurarsi di comprendere il funzionamento di uno script prima di applicarlo al cluster.

Quando si applica uno script a un cluster, lo stato del cluster passa da **In esecuzione** ad **Accettato**. Quindi passa a **Configurazione di HDInsight** e infine di nuovo a **In esecuzione** per gli script con esito positivo. Lo stato dello script viene registrato nella cronologia delle azioni script. Queste informazioni indicano se lo script ha avuto esito positivo o negativo. Ad esempio, il cmdlet di PowerShell `Get-AzHDInsightScriptActionHistory` mostra lo stato di uno script. Verranno restituite informazioni simili al testo seguente:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

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

## <a name="script-action-during-cluster-creation"></a>Azione script durante la creazione del cluster

In questa sezione vengono illustrate le diverse modalità d'uso delle azioni script durante la creazione di un cluster HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Usare un'azione script durante la creazione di un cluster dal portale di Azure

1. Iniziare a creare un cluster come descritto in [creare cluster basati su Linux in HDInsight usando il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Dalla scheda **configurazione e prezzi** selezionare **+ Aggiungi azione script**.

    ![Azione di script di portale di Azure cluster](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Usare la voce __Seleziona uno script__ per selezionare uno script pronto. Per usare uno script personalizzato, selezionare __Personalizzato__. Specificare quindi __Nome__ e __URI script Bash__ per lo script.

    ![Aggiungere uno script nel modulo di selezione script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    La tabella seguente illustra gli elementi nel modulo:

    | Proprietà | valore |
    | --- | --- |
    | Selezionare uno script | Per usare uno script personalizzato, selezionare __personalizzato__. In caso contrario, selezionare uno degli script disponibili. |
    | Nome |Specificare un nome per l'azione script. |
    | URI script Bash |Specificare l'URI dello script. |
    | Head/Worker/ZooKeeper |Specificare i nodi in cui viene eseguito lo script: **Head**, **Worker**o **ZooKeeper**. |
    | Parametri |Specificare i parametri, se richiesti dallo script. |

    Usare la voce __Salvare questa azione script in modo permanente__ per assicurarsi che lo script venga applicato durante le operazioni di ridimensionamento.

1. Selezionare __Crea__ per salvare lo script. È quindi possibile usare __+ Invia nuovo__ per aggiungere un altro script.

    ![HDInsight di più azioni script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Al termine dell'aggiunta degli script, tornare alla scheda **configurazione e prezzi** .

1. Completare i passaggi di creazione del cluster rimanenti come di consueto.

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

In questa sezione si userà il cmdlet [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) per richiamare gli script per personalizzare un cluster. Prima di iniziare, assicurarsi di installare e configurare Azure PowerShell. Per usare questi comandi di PowerShell, è necessario il [modulo AZ](https://docs.microsoft.com/powershell/azure/overview).

Lo script seguente mostra come applicare un'azione script durante la creazione di un cluster con PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

La creazione del cluster può richiedere alcuni minuti.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Usare un'azione script durante la creazione di un cluster da HDInsight .NET SDK

HDInsight .NET SDK fornisce librerie client che semplificano l'uso di HDInsight da un'applicazione .NET. Per un esempio di codice, vedere [azioni script](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Azione script in un cluster in esecuzione

Questa sezione illustra come applicare azioni script a un cluster in esecuzione.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Applicare un'azione script a un cluster in esecuzione dal portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e individuare il cluster.

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
    | Head/Worker/Zookeeper |Specificare i nodi in cui viene eseguito lo script: **Head**, **Worker**o **ZooKeeper**. |
    | Parametri |Specificare i parametri, se richiesti dallo script. |

    Usare la voce __Salvare questa azione script in modo permanente__ per assicurarsi che lo script venga applicato durante le operazioni di ridimensionamento.

1. Infine, selezionare il pulsante **Crea** per applicare lo script al cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Applicare un'azione script a un cluster in esecuzione da Azure PowerShell

Per usare questi comandi di PowerShell, è necessario il [modulo AZ](https://docs.microsoft.com/powershell/azure/overview). L'esempio seguente mostra come applicare un'azione script a un cluster in esecuzione:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Al termine dell'operazione, vengono visualizzate informazioni simili alle seguenti:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Applicare un'azione script a un cluster in esecuzione dall'interfaccia della riga di comando di Azure

Prima di iniziare, assicurarsi di installare e configurare l'interfaccia della riga di comando di Azure. Verificare di avere l'ultima versione. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Eseguire l'autenticazione alla sottoscrizione di Azure:

    ```azurecli
    az login
    ```

1. Applicare un'azione script a un cluster in esecuzione:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    I ruoli validi sono `headnode` ,, `workernode` `zookeepernode` , `edgenode` . Se lo script deve essere applicato a diversi tipi di nodo, separare i ruoli in base a uno spazio. Ad esempio: `--roles headnode workernode`.

    Per salvare lo script in modo permanente, aggiungere `--persist-on-success`. È anche possibile salvare lo script in modo permanente in un secondo momento usando `az hdinsight script-action promote`.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Applicare un'azione script a un cluster in esecuzione usando l'API REST

Vedere [Cluster REST API in Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx) (API REST Cluster in Azure HDInsight).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Applicare un'azione script a un cluster in esecuzione da HDInsight .NET SDK

Per un esempio dell'uso di .NET SDK per applicare script a un cluster, vedere [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Applicare un'azione script su un cluster HDInsight basato su Linux in esecuzione).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Visualizzare la cronologia e alzare e abbassare di livello le azioni script

### <a name="the-azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e individuare il cluster.

1. Nella visualizzazione predefinita, in **Impostazioni**, selezionare **Azioni script**.

1. Una cronologia degli script per il cluster viene visualizzata nella sezione Azioni script. Queste informazioni includono un elenco degli script persistenti. Lo screenshot seguente mostra che è stato eseguito lo script Solr nel cluster. Lo screenshot non mostra script persistenti.

    ![Cronologia di invio delle azioni script del portale](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Selezionare uno script nella cronologia per visualizzare la sezione **Proprietà** per lo script. Nella parte superiore della schermata è possibile eseguire di nuovo lo script o alzarlo di livello.

    ![Innalzamento di livello delle proprietà delle azioni script](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. È anche possibile selezionare i puntini di sospensione ( **...**) a destra delle voci nella sezione azioni script per eseguire le azioni.

    ![Eliminazione di azioni script permanente](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet | Funzione |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Recuperare informazioni sulle azioni script persistenti. Questo cmdlet non annulla le azioni eseguite da uno script, ma rimuove solo il flag persistente.|
| `Get-AzHDInsightScriptActionHistory` |Recuperare una cronologia delle azioni script applicate al cluster o i dettagli di uno script specifico. |
| `Set-AzHDInsightPersistedScriptAction` |Innalzare `ad hoc` di livello un'azione script a un'azione script permanente. |
| `Remove-AzHDInsightPersistedScriptAction` |Abbassare di livello un'azione script permanente in un' `ad hoc` azione. |

Lo script di esempio seguente mostra come usare i cmdlet per alzare di livello e poi abbassare di livello uno script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

| Comando | Descrizione |
| --- | --- |
| [`az hdinsight script-action delete`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Elimina un'azione script permanente specificata del cluster. Questo comando non annulla le azioni eseguite da uno script, ma rimuove solo il flag persistente.|
|[`az hdinsight script-action execute`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Eseguire azioni script sul cluster HDInsight specificato.|
| [`az hdinsight script-action list`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Elenca tutte le azioni script permanente per il cluster specificato. |
|[`az hdinsight script-action list-execution-history`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Elenca la cronologia di esecuzione di tutti gli script per il cluster specificato.|
|[`az hdinsight script-action promote`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Promuove l'esecuzione di script ad hoc specificata in uno script permanente.|
|[`az hdinsight script-action show-execution-details`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Ottiene i dettagli di esecuzione dello script per l'ID esecuzione dello script specificato.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Per un esempio relativo all'uso di .NET SDK per recuperare la cronologia degli script da un cluster e alzare o abbassare di livello gli script, vedere [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Applicare un'azione script su un cluster HDInsight basato su Linux in esecuzione).

> [!NOTE]  
> Questo esempio mostra anche come installare un'applicazione HDInsight mediante .NET SDK.

## <a name="next-steps"></a>Passaggi successivi

* [Sviluppare script di azioni script per HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Add additional storage to an HDInsight cluster](hdinsight-hadoop-add-storage.md) (Aggiungere altra memoria a un cluster HDInsight)
* [Risolvere i problemi delle azioni script](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Fasi durante la creazione di un cluster"
