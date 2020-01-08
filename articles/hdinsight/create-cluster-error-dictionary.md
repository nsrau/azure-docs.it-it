---
title: Creare un dizionario degli errori del cluster
description: Informazioni su come creare un dizionario degli errori del cluster.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483063"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight: errori di creazione del cluster

Questo articolo descrive le soluzioni per gli errori riscontrati durante la creazione di cluster. 

> [!NOTE]
> I primi tre errori nell'elenco seguente si verificano a causa di errori di convalida quando la classe CsmDocument_2_0 viene utilizzata da un prodotto HDInsight.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument ' CsmDocument_2_0' non ha superato la convalida

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Errore**: Impossibile accedere al percorso dell'azione di script:\<URL azione script\>

**Messaggio di errore: "il server remoto ha restituito un errore: (404) non trovato".**

### <a name="cause"></a>Causa
L'URL dell'azione script fornito come parte della richiesta create cluster non è accessibile dal servizio HDInsight. Quando si tenta di accedere all'azione script, viene visualizzato il messaggio "ErrorMessage".

### <a name="resolution"></a>Risoluzione 
  - Per un URL http/https, è possibile verificare tentando di aprire l'URL da una finestra del browser in incognito. 
  - Per un URL WASB, assicurarsi che lo script esista nell'account di archiviazione specificato nella richiesta. Assicurarsi che la chiave di archiviazione per questo account di archiviazione sia accurata. 
  - Per un URL ADLS, assicurarsi che lo script esista nell'account di archiviazione.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument ' CsmDocument_2_0' non ha superato la convalida

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Errore**: Impossibile accedere al percorso dell'azione di script: \<URL azione script\>

**Messaggio di errore: l'URI dello script specificato \<URI dello SCRIPT\> si trova in ADLS, ma questo cluster non ha un'entità di archiviazione di data Lake**

### <a name="cause"></a>Causa
L'URL dell'azione script fornito come parte della richiesta create cluster non è accessibile dal servizio HDInsight. Quando si tenta di accedere all'azione script, viene visualizzato il messaggio "ErrorMessage". 

### <a name="resolution"></a>Risoluzione

Assicurarsi che l'account Azure Data Lake Store generazione 1 corrispondente venga aggiunto al cluster. Anche l'entità servizio usata per accedere all'account Azure Data Lake Store generazione 1 viene aggiunta al cluster. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument ' CsmDocument_2_0' non ha superato la convalida

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Errore**: le dimensioni della macchina virtuale '\<le dimensioni della macchina virtuale specificate\>' specificate nella richiesta non sono valide o non sono supportate per il ruolo '\<Role\>'. I valori validi sono: \<dimensioni valide della macchina virtuale per il ruolo\>.

### <a name="cause"></a>Causa
Le dimensioni delle macchine virtuali specificate dal cliente non sono consentite per il ruolo. Questa operazione può essere vera perché il valore delle dimensioni della macchina virtuale non funziona come previsto o non è adatto al ruolo del computer. 

### <a name="resolution"></a>Risoluzione
Il messaggio di errore elenca i valori validi per le dimensioni della macchina virtuale. Selezionare uno di questi valori validi e riprovare la richiesta di creazione del cluster. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Codice di errore: InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Errore**: VirtualNetworkId non valido. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>' *

### <a name="cause"></a>Causa
Il formato del valore **VirtualNetworkId** specificato durante la creazione del cluster non è corretto. 

### <a name="resolution"></a>Risoluzione
Verificare che **VirtualNetworkId** e subnet siano nel formato corretto. Per ottenere il valore **VirtualnetworkId** , passare alla portale di Azure, selezionare la rete virtuale e quindi selezionare **Proprietà** nel menu. La proprietà **resourceId** è il valore **VirtualNetworkId** . 

Un esempio di ID di rete virtuale è: 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>Codice di errore: CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Errore**: la distribuzione del cluster non è riuscita a causa di un errore nell'azione script personalizzata. Azioni non riuscite: \<SCRIPT_NAME\>, passare all'interfaccia utente di Ambari per eseguire ulteriormente il debug dell'errore.

### <a name="cause"></a>Causa
Lo script personalizzato dell'utente fornito durante la richiesta create cluster viene eseguito dopo che il cluster è stato distribuito correttamente. Questo codice di errore indica che si è verificato un errore durante l'esecuzione di questo script personalizzato con il nome \<SCRIPT_NAME\>.   

### <a name="resolution"></a>Risoluzione
Poiché si tratta dello script personalizzato dell'utente, gli utenti devono risolvere il problema ed eseguire nuovamente lo script, se necessario. Per risolvere gli errori di script, esaminare i log nella cartella/var/lib/Ambari-Agent/*. In alternativa, aprire la pagina operazioni nell'interfaccia utente di Ambari, quindi selezionare l'operazione **run_customscriptionaction** per visualizzare i dettagli dell'errore. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Codice di errore: InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Errore**: il \<META_STORE_TYPE\> versione dello schema del metastore \<METASTORE_MAJOR_VERSION\> nel database \<database_name\> è incompatibile con la versione del cluster \<CLUSTER_VERSION\>

### <a name="cause"></a>Causa
Il Metastore personalizzato è incompatibile con la versione selezionata del cluster HDInsight. Attualmente, il cluster HDInsight 4,0 supporta solo Metastore versione 3. *x*e HDInsight 3,6 non supportano il Metastore versione 3. *x* o versione successiva. 

