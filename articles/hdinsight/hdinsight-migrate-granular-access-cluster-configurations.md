---
title: Configurazioni granulari di accesso in base al ruolo di Azure HDInsight cluster
description: Informazioni sulle modifiche necessarie nell'ambito della migrazione a un accesso granulare basato sui ruoli per le configurazioni del cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: ea8e1565a5ebe4e5cb40049fbfcb329feb83bdda
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498200"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Eseguire la migrazione all'accesso granulare in base al ruolo per le configurazioni dei cluster

Sono state introdotte alcune modifiche importanti per supportare l'accesso in base al ruolo con granularità fine per ottenere informazioni riservate. Come parte di queste modifiche, è possibile che venga richiesta un'azione **entro il 3 settembre 2019** se si utilizza una delle [entità/scenari interessati](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Cosa cambierà

In precedenza, i segreti potevano essere ottenuti tramite l'API HDInsight dagli utenti del cluster che possedevano i ruoli di proprietario, collaboratore o controllo degli accessi in base al [ruolo](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)di lettore, perché erano disponibili per chiunque disponga dell'autorizzazione `*/read`. I segreti sono definiti come valori che possono essere usati per ottenere un accesso più elevato rispetto a quello consentito da un utente. Sono inclusi valori come le credenziali HTTP del gateway cluster, le chiavi dell'account di archiviazione e le credenziali del database.

A partire dal 3 settembre 2019, per accedere a questi segreti sarà necessaria l'autorizzazione `Microsoft.HDInsight/clusters/configurations/action`, ovvero non sarà più possibile accedervi dagli utenti con il ruolo lettore. I ruoli che dispongono di questa autorizzazione sono collaboratore, proprietario e nuovo ruolo Operatore cluster HDInsight (altre informazioni sono disponibili di seguito).

Viene inoltre introdotto un nuovo ruolo [operatore cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) che sarà in grado di recuperare i segreti senza concedere le autorizzazioni amministrative di collaboratore o proprietario. Per riepilogare:

| Ruolo                                  | Precedentemente                                                                                       | In futuro       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Lettore                                | -Accesso in lettura, inclusi i segreti                                                                   | -Accesso in lettura, **esclusi** i segreti |           |   |   |
| Operatore cluster HDInsight<br>(Nuovo ruolo) | N/D                                                                                              | -Accesso in lettura/scrittura, inclusi i segreti         |   |   |
| Collaboratore                           | -Accesso in lettura/scrittura, inclusi i segreti<br>-Creare e gestire tutti i tipi di risorse di Azure.     | Nessuna modifica |
| Proprietario                                 | -Accesso in lettura/scrittura, inclusi i segreti<br>-Accesso completo a tutte le risorse<br>-Delegare l'accesso ad altri utenti | Nessuna modifica |

Per informazioni su come aggiungere l'assegnazione di ruolo Operatore cluster HDInsight a un utente per concedere loro l'accesso in lettura/scrittura ai segreti del cluster, vedere la sezione seguente [aggiungere l'assegnazione di ruolo Operatore cluster HDInsight a un utente](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Sono interessati da queste modifiche?

Le entità e gli scenari seguenti sono interessati:

- [API](#api): utenti che usano gli endpoint `/configurations` o `/configurations/{configurationName}`.
- [Strumenti di Azure HDInsight per Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versione 1.1.1 o successiva.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) versione 3.20.0 o successiva.
- [Strumenti di analisi di flusso e Azure Data Lake per Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) versione 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) versione 3.15.0 o successiva.
- [SDK per .NET](#sdk-for-net)
    - [versioni 1. x o 2. x](#versions-1x-and-2x): gli utenti che usano i metodi `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` o `DisableHttp` dalla classe ConfigurationsOperationsExtensions.
    - [versioni 3. x e versioni precedenti](#versions-3x-and-up): gli utenti che usano i metodi `Get`, `Update`, `EnableHttp`o `DisableHttp` dalla classe `ConfigurationsOperationsExtensions`.
- [SDK per Python](#sdk-for-python): gli utenti che usano i metodi `get` o `update` della classe `ConfigurationsOperations`.
- [SDK per Java](#sdk-for-java): gli utenti che usano i metodi `update` o `get` della classe `ConfigurationsInner`.
- [SDK per go](#sdk-for-go): gli utenti che usano i metodi `Get` o `Update` dello struct `ConfigurationsClient`.
- [AZ. HDInsight PowerShell](#azhdinsight-powershell) sotto la versione 2.0.0.
Per informazioni sulla procedura di migrazione per il tuo scenario, vedi le sezioni seguenti o usa i collegamenti indicati in precedenza.

### <a name="api"></a>API

Le seguenti API verranno modificate o deprecate:

- [**Get/Configurations/{ConfigurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informazioni riservate rimosse)
    - Usato in precedenza per ottenere i singoli tipi di configurazione (inclusi i segreti).
    - A partire dal 3 settembre 2019, questa chiamata API restituirà i singoli tipi di configurazione con segreti omessi. Per ottenere tutte le configurazioni, inclusi i segreti, usare la nuova chiamata POST/Configurations. Per ottenere solo le impostazioni del gateway, usare la nuova chiamata POST/getGatewaySettings.
- [**Get/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (deprecato)
    - Usato in precedenza per ottenere tutte le configurazioni (inclusi i segreti)
    - A partire dal 3 settembre 2019, questa chiamata API verrà deprecata e non sarà più supportata. Per ottenere tutte le configurazioni in futuro, usare la nuova chiamata POST/Configurations. Per ottenere le configurazioni con parametri sensibili omessi, usare la chiamata GET/configurations/{configurationName}.
- [**Post/Configurations/{ConfigurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (deprecato)
    - Usato in precedenza per aggiornare le credenziali del gateway.
    - A partire dal 3 settembre 2019, questa chiamata API verrà deprecata e non sarà più supportata. Usare invece il nuovo POST/updateGatewaySettings.

Sono state aggiunte le seguenti API sostitutive:</span>

- [**POST/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Usare questa API per ottenere tutte le configurazioni, inclusi i segreti.
- [**POST/getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Usare questa API per ottenere le impostazioni del gateway.
- [**POST/updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Usare questa API per aggiornare le impostazioni del gateway (nome utente e/o password).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Strumenti di Azure HDInsight per Visual Studio Code

Se si usa la versione 1.1.1 o successiva, eseguire l'aggiornamento alla [versione più recente degli strumenti di Azure HDInsight per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) per evitare interruzioni.

### <a name="azure-toolkit-for-intellij"></a>Toolkit di Azure per IntelliJ

Se si usa la versione 3.20.0 o successiva, eseguire l'aggiornamento alla [versione più recente del plug](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) -in Azure Toolkit for IntelliJ per evitare interruzioni.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Strumenti di analisi di flusso e Azure Data Lake per Visual Studio

Eseguire l'aggiornamento alla versione 2.3.9000.1 o successiva di [Azure Data Lake e gli strumenti di analisi di flusso per Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) per evitare interruzioni.  Per informazioni sull'aggiornamento, vedere la documentazione, [aggiornare Data Lake Tools per Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Toolkit di Azure per Eclipse

Se si usa la versione 3.15.0 o successiva, eseguire l'aggiornamento alla [versione più recente del Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) per evitare interruzioni.

### <a name="sdk-for-net"></a>SDK per .NET

#### <a name="versions-1x-and-2x"></a>Versioni 1. x e 2. x

Eseguire l'aggiornamento alla [versione 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) di HDInsight SDK per .NET. Potrebbero essere necessarie modifiche minime al codice se si utilizza un metodo interessato da queste modifiche:

- `ClusterOperationsExtensions.GetClusterConfigurations` **non restituirà più parametri sensibili** come le chiavi di archiviazione (sito Core) o le credenziali http (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, usare `ClusterOperationsExtensions.ListConfigurations` in futuro.  Si noti che gli utenti con il ruolo "Reader" non saranno in grado di utilizzare questo metodo. Questo consente un controllo granulare sugli utenti che possono accedere alle informazioni riservate per un cluster.
    - Per recuperare solo le credenziali del gateway HTTP, usare `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` ora è deprecata ed è stata sostituita da `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` ora è deprecata ed è stata sostituita da `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` e `DisableHttp` sono ora deprecate. Il protocollo HTTP è ora sempre abilitato, quindi questi metodi non sono più necessari.

#### <a name="versions-3x-and-up"></a>Versioni 3. x e versioni precedenti

Eseguire l'aggiornamento alla [versione 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) o successiva di HDInsight SDK per .NET. Potrebbero essere necessarie modifiche minime al codice se si utilizza un metodo interessato da queste modifiche:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) **non restituirà più parametri sensibili** come le chiavi di archiviazione (sito Core) o le credenziali http (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, usare [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) in futuro.  Si noti che gli utenti con il ruolo "Reader" non saranno in grado di utilizzare questo metodo. Questo consente un controllo granulare sugli utenti che possono accedere alle informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) ora è deprecata ed è stata sostituita da [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) e [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) sono ora deprecate. Il protocollo HTTP è ora sempre abilitato, quindi questi metodi non sono più necessari.

### <a name="sdk-for-python"></a>SDK per Python

Eseguire l'aggiornamento alla [versione 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) o successiva di HDInsight SDK per Python. Potrebbero essere necessarie modifiche minime al codice se si utilizza un metodo interessato da queste modifiche:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) **non restituirà più parametri sensibili** come le chiavi di archiviazione (sito Core) o le credenziali http (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, usare [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) in futuro.  Si noti che gli utenti con il ruolo "Reader" non saranno in grado di utilizzare questo metodo. Questo consente un controllo granulare sugli utenti che possono accedere alle informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) ora è deprecata ed è stata sostituita da [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK per Java

Eseguire l'aggiornamento alla [versione 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) o successiva di HDInsight SDK per Java. Potrebbero essere necessarie modifiche minime al codice se si utilizza un metodo interessato da queste modifiche:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) **non restituirà più parametri sensibili** come le chiavi di archiviazione (sito Core) o le credenziali http (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, usare [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) in futuro.  Si noti che gli utenti con il ruolo "Reader" non saranno in grado di utilizzare questo metodo. Questo consente un controllo granulare sugli utenti che possono accedere alle informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) ora è deprecata ed è stata sostituita da [`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK per go

Eseguire l'aggiornamento alla [versione 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) o successiva di HDInsight SDK per go. Potrebbero essere necessarie modifiche minime al codice se si utilizza un metodo interessato da queste modifiche:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) **non restituirà più parametri sensibili** come le chiavi di archiviazione (sito Core) o le credenziali http (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, usare [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) in futuro.  Si noti che gli utenti con il ruolo "Reader" non saranno in grado di utilizzare questo metodo. Questo consente un controllo granulare sugli utenti che possono accedere alle informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) ora è deprecata ed è stata sostituita da [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>AZ. HDInsight PowerShell
Eseguire l'aggiornamento a [AZ PowerShell versione 2.0.0](https://www.powershellgallery.com/packages/Az) o successiva per evitare interruzioni.  Potrebbero essere necessarie modifiche minime al codice se si utilizza un metodo interessato da queste modifiche.
- `Grant-AzHDInsightHttpServicesAccess` è ora deprecato ed è stato sostituito dal nuovo cmdlet `Set-AzHDInsightGatewayCredential`.
- `Get-AzHDInsightJobOutput` è stato aggiornato per supportare l'accesso granulare basato sui ruoli alla chiave di archiviazione.
    - Gli utenti con ruolo di collaboratore, proprietario oppure operatore cluster HDInsight non sono interessati.
    - Gli utenti con solo il ruolo Reader dovranno specificare esplicitamente il parametro `DefaultStorageAccountKey`.
- `Revoke-AzHDInsightHttpServicesAccess` è ora deprecata. Il protocollo HTTP è ora sempre abilitato, quindi questo cmdlet non è più necessario.
 Vedere [AZ. Guida alla migrazione di HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) per altri dettagli.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Aggiungere l'assegnazione di ruolo Operatore cluster HDInsight a un utente

Un utente con il ruolo di [proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) può assegnare il ruolo di [operatore cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) agli utenti per i quali si vuole avere accesso in lettura/scrittura ai valori di configurazione del cluster HDInsight sensibili, ad esempio le credenziali del gateway del cluster e le chiavi dell'account di archiviazione.

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Il modo più semplice per aggiungere questa assegnazione di ruolo consiste nell'usare il comando `az role assignment create` nell'interfaccia della riga di comando di Azure.

> [!NOTE]
> Questo comando deve essere eseguito da un utente con il ruolo proprietario, perché solo questi possono concedere tali autorizzazioni. Il `--assignee` è il nome dell'entità servizio o dell'indirizzo di posta elettronica dell'utente a cui si vuole assegnare il ruolo di operatore cluster HDInsight. Se si riceve un errore di autorizzazioni insufficienti, vedere le domande frequenti riportate di seguito.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Concedere il ruolo a livello di risorsa (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Concedi ruolo a livello di gruppo di risorse

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Concedi ruolo a livello di sottoscrizione

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

In alternativa, è possibile usare la portale di Azure per aggiungere l'assegnazione di ruolo Operatore cluster HDInsight a un utente. Vedere la documentazione, [gestire l'accesso alle risorse di Azure usando RBAC e il portale di Azure-aggiungere un'assegnazione di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>domande frequenti

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Perché viene visualizzato un 403 (accesso negato) dopo l'aggiornamento delle richieste e/o dello strumento API?

Le configurazioni del cluster sono ora sottostanti il controllo degli accessi in base al ruolo granulari e richiedono l'autorizzazione `Microsoft.HDInsight/clusters/configurations/*` per accedervi. Per ottenere questa autorizzazione, assegnare il ruolo Operatore cluster HDInsight, collaboratore o proprietario all'utente o all'entità servizio che tenta di accedere alle configurazioni.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Perché vengono visualizzati "privilegi insufficienti per completare l'operazione" quando si esegue il comando dell'interfaccia della riga di comando di Azure per assegnare il ruolo Operatore cluster HDInsight a un altro utente o a un'entità servizio?

Oltre a avere il ruolo proprietario, l'utente o l'entità servizio che esegue il comando deve avere autorizzazioni AAD sufficienti per cercare gli ID oggetto dell'assegnatario. Questo messaggio indica autorizzazioni AAD insufficienti. Provare a sostituire l'argomento `-–assignee` con `–assignee-object-id` e specificare l'ID oggetto dell'assegnatario come parametro anziché il nome (o l'ID principale nel caso di un'identità gestita). Per altre informazioni, vedere la sezione relativa ai parametri facoltativi della [documentazione AZ Role Assignment create](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) .

Se il problema persiste, contattare l'amministratore di AAD per acquisire le autorizzazioni corrette.

### <a name="what-will-happen-if-i-take-no-action"></a>Che cosa succede se non viene eseguita alcuna azione?

A partire dal 3 settembre 2019, le chiamate `GET /configurations` e `POST /configurations/gateway` non restituiranno più informazioni e la chiamata `GET /configurations/{configurationName}` non restituirà più parametri sensibili, ad esempio le chiavi dell'account di archiviazione o la password del cluster. Lo stesso vale per i metodi SDK e i cmdlet di PowerShell corrispondenti.

Se si usa una versione precedente di uno degli strumenti per Visual Studio, VSCode, IntelliJ o Eclipse menzionati in precedenza, non funzioneranno più fino a quando non si esegue l'aggiornamento.

Per informazioni più dettagliate, vedere la sezione corrispondente di questo documento per lo scenario in uso.