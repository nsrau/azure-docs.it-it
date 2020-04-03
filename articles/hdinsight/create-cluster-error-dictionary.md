---
title: Azure HDInsight Creare un cluster - dizionario degli erroriAzure HDInsight Create a cluster - error dictionary
description: Informazioni su come risolvere gli errori che si verificano durante la creazione di cluster di Azure HDInsightLearn how to troubleshoot errors that occur when creating Azure HDInsight clusters
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 803783eddfbffd5c3dbab7353ee00dd7f11a09e5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618892"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: errori di creazione del clusterAzure HDInsight: Cluster creation errors

In questo articolo vengono descritte le risoluzioni agli errori che potrebbero verificarsi durante la creazione di cluster.

> [!NOTE]
> I primi tre errori descritti in questo articolo sono errori di convalida. Possono verificarsi quando un prodotto Azure HDInsight usa la classe **CsmDocument_2_0.**

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument 'CsmDocument_2_0' non riuscita la convalida

### <a name="error"></a>Errore

"Impossibile accedere al percorso\<dell'azione di script URI: URL\>AZIONE SCRIPT"

#### <a name="error-message"></a>Messaggio di errore

"Il server remoto ha restituito un errore: (404) Non trovato."

### <a name="cause"></a>Causa

Il servizio HDInsight non può accedere all'URL dell'azione script fornito come parte della richiesta Crea cluster. Il servizio riceve il messaggio di errore precedente quando tenta di accedere all'azione script.

### <a name="resolution"></a>Risoluzione

- Per un URL HTTP o HTTPS, verifica l'URL provando a passare da una finestra del browser di navigazione in incognito.
- Per un URL WASB, assicurarsi che lo script esista nell'account di archiviazione che si assegna nella richiesta. Assicurarsi inoltre che la chiave di archiviazione per questo account di archiviazione sia corretta.
- Per un URL ADLS, assicurarsi che lo script esista nell'account di archiviazione.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument 'CsmDocument_2_0' non riuscita la convalida

### <a name="error"></a>Errore

"Impossibile accedere al percorso \<\>dell'azione di script: SCRIPT_ACTION_URL"

#### <a name="error-message"></a>Messaggio di errore

"Il SCRIPT_URI\> \<URI dello script specificato è in ADLS, ma questo cluster non dispone di alcuna entità di archiviazione data lake"

### <a name="cause"></a>Causa

Il servizio HDInsight non può accedere all'URL dell'azione script fornito come parte della richiesta Crea cluster. Il servizio riceve il messaggio di errore precedente quando tenta di accedere all'azione script.

### <a name="resolution"></a>Risoluzione

Aggiungere l'account Azure Data Lake Storage Gen 1 corrispondente al cluster. Aggiungere inoltre l'entità servizio che accede all'account Data Lake Storage Gen 1 al cluster.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument 'CsmDocument_2_0' non riuscita la convalida

### <a name="error"></a>Errore

"Dimensione vm\<\>' CUSTOMER_SPECIFIED_VM_SIZE ' fornito nella richiesta non\<\>è valido o non è supportato per il ruolo ' RUOLO '. I valori \<validi\>sono: VALID_VM_SIZE_FOR_ROLE ".

### <a name="cause"></a>Causa

La dimensione della macchina virtuale specificata non è consentita per il ruolo. Questo errore può verificarsi perché il valore della dimensione della macchina virtuale non funziona come previsto o non è adatto per il ruolo del computer.

### <a name="resolution"></a>Risoluzione

Il messaggio di errore elenca i valori validi per la dimensione della macchina virtuale. Selezionare uno di questi valori e ripetere la richiesta Crea cluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Codice di errore: InvalidVirtualNetworkId  

### <a name="error"></a>Errore

"VirtualNetworkId non è valido. VirtualNetworkId\<'\>USER_VIRTUALNETWORKID '

### <a name="cause"></a>Causa

Il valore **VirtualNetworkId** specificato durante la creazione del cluster non è nel formato corretto.

### <a name="resolution"></a>Risoluzione

Assicurarsi che i valori **di VirtualNetworkId** e subnet siano nel formato corretto. Per ottenere il valore **VirtualNetworkId:**

1. Accedere al portale di Azure.
1. Selezionare la rete virtuale.
1. Selezionare la voce di menu **Proprietà.** Il valore della proprietà **ResourceID** è il valore **VirtualNetworkId.The ResourceID** property value is the VirtualNetworkId value.

Ecco un esempio di ID di rete virtuale:Here's an example of a virtual network ID:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Codice di errore: CustomizationFailedErrorCode

### <a name="error"></a>Errore

