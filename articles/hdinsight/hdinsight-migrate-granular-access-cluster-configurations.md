---
title: Eseguire la migrazione a un accesso granulare basata sui ruoli per le configurazioni di cluster - Azure HDInsight
description: Scopri le modifiche necessarie come parte della migrazione a un accesso granulare basata sui ruoli per le configurazioni di cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1ec4786291d6e2e5be6785e52cf3ab5bb5bbc690
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754546"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Eseguire la migrazione all'accesso granulare in base al ruolo per le configurazioni dei cluster

Sono state introdotte alcune importanti modifiche per supportare ulteriori accessi con granularità fine in base al ruolo per ottenere informazioni riservate. Come parte di queste modifiche, alcune **azione potrebbe essere necessaria** se si usa uno delle [interessate entità/scenari](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Cosa cambierà

In precedenza, i segreti è stato possibile ottenere tramite l'API di HDInsight da parte degli utenti cluster che presenta il proprietario, collaboratore o lettore [ruoli RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles), come fossero disponibili per chiunque abbia il `*/read` è necessaria l'autorizzazione.
In futuro, l'accesso a questi segreti richiederanno la `Microsoft.HDInsight/clusters/configurations/*` autorizzazione, vale a dire non siano accessibili da parte degli utenti con ruolo di lettore. I segreti siano definiti come devono consentire valori che può essere usati per ottenere l'accesso con privilegi più elevato rispetto a un ruolo utente. Sono inclusi i valori, ad esempio cluster gateway HTTP credenziali, chiavi dell'account di archiviazione e le credenziali del database.

È stato anche introdotto un nuovo [operatore del Cluster Hdinsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) ruolo che sarà in grado di recuperare i segreti senza disporre delle autorizzazioni amministrative di proprietario o collaboratore. Per riepilogare:

| Ruolo                                  | In precedenza                                                                                       | In futuro       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Reader                                | -Accesso in lettura, inclusi i segreti                                                                   | -Accesso in lettura, **esclusione** segreti |           |   |   |
| Operatore del Cluster HDInsight<br>(Nuovo ruolo) | N/D                                                                                              | : Accesso lettura/scrittura, inclusi i segreti         |   |   |
| Collaboratore                           | : Accesso lettura/scrittura, inclusi i segreti<br>: Consente di creare e gestire tutti i tipi di risorse di Azure.     | Nessuna modifica |
| Proprietario                                 | -Accesso in lettura/scrittura inclusi i segreti<br>-Accesso completo a tutte le risorse<br>-Delegare l'accesso ad altri utenti | Nessuna modifica |

Per informazioni su come aggiungere l'assegnazione del ruolo operatore del Cluster HDInsight a un utente per concedere loro l'accesso in lettura/scrittura per i segreti di cluster, vedere la seguente sezione: [aggiungere l'assegnazione del ruolo operatore del Cluster HDInsight a un utente](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Sono interessato da queste modifiche?

Sono interessati le entità e gli scenari seguenti:

- [API](#api): Gli utenti che usano il `/configurations` o `/configurations/{configurationName}` gli endpoint.
- [Gli strumenti di Azure HDInsight per Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versione 1.1.1 o versione precedente.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) versione 3.20.0 o versione precedente.
- [Azure Data Lake e Stream Analitica Tools per Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) versione 2.3.9000.1 precedente.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) versione 3.15.0 o versione precedente.
- [SDK per .NET](#sdk-for-net)
    - [le versioni 1.x o 2.x](#versions-1x-and-2x): Gli utenti che usano il `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` o `DisableHttp` metodi della classe ConfigurationsOperationsExtensions.
    - [le versioni 3.x e successive](#versions-3x-and-up): Gli utenti che usano il `Get`, `Update`, `EnableHttp`, o `DisableHttp` metodi dal `ConfigurationsOperationsExtensions` classe.
- [SDK per Python](#sdk-for-python): Gli utenti che usano il `get` oppure `update` metodi dal `ConfigurationsOperations` classe.
- [SDK per Java](#sdk-for-java): Gli utenti che usano il `update` oppure `get` metodi dal `ConfigurationsInner` classe.
- [SDK per Go](#sdk-for-go): Gli utenti che usano il `Get` oppure `Update` metodi dal `ConfigurationsClient` struct.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) precedente versione 2.0.0.
Per informazioni sulla procedura di migrazione per il tuo scenario, vedi le sezioni seguenti o usa i collegamenti indicati in precedenza.

### <a name="api"></a>API

Le API seguenti verranno modificate o deprecate:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (le informazioni riservate rimosse)
    - In precedenza usato per ottenere i tipi di configurazione singoli (inclusi i segreti).
    - Questa chiamata API restituiranno ora i tipi di singola configurazione con segreti omesso. Per ottenere tutte le configurazioni, inclusi i segreti, usare la nuova chiamata /configurations POST. Per ottenere solo le impostazioni del gateway, usare la nuova chiamata /getGatewaySettings POST.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (deprecata)
    - Usato in precedenza per ottenere tutte le configurazioni (inclusi i segreti)
    - Questa chiamata all'API non sarà più supportata. Per ottenere tutte le configurazioni in futuro, usare la nuova chiamata /configurations POST. Per ottenere le configurazioni con parametri sensibili omessi, usare la chiamata di /configurations/ {configurationName} GET.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (deprecata)
    - Usato in precedenza per aggiornare credenziali del gateway.
    - Questa chiamata API verrà deprecata e non è più supportata. In alternativa, usare il nuovo /updateGatewaySettings POST.

La sostituzione seguente che sono state aggiunte API:</span>

- [ **/Configurations POST**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Usare questa API per ottenere tutte le configurazioni, inclusi i segreti.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Usare questa API per ottenere le impostazioni del gateway.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Usare questa API per aggiornare le impostazioni del gateway (nome utente e/o password).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools per Visual Studio Code

Se si usa la versione 1.1.1 o aggiornamento di seguito per il [versione più recente di Azure HDInsight Tools per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) per evitare interruzioni.

### <a name="azure-toolkit-for-intellij"></a>Toolkit di Azure per IntelliJ

Se si usa la versione 3.20.0 o aggiornamento di seguito per il [versione più recente di Azure Toolkit for IntelliJ plugin](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) per evitare interruzioni.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake e Stream Analitica Tools per Visual Studio

Aggiornamento alla versione 2.3.9000.1 dello o versione successiva [Stream Analitica Tools per Visual Studio e Azure Data Lake](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) per evitare interruzioni.  Per informazioni sull'aggiornamento, vedere la documentazione [Update Data Lake Tools per Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Toolkit di Azure per Eclipse

Se si usa la versione 3.15.0 o aggiornamento di seguito per il [versione più recente di Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) per evitare interruzioni.

### <a name="sdk-for-net"></a>SDK per .NET

#### <a name="versions-1x-and-2x"></a>Le versioni 1.x e 2.x

Aggiornamento a [la versione 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) di HDInsight SDK per .NET. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- `ClusterOperationsExtensions.GetClusterConfigurations` verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare `ClusterOperationsExtensions.ListConfigurations` in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster.
    - Per recuperare solo le credenziali del gateway HTTP, usare `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` è ora deprecata ed è stata sostituita da `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` è ora deprecata ed è stata sostituita da `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` e `DisableHttp` ora sono deprecate. HTTP ora è sempre abilitata, in modo che questi metodi non sono più necessari.

#### <a name="versions-3x-and-up"></a>Le versioni 3.x e successive

Aggiornamento a [versione 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) o versioni successive del SDK di HDInsight per .NET. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) è ora deprecata ed è stata sostituita da [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) e [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) ora sono deprecate. HTTP ora è sempre abilitata, in modo che questi metodi non sono più necessari.

### <a name="sdk-for-python"></a>SDK per Python

Aggiornamento a [versione 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) o versione successiva di HDInsight SDK per Python. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) è ora deprecata ed è stata sostituita da [ `ClusterOperations.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK per Java

Aggiornamento a [versione 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) o versione successiva di HDInsight SDK per Java. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [ `ClustersInner.getGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) è ora deprecata ed è stata sostituita da [ `ClustersInner.updateGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK per Go

Aggiornamento a [versione 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) o versione successiva di HDInsight SDK per Go. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) è ora deprecata ed è stata sostituita da [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Aggiornamento a [Az PowerShell versione 2.0.0](https://www.powershellgallery.com/packages/Az) o versione successiva per evitare interruzioni.  Se si usa un metodo interessato da queste modifiche, possono essere necessarie modifiche di codice minima.
- `Grant-AzHDInsightHttpServicesAccess` è ora deprecata ed è stata sostituita dalla nuova `Set-AzHDInsightGatewayCredential` cmdlet.
- `Get-AzHDInsightJobOutput` è stato aggiornato per supportare l'accesso in base al ruolo granulare per la chiave di archiviazione.
    - Gli utenti con ruolo di collaboratore, proprietario oppure operatore cluster HDInsight non sono interessati.
    - Saranno necessario specificare gli utenti che dispongono solo il ruolo Lettore di `DefaultStorageAccountKey` parametro in modo esplicito.
- `Revoke-AzHDInsightHttpServicesAccess` è ora deprecata. HTTP ora è sempre abilitata, in modo che questo cmdlet non è più necessario.
 Vedere il [az. Guida alla migrazione HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) per altri dettagli.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Aggiungere l'assegnazione del ruolo operatore del Cluster HDInsight a un utente

Un utente con il [collaboratori](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) o [proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) ruolo può assegnare il [operatore del Cluster Hdinsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) ruolo agli utenti che si abbiano accesso in lettura/scrittura alla distinzione Valori di configurazione del cluster HDInsight (ad esempio le credenziali del cluster gateway e chiavi dell'account di archiviazione).

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Il modo più semplice per aggiungere questa assegnazione di ruolo consiste nell'usare il `az role assignemnt create` comando CLI di Azure.

> [!NOTE]
> Questo comando deve essere eseguito da un utente con i ruoli di collaboratore o proprietario, come solo è possibile concedere queste autorizzazioni. Il `--assignee` è l'indirizzo di posta elettronica dell'utente a cui si desidera assegnare il ruolo operatore del Cluster HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Concedere il ruolo a livello di risorse (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Concedere il ruolo a livello di gruppo di risorse

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Concedere il ruolo a livello di sottoscrizione

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

In alternativa, è possibile usare il portale di Azure per aggiungere l'assegnazione del ruolo operatore del Cluster HDInsight a un utente. Vedere la documentazione [gestire l'accesso alle risorse di Azure tramite RBAC e il portale di Azure - aggiungere un'assegnazione di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
