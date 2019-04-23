---
title: Eseguire la migrazione a un accesso granulare basata sui ruoli per le configurazioni di cluster - Azure HDInsight
description: Scopri le modifiche necessarie per eseguire la migrazione a un accesso granulare basata sui ruoli per le configurazioni del cluster.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0422d848ccdf9ba82e68813de64eec863ee4ad29
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006234"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Eseguire la migrazione a un accesso granulare basata sui ruoli per le configurazioni di cluster

Sono state introdotte alcune importanti modifiche per supportare ulteriori accessi con granularità fine in base al ruolo per ottenere informazioni riservate. Come parte di queste modifiche, alcune **azione potrebbe essere necessaria** se si usa uno delle [interessate entità/scenari](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Cosa cambierà

In precedenza, i segreti è stato possibile ottenere tramite l'API di HDInsight da parte degli utenti cluster che presenta il proprietario, collaboratore o lettore [ruoli RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
In futuro, questi segreti non saranno accessibili agli utenti con ruolo di lettore. Inoltre, Microsoft è possibile introdurre un nuovo ruolo 'Operatore del Cluster Hdinsight' che è in grado di recuperare i segreti senza disporre delle autorizzazioni amministrative di proprietario o collaboratore. Per riepilogare:

| Ruolo                                  | In precedenza                                                                                       | Now       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Reader                                | -Accesso in lettura, inclusi i segreti                                                                   | -Accesso in lettura, **esclusione** segreti |           |   |   |
| Operatore del Cluster HDInsight<br>(Nuovo ruolo) | N/D                                                                                              | : Accesso lettura/scrittura, inclusi i segreti         |   |   |
| Collaboratore                           | : Accesso lettura/scrittura, inclusi i segreti<br>: Consente di creare e gestire tutti i tipi di risorse di Azure.     | Nessuna modifica |
| Proprietario                                 | -Accesso in lettura/scrittura inclusi i segreti<br>-Accesso completo a tutte le risorse<br>-Delegare l'accesso ad altri utenti | Nessuna modifica |

Per informazioni su come aggiungere l'assegnazione del ruolo operatore del Cluster HDInsight a un utente per concedere loro l'accesso in lettura/scrittura per i segreti di cluster, vedere la seguente sezione: [aggiungere l'assegnazione del ruolo operatore del Cluster HDInsight a un utente](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Sono interessato da queste modifiche?

Sono interessati le entità e gli scenari seguenti:

- [API](#api): Gli utenti che usano il `/configurations` o `/configurations/{configurationName}` gli endpoint.
- [Gli strumenti di Azure HDInsight per Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versione _ e versioni precedenti.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) versione _ e versioni precedenti.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) versione _ e versioni precedenti.
- [SDK per .NET](#sdk-for-net)
    - [le versioni 1.x o 2.x](#versions-1x-and-2x): Gli utenti che usano il `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` o `DisableHttp` metodi della classe ConfigurationsOperationsExtensions.
    - [le versioni 3.x e successive](#versions-3x-and-up): Gli utenti che usano il `EnableHttp`, `DisableHttp`, `Update`, o `Get` metodi dal `ConfigurationsOperationsExtensions` classe.
- [SDK per Python](#sdk-for-python): Gli utenti che usano il `get` o `update` metodi della classe ConfigurationsOperations.
- [SDK per Java](#sdk-for-java): Gli utenti che usano il `update` o `get` metodi della classe ConfigurationsInner.
- [SDK per Go](#sdk-for-go): Gli utenti che usano il `Get` o `Update` metodi da struct ConfigurationsClient.

Vedere le sezioni seguenti (o usare i collegamenti riportati sopra) per verificare la migrazione i passaggi per il proprio scenario.

### <a name="api"></a>API

Le API seguenti verranno modificate o deprecate:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (le informazioni riservate rimosse) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - In precedenza usato per ottenere i tipi di configurazione singoli (inclusi i segreti).
    - Questa chiamata API restituiranno ora i tipi di singola configurazione con segreti omesso. Per ottenere tutte le configurazioni, inclusi i segreti, usare le nuove [/configurations POST]() chiamare. Per ottenere solo le impostazioni del gateway, usare le nuove [/getGatewaySettings POST]() chiamare.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (deprecata) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Usato in precedenza per ottenere tutte le configurazioni (inclusi i segreti)
    - Questa chiamata all'API non sarà più supportata. Per ottenere tutte le configurazioni in futuro, usare le nuove [/configurations POST]() chiamare. Per ottenere le configurazioni con parametri sensibili omessi, usare il [/configurations/ GET {configurationName}]() chiamare.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (deprecata) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Usato in precedenza per aggiornare credenziali del gateway.
    - Questa chiamata API verrà deprecata e non è più supportata. Usare le nuove [/updateGatewaySettings POST]() invece.

La sostituzione seguente che sono state aggiunte API:</span>

- **/Configurations POST** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Usare questa API per ottenere tutte le configurazioni, inclusi i segreti.
- **POST /getGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/getGatewaySettings?api-version={api-version}
    - Usare questa API per ottenere le impostazioni del gateway.
- **/UpdateGatewaySettings POST** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/updateGatewaySettings?api-version={api-version}
    - Usare questa API per aggiornare le impostazioni del gateway (nome utente e/o password).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools per Visual Studio Code

Se si usa la versione 1.1.1 o meno recenti, eseguire l'aggiornamento al [versione più recente di Azure HDInsight Tools per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) per evitare interruzioni.

### <a name="azure-toolkit-for-intellij"></a>Toolkit di Azure per IntelliJ

Se si usa la versione 3.21.0 o versione precedente, eseguire l'aggiornamento al [versione più recente di Azure Toolkit for IntelliJ plug-in](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) nell'evitare interruzioni.

### <a name="azure-toolkit-for-eclipse"></a>Toolkit di Azure per Eclipse

Se si usa la versione 2019-03-29 o meno recenti, aggiornare la versione più recente di Azure Toolkit for Eclipse in modo da evitare interruzioni.

### <a name="sdk-for-net"></a>SDK per .NET

#### <a name="versions-1x-and-2x"></a>Le versioni 1.x e 2.x

Eseguire l'aggiornamento a [la versione 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) di HDInsight SDK per .NET. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- `ClusterOperationsExtensions.GetClusterConfigurations` verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare `ClusterOperationsExtensions.ListConfigurations` in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster.
    - Per recuperare solo le credenziali del gateway HTTP, usare `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` è ora deprecata ed è stata sostituita da `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` è ora deprecata ed è stata sostituita da `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` e `DisableHttp` ora sono deprecate. HTTP ora è sempre abilitata, in modo che questi metodi non sono più necessari.

#### <a name="versions-3x-and-up"></a>Le versioni 3.x e successive

Eseguire l'aggiornamento a [versione 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) di HDInsight SDK per .NET. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare `ConfigurationOperationsExtensions.List` in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) è ora deprecata ed è stata sostituita da `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) e [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) ora sono deprecate. HTTP ora è sempre abilitata, in modo che questi metodi non sono più necessari.

### <a name="sdk-for-python"></a>SDK per Python

Eseguire l'aggiornamento a [versione 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) di HDInsight SDK per Python. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) è ora deprecata ed è stata sostituita da [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK per Java

Eseguire l'aggiornamento a [versione 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) di HDInsight SDK per Java. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare `ConfigurationsInner.list` in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare `ConfigurationsOperations.get_gateway_settings`.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) è ora deprecata ed è stata sostituita da `ClusterOperationsExtensions.update_gateway_settings`.

### <a name="sdk-for-go"></a>SDK per Go

Eseguire l'aggiornamento a [versione 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) di HDInsight SDK per Go. Modifiche al codice minimo potrebbe essere necessari se si usa un metodo interessato da queste modifiche:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) è ora deprecata ed è stata sostituita da [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Aggiungere l'assegnazione del ruolo operatore del Cluster HDInsight a un utente

Un utente con il [Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) oppure [proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) ruolo può concedere il ruolo operatore del Cluster HDInsight per gli utenti che si desidera avere accesso in lettura/scrittura per i segreti di cluster HDInsight, ad esempio le credenziali di gateway del cluster e chiavi dell'account di archiviazione.

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Il modo più semplice per aggiungere questa assegnazione di ruolo consiste nell'usare il comando seguente nella finestra di comando di Azure:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

> [!NOTE]
> Questo comando deve essere eseguito da un utente con i ruoli di collaboratore o proprietario, come solo è possibile concedere queste autorizzazioni. Il `--assignee` è l'indirizzo di posta elettronica dell'utente a cui si desidera assegnare il ruolo operatore del Cluster HDInsight.

Il comando precedente concede a tale ruolo a livello di sottoscrizione. Per concedere invece solo questo ruolo a livello di gruppo di risorse, è possibile modificare il comando come segue:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

In alternativa, è possibile usare il portale di Azure per aggiungere l'assegnazione del ruolo operatore del Cluster HDInsight a un utente. Vedere la documentazione [gestire l'accesso alle risorse di Azure tramite RBAC e il portale di Azure - aggiungere un'assegnazione di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