"Distribuzione del cluster non riuscita a causa di un errore nell'azione di script personalizzato. Azioni non \<\>riuscite: SCRIPT_NAME , Vai ad Ambari UI per eseguire ulteriormente il debug dell'errore."

### <a name="cause"></a>Causa

Lo script personalizzato fornito durante la richiesta Create Cluster viene eseguito dopo la distribuzione corretta del cluster. Questo codice di errore indica che si è verificato \<\>un errore durante l'esecuzione dello script personalizzato denominato SCRIPT_NAME .

### <a name="resolution"></a>Risoluzione

Poiché lo script è lo script personalizzato, è consigliabile risolvere il problema e rieseguire lo script, se necessario. Per risolvere l'errore di script, esaminare i registri nella cartella /var/lib/ambari-agent/. In alternativa, aprire la pagina **Operazioni** nell'interfaccia utente di Ambari e quindi selezionare l'operazione **run_customscriptaction** per visualizzare i dettagli dell'errore.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Codice di errore: InvalidDocumentErrorCode

### <a name="error"></a>Errore

"La \<\> \<versione dello schema\> META_STORE_TYPE \<\> Metastore METASTORE_MAJOR_VERSION nel \<DATABASE_NAME\>del database non è compatibile con la versione del cluster CLUSTER_VERSION"

### <a name="cause"></a>Causa

Il metastore personalizzato non è compatibile con la versione del cluster HDInsight selezionata. Attualmente, i cluster HDInsight 4.0 supportano solo Metastore versione 3.0 e successive, mentre i cluster HDInsight 3.6 non supportano Metastore versione 3.0 e successive.

### <a name="resolution"></a>Risoluzione

Usare solo le versioni di Metastore supportate dalla versione del cluster HDInsight.Use only Metastore versions that your HDInsight cluster version supports. Se non si specifica un metastore personalizzato, HDInsight crea internamente un metastore e quindi lo elimina all'eliminazione del cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Codice di errore: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Errore

"Impossibile connettersi all'endpoint di gestione del cluster per eseguire l'operazione di ridimensionamento. Verificare che le regole di sicurezza di rete non blocchino l'accesso esterno al cluster e che sia possibile accedere correttamente all'interfaccia utente di Gestione cluster (Ambari)."

### <a name="cause"></a>Causa

Una regola del firewall nel gruppo di sicurezza di rete blocca la comunicazione del cluster con i servizi critici di integrità e gestione di Azure.A firewall rule on your network security group (NSG) is blocking cluster communication with critical Azure health and management services.

### <a name="resolution"></a>Risoluzione

Se si prevede di usare i gruppi di sicurezza di rete per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight:If you plan to use network security groups to control network traffic, take the following actions before you install HDInsight:

- Identificare l'area di Azure che si intende usare per HDInsight.
- Identificare gli indirizzi IP richiesti da HDInsight. Per altre informazioni, vedere [Indirizzi IP di gestione di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight.Create or modify the network security groups for the subnet that you plan to install HDInsight into.
  - Per i gruppi di sicurezza di rete, consentire il traffico in ingresso sulla porta 443 dagli indirizzi IP. Questa configurazione garantisce che i servizi di gestione HDInsight possano raggiungere il cluster dall'esterno della rete virtuale.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Codice di errore: StoragePermissionsBlockedForMsi

### <a name="error"></a>Errore

"L'identità gestita non dispone delle autorizzazioni per l'account di archiviazione. Verificare che il ruolo 'Proprietario dati BLOB di archiviazione' sia assegnato all'identità gestita per l'account di archiviazione. Archiviazione: \</subscriptions/\> Subscription ID\< /resourceGroups/Resource\> Group Name /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>, Identità gestita: /subscriptions/ \<Subscription ID\> /resourceGroups/ /\< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>Causa

Non sono stati fornite le autorizzazioni necessarie per gestire l'identità. L'identità gestita assegnata dall'utente non dispone del ruolo Collaboratore archiviazione BLOB nell'account di archiviazione Gen2 di Archiviazione BLOB di Azure.The user-assigned managed identity doesn't have the Blob Storage Contributor role on the Azure Data Lake Storage Gen2 storage account.

### <a name="resolution"></a>Risoluzione

1. Aprire il portale di Azure.
1. Passare all'account di archiviazione.
1. Cercare in **Controllo di accesso (IAM)**.
1. Assicurarsi che all'utente sia assegnato il ruolo Collaboratore dati BLOB di archiviazione o Proprietario dati BLOB di archiviazione.

Per ulteriori informazioni, vedere [Impostare le autorizzazioni per l'identità gestita nell'account Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Codice di errore: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Errore

"Le regole di sicurezza nel gruppo\<di\>sicurezza di rete /subscriptions/ SubscriptionID /resourceGroups/<\<Resource Group\> name\> default/providers/Microsoft.Network/networkSecurityGroups/\> Network Security Group Name configurato con subnet /subscriptions/\<SubscriptionID\>\</resourceGroups/Resource Group name RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Network Name\>/subnets/\<Subnet Name\> does not allow required inbound and/and outbound(and connectivity. Per altre informazioni, vedere [Pianificare una rete virtuale per Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)o contattare il supporto tecnico."

### <a name="cause"></a>Causa

Se i gruppi di sicurezza di rete o le route definite dall'utente (UDR) controllano il traffico in ingresso verso il cluster HDInsight, assicurarsi che il cluster possa comunicare con i servizi critici di integrità e gestione di Azure.If network security groups or user-defined routes (UDR) control inbound traffic to your HDInsight cluster, be sure that your cluster can communicate with critical Azure health and management services.

### <a name="resolution"></a>Risoluzione

Se si prevede di usare i gruppi di sicurezza di rete per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight:If you plan to use network security groups to control network traffic, take the following actions before you install HDInsight:

- Identificare l'area di Azure che si prevede di usare per HDInsight e creare un elenco sicuro degli indirizzi IP per l'area. Per ulteriori informazioni, vedere [Servizi di integrità e gestione: aree specifiche](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Identificare gli indirizzi IP necessari per HDInsight.Identify the IP addresses that HDInsight requires. Per ulteriori informazioni, vedere Indirizzi IP di [gestione HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight.Create or modify the network security groups for the subnet that you plan to install HDInsight into. Per i gruppi di sicurezza di rete, consentire il traffico in ingresso sulla porta 443 dagli indirizzi IP. Questa configurazione garantisce che i servizi di gestione HDInsight possano raggiungere il cluster dall'esterno della rete virtuale.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Codice di errore: Impossibile installare i componenti in uno o più host

### <a name="error"></a>Errore

"Impossibile installare i componenti in uno o più host. Riprovare la richiesta."

### <a name="cause"></a>Causa 

In genere, questo errore viene generato quando si verifica un problema temporaneo o un'interruzione di Azure.Typically, this error is generated when there's a transient problem or an Azure outage.

### <a name="resolution"></a>Risoluzione

Controllare la pagina [Stato di Azure](https://status.azure.com) per eventuali interruzioni di Azure che potrebbero influire sulla distribuzione del cluster. Se non sono presenti interruzioni, ripetere la distribuzione del cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Codice di errore: FailedToConnectWithClusterErrorCode

### <a name="error"></a>Errore

Impossibile connettersi all'endpoint di gestione del cluster. Riprovare più tardi.

### <a name="cause"></a>Causa

Il servizio HDInsight non è in grado di connettersi al cluster durante il tentativo di creare il cluster

### <a name="resolution"></a>Risoluzione

Se si usano gruppi di sicurezza di rete della rete virtuale (NSG) personalizzati e route definite dall'utente, assicurarsi che il cluster possa comunicare con i servizi di gestione HDInsight.If you are using custom VNet network security group (NSGs) and user-defined routes (UDRs), ensure that your cluster can communicate with HDInsight management services. Per altre informazioni, vedere Indirizzi IP di [gestione HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Codice di errore: Distribuzioni non riuscite<Resource URI>a causa di una violazione dei criteri: 'Risorsa' non consentita dai criteri. Identificatori dei criteri: "["policyAssignment":"name":"<Policy Name> ","id":"/providers/Microsoft.Management/managementGroups/<Management Group Name> providers/Microsoft.Authorization/policyAssignments/<Policy Name>","policyDefinition":<Policy Definition>

### <a name="cause"></a>Causa

I criteri di Azure basati su sottoscrizione possono negare la creazione di indirizzi IP pubblici. La creazione del cluster HDInsight richiede due indirizzi IP pubblici.

I criteri seguenti in genere influiscono sulla creazione del cluster:The following policies generally impact cluster creation:

* Criteri che impediscono la creazione di indirizzi IP o servizi di bilanciamento del carico all'interno della sottoscrizione.
* Criteri che impediscono la creazione di account di archiviazione.
* Criteri che impediscono l'eliminazione di risorse di rete, ad esempio indirizzi IP o servizi di bilanciamento del carico.

### <a name="resolution"></a>Risoluzione

Eliminare o disabilitare i criteri di Azure basati su sottoscrizione durante la creazione del cluster HDInsight.Delete or Disable the subscription-based Azure policy while creating HDInsight Cluster.

---

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione degli errori nella creazione del cluster, vedere Risolvere i problemi relativi agli errori di creazione del cluster con Azure HDInsight.For more information about troubleshooting errors in cluster creation, see [Troubleshoot cluster creation failures with Azure HDInsight.](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)
