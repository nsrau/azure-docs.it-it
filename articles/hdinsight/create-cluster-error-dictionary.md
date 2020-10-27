---
title: Azure HDInsight creare un cluster-error Dictionary
description: Informazioni su come risolvere gli errori che si verificano durante la creazione di cluster HDInsight di Azure
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: 97cad12ad1854df37b54ff663385fe79ca15b7c2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543101"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: errori di creazione del cluster

Questo articolo descrive le soluzioni per gli errori che si possono incontrare durante la creazione di cluster.

> [!NOTE]
> I primi tre errori descritti in questo articolo sono errori di convalida. Possono verificarsi quando un prodotto Azure HDInsight usa la classe **CsmDocument_2_0** .

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument ' CsmDocument_2_0' non ha superato la convalida

**Errore** : "Impossibile accedere al percorso dell'azione di script: \<SCRIPT ACTION URL\> "

### <a name="error-message-1"></a>Messaggio di errore 1

"Il server remoto ha restituito un errore: (404) non trovato".

#### <a name="cause"></a>Causa

Il servizio HDInsight non è in grado di accedere all'URL dell'azione script fornito come parte della richiesta di creazione del cluster. Il servizio riceve il messaggio di errore precedente quando tenta di accedere all'azione di script.

#### <a name="resolution"></a>Soluzione

- Per un URL HTTP o HTTPS, verificare l'URL tentando di accedervi da una finestra del browser in incognito.
- Per un URL WASB, assicurarsi che lo script esista nell'account di archiviazione fornito nella richiesta. Assicurarsi anche che la chiave di archiviazione per questo account di archiviazione sia corretta.
- Per un URL ADLS, assicurarsi che lo script esista nell'account di archiviazione.

---

### <a name="error-message-2"></a>Messaggio di errore 2

"L'URI dello script specificato \<SCRIPT_URI\> si trova in ADLS, ma questo cluster non ha un'entità di archiviazione di data Lake"

#### <a name="cause"></a>Causa

Il servizio HDInsight non è in grado di accedere all'URL dell'azione script fornito come parte della richiesta di creazione del cluster. Il servizio riceve il messaggio di errore precedente quando tenta di accedere all'azione di script.

#### <a name="resolution"></a>Soluzione

Aggiungere l'account Azure Data Lake Storage generazione 1 corrispondente al cluster. Aggiungere inoltre l'entità servizio che accede all'account Data Lake Storage generazione 1 al cluster.

---

### <a name="error-message-3"></a>Messaggio di errore 3

"Le dimensioni della macchina virtuale ' \<CUSTOMER_SPECIFIED_VM_SIZE\> ' specificate nella richiesta non sono valide o non sono supportate per il ruolo ' \<ROLE\> '. I valori validi sono: \<VALID_VM_SIZE_FOR_ROLE\> . "

#### <a name="cause"></a>Causa

La dimensione della macchina virtuale specificata non è consentita per il ruolo. Questo errore può verificarsi perché il valore delle dimensioni della macchina virtuale non funziona come previsto o non è adatto per il ruolo del computer.

#### <a name="resolution"></a>Soluzione

Il messaggio di errore elenca i valori validi per le dimensioni della macchina virtuale. Selezionare uno di questi valori e riprovare la richiesta di creazione del cluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Codice di errore: InvalidVirtualNetworkId  

### <a name="error"></a>Errore

"VirtualNetworkId non è valido. VirtualNetworkId " \<USER_VIRTUALNETWORKID\> " * "

### <a name="cause"></a>Causa

Il valore **VirtualNetworkId** specificato durante la creazione del cluster non è nel formato corretto.

### <a name="resolution"></a>Soluzione

Verificare che i valori di **VirtualNetworkId** e subnet siano nel formato corretto. Per ottenere il valore di **VirtualNetworkId** :

1. Accedere al portale di Azure.
1. Selezionare la rete virtuale.
1. Selezionare la voce di menu **Proprietà** . Il valore della proprietà **resourceId** è il valore **VirtualNetworkId** .

Di seguito è riportato un esempio di ID rete virtuale:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Codice di errore: CustomizationFailedErrorCode

### <a name="error"></a>Errore

"Distribuzione del cluster non riuscita a causa di un errore nell'azione script personalizzata. Azioni non riuscite: \<SCRIPT_NAME\> , passare all'interfaccia utente di Ambari per eseguire ulteriormente il debug dell'errore ".

