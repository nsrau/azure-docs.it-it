---
title: Azure HDInsight creare un cluster-error Dictionary
description: Informazioni su come risolvere gli errori che si verificano durante la creazione di cluster HDInsight di Azure
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: b0dc974185ad616d57327e9cc3743db9ecb20e54
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302730"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: errori di creazione del cluster

Questo articolo descrive le soluzioni per gli errori che si possono incontrare durante la creazione di cluster.

> [!NOTE]
> I primi tre errori descritti in questo articolo sono errori di convalida. Possono verificarsi quando un prodotto Azure HDInsight usa la classe **CsmDocument_2_0** .

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument ' CsmDocument_2_0' non ha superato la convalida

### <a name="error"></a>Errore

"Impossibile accedere al percorso dell'azione di script:\<URL azione SCRIPT\>"

#### <a name="error-message"></a>Messaggio di errore

"Il server remoto ha restituito un errore: (404) non trovato".

### <a name="cause"></a>Causa

Il servizio HDInsight non è in grado di accedere all'URL dell'azione script fornito come parte della richiesta di creazione del cluster. Il servizio riceve il messaggio di errore precedente quando tenta di accedere all'azione di script.

### <a name="resolution"></a>Risoluzione

- Per un URL HTTP o HTTPS, verificare l'URL tentando di accedervi da una finestra del browser in incognito.
- Per un URL WASB, assicurarsi che lo script esista nell'account di archiviazione fornito nella richiesta. Assicurarsi anche che la chiave di archiviazione per questo account di archiviazione sia corretta.
- Per un URL ADLS, assicurarsi che lo script esista nell'account di archiviazione.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument ' CsmDocument_2_0' non ha superato la convalida

### <a name="error"></a>Errore

"Impossibile accedere al percorso dell'azione di script: \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>Messaggio di errore

"L'URI dello script specificato \<SCRIPT_URI\> si trova in ADLS, ma questo cluster non contiene l'entità di archiviazione di data Lake"

### <a name="cause"></a>Causa

Il servizio HDInsight non è in grado di accedere all'URL dell'azione script fornito come parte della richiesta di creazione del cluster. Il servizio riceve il messaggio di errore precedente quando tenta di accedere all'azione di script.

### <a name="resolution"></a>Risoluzione

Aggiungere l'account Azure Data Lake Storage generazione 1 corrispondente al cluster. Aggiungere inoltre l'entità servizio che accede all'account Data Lake Storage generazione 1 al cluster.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Codice di errore: DeploymentDocument ' CsmDocument_2_0' non ha superato la convalida

### <a name="error"></a>Errore

"La dimensione della macchina virtuale '\<CUSTOMER_SPECIFIED_VM_SIZE\>' specificato nella richiesta non è valida o non è supportata per il ruolo '\<ROLE\>'. I valori validi sono: \<VALID_VM_SIZE_FOR_ROLE\>".

### <a name="cause"></a>Causa

La dimensione della macchina virtuale specificata non è consentita per il ruolo. Questo errore può verificarsi perché il valore delle dimensioni della macchina virtuale non funziona come previsto o non è adatto per il ruolo del computer.

### <a name="resolution"></a>Risoluzione

Il messaggio di errore elenca i valori validi per le dimensioni della macchina virtuale. Selezionare uno di questi valori e riprovare la richiesta di creazione del cluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Codice di errore: InvalidVirtualNetworkId  

### <a name="error"></a>Errore

"VirtualNetworkId non è valido. VirtualNetworkId "\<USER_VIRTUALNETWORKID\>" * "

### <a name="cause"></a>Causa

Il valore **VirtualNetworkId** specificato durante la creazione del cluster non è nel formato corretto.

### <a name="resolution"></a>Risoluzione

Verificare che i valori di **VirtualNetworkId** e subnet siano nel formato corretto. Per ottenere il valore di **VirtualNetworkId** :

1. Accedere al portale di Azure.
1. Selezionare la rete virtuale.
1. Selezionare la voce di menu **Proprietà** . Il valore della proprietà **resourceId** è il valore **VirtualNetworkId** .

Di seguito è riportato un esempio di ID rete virtuale:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Codice di errore: CustomizationFailedErrorCode

### <a name="error"></a>Errore

"Distribuzione del cluster non riuscita a causa di un errore nell'azione script personalizzata. Azioni non riuscite: \<SCRIPT_NAME\>, passare all'interfaccia utente di Ambari per eseguire ulteriormente il debug dell'errore. "

### <a name="cause"></a>Causa

Lo script personalizzato fornito durante la richiesta create cluster viene eseguito dopo che il cluster è stato distribuito correttamente. Questo codice di errore indica che si è verificato un errore durante l'esecuzione dello script personalizzato denominato \<SCRIPT_NAME\>.

### <a name="resolution"></a>Risoluzione

