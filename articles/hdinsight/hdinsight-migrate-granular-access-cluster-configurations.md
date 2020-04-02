---
title: Configurazioni granulari del cluster di Azure HDInsight con accesso basato sui ruoli
description: Informazioni sulle modifiche necessarie durante la migrazione all'accesso granulare basato sui ruoli per le configurazioni del cluster HDInsight.Learn about the changes required as part of the migration to granular role-based access for HDInsight cluster configurations.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: bb78d84aa0f9a2832b6599edeac9d50e0e226437
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546348"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Eseguire la migrazione all'accesso granulare in base al ruolo per le configurazioni dei cluster

Stiamo introducendo alcune importanti modifiche per supportare un accesso più dettagliato basato sui ruoli per ottenere informazioni riservate. Come parte di queste modifiche, alcune azioni potrebbero essere necessarie entro il **3 settembre 2019** se si utilizza una delle [entità/scenari interessati.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Cosa cambierà

In precedenza, i segreti potevano essere ottenuti tramite l'API HDInsight dagli utenti del cluster `*/read` in possesso dei ruoli [RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)Proprietario, Collaboratore o Lettore, poiché erano disponibili per chiunque disponga dell'autorizzazione. I segreti sono definiti come valori che potrebbero essere utilizzati per ottenere un accesso con privilegi elevati rispetto a quello consentito dal ruolo di un utente. Questi includono valori come le credenziali HTTP del gateway del cluster, le chiavi dell'account di archiviazione e le credenziali del database.

A partire dal 3 settembre 2019, `Microsoft.HDInsight/clusters/configurations/action` l'accesso a questi segreti richiederà l'autorizzazione, il che significa che non è più accessibile agli utenti con il ruolo Lettore. I ruoli che dispongono di questa autorizzazione sono Collaboratore, Proprietario e il nuovo ruolo operatore cluster HDInsight (ulteriori informazioni di seguito).

È inoltre in corso l'introduzione di un nuovo ruolo [operatore cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) che sarà in grado di recuperare i segreti senza disporre delle autorizzazioni amministrative di Collaboratore o Proprietario. Per riepilogare:

| Ruolo                                  | In precedenza                                                                                        | Andare avanti       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Reader                                | - Accesso in lettura, inclusi i segreti                                                                   | - Accesso in lettura, **esclusi i** segreti |           |   |   |
| Operatore cluster HDInsightHDInsight Cluster Operator<br>(Nuovo ruolo) | N/D                                                                                              | - Accesso in lettura/scrittura, inclusi i segreti         |   |   |
| Collaboratore                           | - Accesso in lettura/scrittura, inclusi i segreti<br>- Creare e gestire tutti i tipi di risorse di Azure.- Create and manage all of types of Azure resources.     | Nessuna modifica |
| Proprietario                                 | - Accesso in lettura/scrittura inclusi i segreti<br>- Accesso completo a tutte le risorse<br>- Delegare l'accesso ad altri | Nessuna modifica |

Per informazioni su come aggiungere l'assegnazione di ruolo Operatore cluster HDInsight a un utente per concedere loro l'accesso in lettura/scrittura ai segreti del cluster, vedere la sezione seguente [Aggiungere l'assegnazione di ruolo Operatore cluster HDInsight a un utente.](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)

## <a name="am-i-affected-by-these-changes"></a>Sono interessato da questi cambiamenti?

Sono interessate le entità e gli scenari seguenti:The following entities and scenarios are affected:

- [API](#api): Gli `/configurations` `/configurations/{configurationName}` utenti che utilizzano gli endpoint o .
- [Strumenti di Azure HDInsight per Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versione 1.1.1 o versione successiva.
- [Toolkit di Azure per IntelliJ](#azure-toolkit-for-intellij) versione 3.20.0 o inferiore.
- [Strumenti](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) di Analisi dei dati di Azure e di flusso per Visual Studio sotto la versione 2.3.9000.1.Azure Data Lake and Stream Analytics Tools for Visual Studio below version 2.3.9000.1.
- [Azure Toolkit per Eclipse](#azure-toolkit-for-eclipse) versione 3.15.0 o inferiore.
- [SDK per .NET](#sdk-for-net)
    - [versioni 1.x o 2.x](#versions-1x-and-2x) `GetClusterConfigurations`: `GetConnectivitySettings` `ConfigureHttpSettings`gli `EnableHttp` `DisableHttp` utenti che utilizzano i metodi , , , o della classe ConfigurationsOperationsExtensions .
    - [versioni 3.x e successive](#versions-3x-and-up) `Get`: `Update` `EnableHttp`Gli `DisableHttp` utenti che `ConfigurationsOperationsExtensions` utilizzano i metodi , , , o della classe .
- [SDK per Python](#sdk-for-python): `get` `update` Gli utenti `ConfigurationsOperations` che utilizzano i metodi o della classe .
- [SDK per Java](#sdk-for-java): `update` `get` Utenti che `ConfigurationsInner` utilizzano i metodi o della classe .
- [SDK for Go](#sdk-for-go): `Get` `Update` Utenti che `ConfigurationsClient` utilizzano i metodi o della struttura.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) sotto la versione 2.0.0.Az.HDInsight PowerShell below version 2.0.0.
Vedere le sezioni seguenti (o usare i collegamenti precedenti) per visualizzare i passaggi di migrazione per lo scenario.

### <a name="api"></a>API

Le API seguenti verranno modificate o deprecate:

- [**GET /configurations/'nomeconfigurazione'**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informazioni riservate rimosse)
    - Utilizzato in precedenza per ottenere singoli tipi di configurazione (inclusi i segreti).
    - A partire dal 3 settembre 2019, questa chiamata API restituirà i singoli tipi di configurazione con i segreti omessi. Per ottenere tutte le configurazioni, inclusi i segreti, utilizzare la nuova chiamata POST /configurations. Per ottenere solo le impostazioni del gateway, utilizzare la nuova chiamata POST /getGatewaySettings.
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (deprecato)
    - Utilizzato in precedenza per ottenere tutte le configurazioni (inclusi i segreti)
    - A partire dal 3 settembre 2019, questa chiamata API sarà deprecata e non sarà più supportata. Per ottenere tutte le configurazioni in futuro, utilizzare la nuova chiamata POST /configurations. Per ottenere le configurazioni con parametri riservati omessi, utilizzare la chiamata GET /configurations/'configurationName'.
- [**POST /configurations/**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Utilizzato in precedenza per aggiornare le credenziali del gateway.
    - A partire dal 3 settembre 2019, questa chiamata API sarà deprecata e non più supportata. Utilizzare invece il nuovo POST /updateGatewaySettings.

Sono state aggiunte le seguenti API sostitutive:</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Usare questa API per ottenere tutte le configurazioni, inclusi i segreti.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Utilizzare questa API per ottenere le impostazioni del gateway.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Utilizzare questa API per aggiornare le impostazioni del gateway (nome utente e/o password).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

Se si usa la versione 1.1.1 o successiva, eseguire l'aggiornamento alla [versione più recente di Azure HDInsight Tools per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) per evitare interruzioni.

### <a name="azure-toolkit-for-intellij"></a>Toolkit di Azure per IntelliJ

Se si usa la versione 3.20.0 o successiva, eseguire l'aggiornamento alla [versione più recente del plug-in Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) per evitare interruzioni.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake and Stream Analytics Tools for Visual Studio

Eseguire l'aggiornamento alla versione 2.3.9000.1 o successiva di Azure Data Lake e Strumenti analisi [di flusso per Visual Studio per](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) evitare interruzioni.  Per informazioni sull'aggiornamento, vedere la documentazione relativa agli aggiornamenti di [Data Lake Tools per Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Toolkit di Azure per Eclipse

Se si usa la versione 3.15.0 o successiva, eseguire l'aggiornamento alla [versione più recente di Azure Toolkit per Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) per evitare interruzioni.

### <a name="sdk-for-net"></a>SDK per .NET

#### <a name="versions-1x-and-2x"></a>Versioni 1.x e 2.x

Aggiornamento alla [versione 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) di HDInsight SDK per .NET. Se si utilizza un metodo interessato da queste modifiche, possono essere necessarie modifiche minime al codice:Minimal code modifications may be required if you are using a method affected by these changes:

- `ClusterOperationsExtensions.GetClusterConfigurations`**non restituirà più parametri sensibili** come le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare `ClusterOperationsExtensions.ListConfigurations` in futuro.  Si noti che gli utenti con il ruolo 'Reader' non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare sugli utenti che possono accedere a informazioni riservate per un cluster.
    - Per recuperare solo le credenziali `ClusterOperationsExtensions.GetGatewaySettings`del gateway HTTP, utilizzare .

- `ClusterOperationsExtensions.GetConnectivitySettings`è ora deprecato ed `ClusterOperationsExtensions.GetGatewaySettings`è stato sostituito da .

- `ClusterOperationsExtensions.ConfigureHttpSettings`è ora deprecato ed `ClusterOperationsExtensions.UpdateGatewaySettings`è stato sostituito da .

- `ConfigurationsOperationsExtensions.EnableHttp`e `DisableHttp` sono ora deprecati. HTTP è ora sempre abilitato, quindi questi metodi non sono più necessari.

#### <a name="versions-3x-and-up"></a>Versioni 3.x e successive

Eseguire l'aggiornamento alla [versione 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) o successiva di HDInsight SDK per .NET. Se si utilizza un metodo interessato da queste modifiche, possono essere necessarie modifiche minime al codice:Minimal code modifications may be required if you are using a method affected by these changes:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**non restituirà più parametri sensibili** come le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) in futuro.Si noti che gli utenti con il ruolo 'Reader' non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare sugli utenti che possono accedere a informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)del gateway HTTP, utilizzare . 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)è ora deprecato ed [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)è stato sostituito da . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)e [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) sono ora deprecati. HTTP è ora sempre abilitato, quindi questi metodi non sono più necessari.

### <a name="sdk-for-python"></a>SDK per Python

Eseguire l'aggiornamento alla [versione 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) o successiva di HDInsight SDK per Python. Se si utilizza un metodo interessato da queste modifiche, possono essere necessarie modifiche minime al codice:Minimal code modifications may be required if you are using a method affected by these changes:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**non restituirà più parametri sensibili** come le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) in futuro.Si noti che gli utenti con il ruolo 'Reader' non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare sugli utenti che possono accedere a informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)del gateway HTTP, utilizzare .
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)è ora deprecato ed [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)è stato sostituito da .

### <a name="sdk-for-java"></a>SDK per Java

Eseguire l'aggiornamento alla [versione 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) o successiva di HDInsight SDK per Java. Se si utilizza un metodo interessato da queste modifiche, possono essere necessarie modifiche minime al codice:Minimal code modifications may be required if you are using a method affected by these changes:

- `ConfigurationsInner.get`**non restituirà più parametri sensibili** come le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
- `ConfigurationsInner.update`è ora deprecato.

### <a name="sdk-for-go"></a>SDK Per Go

Eseguire l'aggiornamento alla [versione 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) o successiva di HDInsight SDK for Go. Se si utilizza un metodo interessato da queste modifiche, possono essere necessarie modifiche minime al codice:Minimal code modifications may be required if you are using a method affected by these changes:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**non restituirà più parametri sensibili** come le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) in futuro.Si noti che gli utenti con il ruolo 'Reader' non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare sugli utenti che possono accedere a informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)del gateway HTTP, utilizzare .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)è ora deprecato ed [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)è stato sostituito da .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Aggiornamento a [Az PowerShell versione 2.0.0](https://www.powershellgallery.com/packages/Az) o successiva per evitare interruzioni.  Se si utilizza un metodo interessato da tali modifiche, potrebbero essere necessarie modifiche minime al codice.
- `Grant-AzHDInsightHttpServicesAccess`è ora deprecato ed è `Set-AzHDInsightGatewayCredential` stato sostituito dal nuovo cmdlet.
- `Get-AzHDInsightJobOutput`è stato aggiornato per supportare l'accesso granulare basato sui ruoli alla chiave di archiviazione.
    - Gli utenti con ruolo di collaboratore, proprietario oppure operatore cluster HDInsight non sono interessati.
    - Gli utenti con solo il ruolo `DefaultStorageAccountKey` Lettore dovranno specificare il parametro in modo esplicito.
- `Revoke-AzHDInsightHttpServicesAccess`è ora deprecato. HTTP è ora sempre abilitato, pertanto questo cmdlet non è più necessario.
 Vedi [l'az. Guida alla migrazione di HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) per ulteriori dettagli.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Aggiungere l'assegnazione di ruolo Operatore cluster HDInsight a un utenteAdd the HDInsight Cluster Operator role assignment to a user

Un utente con il ruolo [Proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) può assegnare il ruolo [di operatore cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) agli utenti che si desidera avere accesso in lettura/scrittura ai valori di configurazione del cluster HDInsight sensibili, ad esempio le credenziali del gateway del cluster e le chiavi dell'account di archiviazione.

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Il modo più semplice per aggiungere `az role assignment create` questa assegnazione di ruolo consiste nell'usare il comando nell'interfaccia della riga di comando di Azure.The simplest way to add this role assignment is by using the command in Azure CLI.

> [!NOTE]
> Questo comando deve essere eseguito da un utente con il ruolo Proprietario, in quanto solo loro possono concedere queste autorizzazioni. È `--assignee` il nome dell'entità servizio o dell'indirizzo di posta elettronica dell'utente a cui si desidera assegnare il ruolo Operatore cluster HDInsight. Se viene visualizzato un errore di autorizzazioni insufficienti, vedere le domande frequenti riportate di seguito.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Concedere il ruolo a livello di risorsa (cluster)Grant role at the resource (cluster) level

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Concedere il ruolo a livello di gruppo di risorseGrant role at the resource group level

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Concedere il ruolo a livello di sottoscrizioneGrant role at the subscription level

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

In alternativa, è possibile usare il portale di Azure per aggiungere l'assegnazione di ruolo Operatore cluster HDInsight a un utente. Vedere la documentazione Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo [e il portale di Azure - Aggiungere un'assegnazione](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)di ruolo.

## <a name="faq"></a>Domande frequenti

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Perché viene visualizzata una risposta 403 (Accesso negato) dopo l'aggiornamento delle richieste e/o dello strumento API?

Le configurazioni cluster sono ora alla base `Microsoft.HDInsight/clusters/configurations/*` del controllo granulare degli accessi in base al ruolo e richiedono l'autorizzazione per accedervi. Per ottenere questa autorizzazione, assegnare il ruolo Operatore cluster, Collaboratore o Proprietario di HDInsight all'utente o all'entità servizio che tenta di accedere alle configurazioni.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Perché viene visualizzato "Privilegi insufficienti per completare l'operazione" quando si esegue il comando dell'interfaccia della riga di comando di Azure per assegnare il ruolo Operatore cluster HDInsight a un altro utente o entità servizio?

Oltre a disporre del ruolo Proprietario, l'utente o l'entità servizio che esegue il comando deve disporre di autorizzazioni di Azure AD sufficienti per cercare gli ID oggetto dell'assegnatario. Questo messaggio indica autorizzazioni di Azure AD insufficienti. Provare a `-–assignee` sostituire `–assignee-object-id` l'argomento con e fornire l'ID oggetto dell'assegnatario come parametro anziché il nome (o l'ID principale nel caso di un'identità gestita). Per altre informazioni, vedere la sezione relativa ai parametri facoltativi della documentazione relativa alla [creazione dell'assegnazione](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) di ruolo az.See the optional parameters section of the az role assignment create documentation for more info.

Se il problema persiste, contattare l'amministratore di Azure AD per acquisire le autorizzazioni corrette.

### <a name="what-will-happen-if-i-take-no-action"></a>Cosa succede se non intervengo?

A partire dal 3 settembre `GET /configurations` `POST /configurations/gateway` 2019 e le `GET /configurations/{configurationName}` chiamate non restituiranno più informazioni e la chiamata non restituirà più parametri sensibili, ad esempio le chiavi dell'account di archiviazione o la password del cluster. Lo stesso vale per i metodi SDK e i cmdlet di PowerShell corrispondenti.

Se si utilizza una versione precedente di uno degli strumenti per Visual Studio, VSCode, IntelliJ o Eclipse menzionati in precedenza, non funzioneranno più fino a quando non si esegue l'aggiornamento.

Per informazioni più dettagliate, vedere la sezione corrispondente di questo documento per lo scenario.