### <a name="resolution"></a>Risoluzione
Assicurarsi di usare solo le versioni del Metastore supportate da ogni versione del cluster HDInsight. Si noti che se non viene specificato un Metastore personalizzato, HDInsight crea internamente un Metastore. Questo Metastore verrà tuttavia eliminato automaticamente al momento dell'eliminazione del cluster. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Codice di errore: FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Errore**: non è possibile connettersi all'endpoint di gestione cluster per eseguire un'operazione di ridimensionamento. Verificare che le regole di sicurezza di rete non blocchino l'accesso esterno al cluster e che sia possibile accedere all'interfaccia utente di gestione cluster (Ambari).

### <a name="cause"></a>Causa
Si dispone di una regola del firewall nel gruppo di sicurezza di rete (NSG) che blocca la comunicazione del cluster con i servizi critici di gestione e integrità di Azure. 

### <a name="resolution"></a>Risoluzione
Se si prevede di usare i **gruppi di sicurezza di rete** per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight: 
  - Identificare l'area di Azure che si intende usare per HDInsight. 
  - Identificare gli indirizzi IP richiesti da HDInsight. Per altre informazioni, vedere [Indirizzi IP di gestione di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
    - Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight. 
    - **Gruppi di sicurezza di rete:** Consentire il traffico in **ingresso** sulla porta **443** dagli indirizzi IP. Ciò assicura che i servizi di gestione HDInsight possano raggiungere il cluster dall'esterno della rete virtuale. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Codice di errore: StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Errore**: l'identità gestita non dispone delle autorizzazioni per l'account di archiviazione. Verificare che il ruolo "proprietario dati BLOB di archiviazione" sia assegnato all'identità gestita per l'account di archiviazione. Archiviazione:/subscriptions/\<ID sottoscrizione\>/resourceGroups/\< nome del gruppo di risorse\>/providers/Microsoft.Storage/storageAccounts/\<nome dell'account di archiviazione\>, identità gestita:/subscriptions/\<ID sottoscrizione\>/resourceGroups//\< nome del gruppo di risorse\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<nome identità gestita dall'utente\>

### <a name="cause"></a>Causa
Non sono state fornite le autorizzazioni necessarie per **gestire l'identità.** L' **identità gestita assegnata dall'utente** non ha un ruolo Collaboratore archiviazione Blob per ADLS Gen2 account di archiviazione. 

### <a name="resolution"></a>Risoluzione

Aprire il portale di Azure, passare all'account di archiviazione, controllare in **controllo di accesso (IAM)** e verificare che il ruolo di collaboratore dati BLOB di archiviazione o il ruolo proprietario dati BLOB di archiviazione abbia "assegnato" l'accesso all' **identità gestita assegnata dall'utente** per la sottoscrizione. Per altre informazioni, vedere [configurare le autorizzazioni per l'identità gestita nell'account data Lake storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md). 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Codice di errore: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Errore**: le regole di sicurezza nel gruppo di sicurezza di rete/subscriptions/\<SubscriptionID >\/resourceGroups/<Resource Group name> Default/Providers/Microsoft. Network/networkSecurityGroups/\<nome del gruppo di sicurezza di rete\> configurato con subnet/subscriptions/\<SubscriptionID >\/resourceGroups/\<nome del gruppo di risorse > RG-westeurope-VNET-TomTom-default\/Providers/Microsoft. Network/virtualNetworks/\<Virtual Nome di rete >\/subnet/\<nome subnet\> 
non consente la connettività in ingresso e/o in uscita richiesta. Per altre informazioni, vedere [pianificare una rete virtuale per Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)oppure contattare il supporto tecnico. * *

### <a name="cause"></a>Causa
Se si usano gruppi di sicurezza di rete (gruppi) o route definite dall'utente (UDR) per controllare il traffico in ingresso verso il cluster HDInsight, è necessario assicurarsi che il cluster sia in grado di comunicare con i servizi di gestione e integrità di Azure critici.

### <a name="resolution"></a>Risoluzione
Se si prevede di usare i **gruppi di sicurezza di rete** per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight: 
  - Identificare l'area di Azure che si intende usare per HDInsight e creare un elenco sicuro degli indirizzi IP per la propria area: [servizi di integrità e gestione: aree specifiche](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
  - Identificare gli indirizzi IP richiesti da HDInsight. Per altre informazioni, vedere [indirizzi IP di gestione di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
  - Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight. **Gruppi di sicurezza di rete**: consentire il traffico in ingresso sulla porta **443** dagli indirizzi IP. In questo modo, i servizi di gestione HDInsight possono raggiungere il cluster dall'esterno della rete virtuale.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Codice di errore: installazione del cluster non è riuscita a installare i componenti in uno o più host

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Errore**: l'installazione del cluster non è riuscita a installare i componenti in uno o più host. Si prega di ripetere la richiesta. 

### <a name="cause"></a>Causa 
In genere, questo errore viene generato quando si verifica un problema temporaneo o si verifica un'interruzione di Azure 

### <a name="resolution"></a>Risoluzione

Controllare la pagina di [stato di Azure](https://status.azure.com/status) per eventuali potenziali interruzioni di Azure che potrebbero influisca sulla distribuzione del cluster. Se non sono presenti interruzioni, riprovare la distribuzione del cluster. 

## <a name="next-steps"></a>Fasi successive

Per informazioni più dettagliate sulla risoluzione dei problemi relativi alla creazione di cluster, vedere risolvere gli errori di [creazione del cluster con Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