Poiché lo script è lo script personalizzato, è consigliabile risolvere il problema ed eseguire nuovamente lo script, se necessario. Per risolvere gli errori di script, esaminare i log nella cartella/var/lib/Ambari-Agent/*. In alternativa, aprire la pagina **operazioni** nell'interfaccia utente di Ambariri, quindi selezionare l'operazione **run_customscriptaction** per visualizzare i dettagli dell'errore.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Codice di errore: InvalidDocumentErrorCode

### <a name="error"></a>Errore

"La versione dello schema \<META_STORE_TYPE\> Metastore \<METASTORE_MAJOR_VERSION\> nel database \<database_name\> è incompatibile con la versione del cluster \<CLUSTER_VERSION\>"

### <a name="cause"></a>Causa

Il Metastore personalizzato è incompatibile con la versione selezionata del cluster HDInsight. Attualmente, i cluster HDInsight 4,0 supportano solo Metastore versione 3,0 e versioni successive, mentre i cluster HDInsight 3,6 non supportano il Metastore versione 3,0 e versioni successive.

### <a name="resolution"></a>Risoluzione

Usare solo le versioni del Metastore supportate dalla versione del cluster HDInsight. Se non si specifica un Metastore personalizzato, HDInsight crea internamente un Metastore e quindi lo elimina dopo l'eliminazione del cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Codice di errore: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Errore

"Impossibile connettersi all'endpoint di gestione cluster per eseguire un'operazione di ridimensionamento. Verificare che le regole di sicurezza di rete non blocchino l'accesso esterno al cluster e che l'interfaccia utente di gestione cluster (Ambari) possa accedere correttamente. "

### <a name="cause"></a>Causa

Una regola del firewall nel gruppo di sicurezza di rete (NSG) blocca la comunicazione del cluster con i servizi critici di gestione e integrità di Azure.

### <a name="resolution"></a>Risoluzione

Se si prevede di usare i gruppi di sicurezza di rete per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight:

- Identificare l'area di Azure che si intende usare per HDInsight.
- Identificare gli indirizzi IP richiesti da HDInsight. Per altre informazioni, vedere [Indirizzi IP di gestione di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight.
  - Per i gruppi di sicurezza di rete, consentire il traffico in ingresso sulla porta 443 dagli indirizzi IP. Questa configurazione garantisce che i servizi di gestione HDInsight possano raggiungere il cluster dall'esterno della rete virtuale.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Codice di errore: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Errore

"L'identità gestita non dispone delle autorizzazioni per l'account di archiviazione. Verificare che il ruolo "proprietario dati BLOB di archiviazione" sia assegnato all'identità gestita per l'account di archiviazione. Archiviazione:/subscriptions/\<ID sottoscrizione\>/resourceGroups/\< nome del gruppo di risorse\>/providers/Microsoft.Storage/storageAccounts/\<nome dell'account di archiviazione\>, identità gestita:/subscriptions/\<ID sottoscrizione\>/resourceGroups//\< nome del gruppo di risorse\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<nome identità gestita dall'utente\>"

### <a name="cause"></a>Causa

Non sono state fornite le autorizzazioni necessarie per gestire l'identità. L'identità gestita assegnata dall'utente non ha il ruolo Collaboratore archiviazione BLOB nell'account di archiviazione Azure Data Lake Storage Gen2.

### <a name="resolution"></a>Risoluzione

1. Aprire il portale di Azure.
1. Passare all'account di archiviazione.
1. Controllare il **controllo di accesso (IAM)** .
1. Verificare che all'utente sia assegnato il ruolo di collaboratore dei dati BLOB di archiviazione o il ruolo di proprietario dei dati BLOB di archiviazione.

Per altre informazioni, vedere [configurare le autorizzazioni per l'identità gestita nell'account data Lake storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Codice di errore: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Errore

"Regole di sicurezza nel gruppo di sicurezza di rete/subscriptions/\<SubscriptionID\>/resourceGroups/< nome del gruppo di risorse\> Default/Providers/Microsoft. Network/networkSecurityGroups/\<nome del gruppo di sicurezza di rete\> configurato con subnet/subscriptions/\<SubscriptionID\>/resourceGroups/\<nome del gruppo di risorse\> RG-westeurope-vnet-tomtom-default/Providers/Microsoft. Network/virtualNetworks/\<Virtual Nome di rete\>/Subnets/\<nome subnet\> non consente la connettività in ingresso e/o in uscita richiesta. Per altre informazioni, vedere [pianificare una rete virtuale per Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)oppure contattare il supporto tecnico. "

### <a name="cause"></a>Causa

Se i gruppi di sicurezza di rete o le route definite dall'utente (UDR) controllano il traffico in ingresso verso il cluster HDInsight, assicurarsi che il cluster sia in grado di comunicare con i servizi di gestione e integrità di Azure critici.

### <a name="resolution"></a>Risoluzione

Se si prevede di usare i gruppi di sicurezza di rete per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight:

- Identificare l'area di Azure che si intende usare per HDInsight e creare un elenco sicuro degli indirizzi IP per la propria area geografica. Per altre informazioni, vedere [Health and Management Services: specific Regions](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Identificare gli indirizzi IP richiesti da HDInsight. Per altre informazioni, vedere [indirizzi IP di gestione di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight. Per i gruppi di sicurezza di rete, consentire il traffico in ingresso sulla porta 443 dagli indirizzi IP. Questa configurazione garantisce che i servizi di gestione HDInsight possano raggiungere il cluster dall'esterno della rete virtuale.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Codice di errore: installazione del cluster non è riuscita a installare i componenti in uno o più host

###  <a name="error"></a>Errore

"Installazione cluster non è riuscito a installare i componenti in uno o più host. Ripetere la richiesta ".

### <a name="cause"></a>Causa 

In genere, questo errore viene generato quando si verifica un problema temporaneo o un'interruzione di Azure.

### <a name="resolution"></a>Risoluzione

Controllare la pagina di [stato di Azure](https://status.azure.com) per eventuali interruzioni di Azure che potrebbero influire sulla distribuzione del cluster. Se non ci sono interruzioni, riprovare la distribuzione del cluster.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione degli errori nella creazione del cluster, vedere [risolvere i problemi di creazione del cluster con Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