### <a name="cause"></a>Causa

Lo script personalizzato fornito durante la richiesta create cluster viene eseguito dopo che il cluster è stato distribuito correttamente. Questo codice di errore indica che si è verificato un errore durante l'esecuzione dello script personalizzato denominato \<SCRIPT_NAME\> .

### <a name="resolution"></a>Soluzione

Poiché lo script è lo script personalizzato, è consigliabile risolvere il problema ed eseguire nuovamente lo script, se necessario. Per risolvere gli errori di script, esaminare i log nella cartella/var/lib/Ambari-Agent/*. In alternativa, aprire la pagina **operazioni** nell'interfaccia utente di Ambariri, quindi selezionare l'operazione **run_customscriptaction** per visualizzare i dettagli dell'errore.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Codice di errore: InvalidDocumentErrorCode

### <a name="error"></a>Errore

"La \<META_STORE_TYPE\> versione dello schema del Metastore \<METASTORE_MAJOR_VERSION\> nel database non \<DATABASE_NAME\> è compatibile con la versione del cluster \<CLUSTER_VERSION\> "

### <a name="cause"></a>Causa

Il Metastore personalizzato è incompatibile con la versione selezionata del cluster HDInsight. Attualmente, i cluster HDInsight 4,0 supportano solo Metastore versione 3,0 e versioni successive, mentre i cluster HDInsight 3,6 non supportano il Metastore versione 3,0 e versioni successive.

### <a name="resolution"></a>Soluzione

Usare solo le versioni del Metastore supportate dalla versione del cluster HDInsight. Se non si specifica un Metastore personalizzato, HDInsight crea internamente un Metastore e quindi lo elimina dopo l'eliminazione del cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Codice di errore: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Errore

"Impossibile connettersi all'endpoint di gestione cluster per eseguire un'operazione di ridimensionamento. Verificare che le regole di sicurezza di rete non blocchino l'accesso esterno al cluster e che l'interfaccia utente di gestione cluster (Ambari) possa accedere correttamente. "

### <a name="cause"></a>Causa

Una regola del firewall nel gruppo di sicurezza di rete (NSG) blocca la comunicazione del cluster con i servizi critici di gestione e integrità di Azure.

### <a name="resolution"></a>Soluzione

Se si prevede di usare i gruppi di sicurezza di rete per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight:

- Identificare l'area di Azure che si intende usare per HDInsight.
- Identificare gli indirizzi IP richiesti da HDInsight. Per altre informazioni, vedere [Indirizzi IP di gestione di HDInsight](./hdinsight-management-ip-addresses.md).
  - Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight.
  - Per i gruppi di sicurezza di rete, consentire il traffico in ingresso sulla porta 443 dagli indirizzi IP. Questa configurazione garantisce che i servizi di gestione HDInsight possano raggiungere il cluster dall'esterno della rete virtuale.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Codice di errore: StoragePermissionsBlockedForMsi

### <a name="error"></a>Errore

"L'identità gestita non dispone delle autorizzazioni per l'account di archiviazione. Verificare che il ruolo "proprietario dati BLOB di archiviazione" sia assegnato all'identità gestita per l'account di archiviazione. Archiviazione:/subscriptions/ \<Subscription ID\> /ResourceGroups/ \< Resource Group Name\> /providers/Microsoft.storage/storageAccounts/ \<Storage Account Name\> , identità gestita:/subscriptions/ \<Subscription ID\> /resourceGroups// \< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\> "

### <a name="cause"></a>Causa

Non sono state fornite le autorizzazioni necessarie per gestire l'identità. L'identità gestita assegnata dall'utente non ha il ruolo Collaboratore archiviazione BLOB nell'account di archiviazione Azure Data Lake Storage Gen2.

### <a name="resolution"></a>Soluzione

1. Aprire il Portale di Azure.
1. Passare all'account di archiviazione.
1. Controllare il **controllo di accesso (IAM)** .
1. Verificare che all'utente sia assegnato il ruolo di collaboratore dei dati BLOB di archiviazione o il ruolo di proprietario dei dati BLOB di archiviazione.

Per altre informazioni, vedere [configurare le autorizzazioni per l'identità gestita nell'account data Lake storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Codice di errore: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Errore

"Le regole di sicurezza nel gruppo di sicurezza di rete/subscriptions/ \<SubscriptionID\> /resourceGroups/<nome del gruppo di risorse \> Default/Providers/Microsoft. Network/networkSecurityGroups/ \<Network Security Group Name\> configurata con subnet/subscriptions/ \<SubscriptionID\> /resourceGroups/ \<Resource Group name\> RG-westeurope-VNET-TomTom-Default/Providers/Microsoft. Network/virtualNetworks/ \<Virtual Network Name\> /Subnets/non \<Subnet Name\> consente la connettività in ingresso e/o in uscita richiesta. Per altre informazioni, vedere [pianificare una rete virtuale per Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)oppure contattare il supporto tecnico. "

### <a name="cause"></a>Causa

Se i gruppi di sicurezza di rete o le route definite dall'utente (UDR) controllano il traffico in ingresso verso il cluster HDInsight, assicurarsi che il cluster sia in grado di comunicare con i servizi di gestione e integrità di Azure critici.

### <a name="resolution"></a>Soluzione

Se si prevede di usare i gruppi di sicurezza di rete per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight:

- Identificare l'area di Azure che si intende usare per HDInsight e creare un elenco sicuro degli indirizzi IP per la propria area geografica. Per altre informazioni, vedere [Health and Management Services: specific Regions](./hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions).
- Identificare gli indirizzi IP richiesti da HDInsight. Per altre informazioni, vedere [indirizzi IP di gestione di HDInsight](./hdinsight-management-ip-addresses.md).
- Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight. Per i gruppi di sicurezza di rete, consentire il traffico in ingresso sulla porta 443 dagli indirizzi IP. Questa configurazione garantisce che i servizi di gestione HDInsight possano raggiungere il cluster dall'esterno della rete virtuale.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Codice di errore: installazione del cluster non è riuscita a installare i componenti in uno o più host

### <a name="error"></a>Errore

"Installazione cluster non è riuscito a installare i componenti in uno o più host. Ripetere la richiesta ".

### <a name="cause"></a>Causa 

In genere, questo errore viene generato quando si verifica un problema temporaneo o un'interruzione di Azure.

### <a name="resolution"></a>Soluzione

Controllare la pagina di [stato di Azure](https://status.azure.com) per eventuali interruzioni di Azure che potrebbero influire sulla distribuzione del cluster. Se non ci sono interruzioni, riprovare la distribuzione del cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Codice di errore: FailedToConnectWithClusterErrorCode

### <a name="error"></a>Errore

Impossibile connettersi all'endpoint di gestione cluster. Riprovare più tardi.

### <a name="cause"></a>Causa

Il servizio HDInsight non è in grado di connettersi al cluster durante il tentativo di creare il cluster

### <a name="resolution"></a>Soluzione

Se si usano i gruppi di sicurezza di rete VNet (gruppi) e le route definite dall'utente (UDR) personalizzati, verificare che il cluster sia in grado di comunicare con HDInsight Management Services. Per altre informazioni, vedere [indirizzi IP di gestione di HDInsight](./hdinsight-management-ip-addresses.md).

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Codice di errore: le distribuzioni non sono riuscite a causa di una violazione dei criteri:' risorsa ' non <Resource URI> è consentita dai criteri. Identificatori di criteri:' [{"policyAssignment": {"Name": " <Policy Name> ", "ID": "/providers/Microsoft.Management/managementGroups/ <Management Group Name> providers/Microsoft. Authorization/policyAssignments/ <Policy Name> "}, "policyDefinition": <Policy Definition>

### <a name="cause"></a>Causa

I criteri di Azure basati su sottoscrizioni possono negare la creazione di indirizzi IP pubblici. La creazione del cluster HDInsight richiede due indirizzi IP pubblici.

I criteri seguenti influiscano generalmente sulla creazione del cluster:

* Criteri che impediscono la creazione di indirizzi IP o di bilanciamenti del carico all'interno della sottoscrizione.
* Criteri che impediscono la creazione di account di archiviazione.
* Criteri che impediscono l'eliminazione di risorse di rete, ad esempio indirizzi IP o bilanciamenti del carico.

### <a name="resolution"></a>Soluzione

Eliminare o disabilitare l'assegnazione dei criteri di Azure basata sulla sottoscrizione durante la creazione del cluster HDInsight.

---

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione degli errori nella creazione del cluster, vedere [risolvere i problemi di creazione del cluster con Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md).