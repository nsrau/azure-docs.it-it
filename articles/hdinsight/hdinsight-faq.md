---
title: Domande frequenti su Azure HDInsight
description: Domande frequenti su HDInsight
keywords: domande frequenti, domande frequenti
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 392329d254615406204d67a4e135b6857e13a57d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192553"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: domande frequenti

Questo articolo fornisce le risposte ad alcune delle domande più comuni su come eseguire [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Creazione o eliminazione di cluster HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Ricerca per categorie effettuare il provisioning di un cluster HDInsight?

Per esaminare i tipi di cluster HDInsight e i metodi di provisioning, vedere [configurare i cluster in HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Ricerca per categorie eliminare un cluster HDInsight esistente?

Per altre informazioni sull'eliminazione di un cluster quando non è più in uso, vedere [eliminare un cluster HDInsight](hdinsight-delete-cluster.md).

Provare a lasciare da almeno 30 a 60 minuti tra le operazioni di creazione ed eliminazione. In caso contrario, l'operazione potrebbe non riuscire con il seguente messaggio di errore:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Ricerca per categorie selezionare il numero corretto di core o nodi per il carico di lavoro?

Il numero appropriato di core e altre opzioni di configurazione dipendono da diversi fattori.

Per altre informazioni, vedere [pianificazione della capacità per i cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Quali sono i vari tipi di nodi in un cluster HDInsight?

Vedere [tipi di risorse nei cluster HDInsight di Azure](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Singoli componenti

### <a name="can-i-install-additional-components-on-my-cluster"></a>È possibile installare componenti aggiuntivi nel cluster?

Sì. Per installare componenti aggiuntivi o personalizzare la configurazione del cluster, usare:

- Script durante o dopo la creazione. Gli script vengono richiamati tramite [azione script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux). Azione script è un'opzione di configurazione che è possibile usare dal portale di Azure cmdlet di Windows PowerShell HDInsight o HDInsight .NET SDK. Questa opzione di configurazione può essere usata dai cmdlet di Windows PowerShell portale di Azure, HDInsight o HDInsight .NET SDK.

- [Piattaforma applicativa HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) per installare le applicazioni.

Per un elenco dei componenti supportati [, vedere quali sono i componenti e le versioni di Apache Hadoop disponibili con HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>È possibile aggiornare i singoli componenti preinstallati nel cluster?

Se si esegue l'aggiornamento di componenti predefiniti o di applicazioni preinstallate nel cluster, la configurazione risultante non sarà supportata da Microsoft. Queste configurazioni di sistema non sono state testate da Microsoft. Provare a usare una versione diversa del cluster HDInsight in cui potrebbe essere già installata la versione aggiornata del componente.

Ad esempio, l'aggiornamento di hive come singolo componente non è supportato. HDInsight è un servizio gestito e molti servizi sono integrati con il server Ambari e testato. Se si aggiorna un hive in modo autonomo, i file binari indicizzati di altri componenti cambiano e causeranno problemi di integrazione dei componenti nel cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark e Kafka possono essere eseguiti nello stesso cluster HDInsight?

No, non è possibile eseguire Apache Kafka e Apache Spark nello stesso cluster HDInsight. Creare cluster distinti per Kafka e Spark per evitare problemi di conflitto di risorse.

### <a name="how-do-i-change-timezone-in-ambari"></a>Ricerca per categorie modificare il fuso orario in Ambari?

1. Aprire l'interfaccia utente Web di `https://CLUSTERNAME.azurehdinsight.net`Ambari all'indirizzo, dove clustername è il nome del cluster.
2. Nell'angolo in alto a destra selezionare admin | Impostazioni. 

   ![Impostazioni Ambari](media/hdinsight-faq/ambari-settings.png)

3. Nella finestra impostazioni utente selezionare il nuovo fuso orario dall'elenco a discesa fuso orario, quindi fare clic su Salva.

   ![Impostazioni utente Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Come è possibile eseguire la migrazione dal Metastore esistente ad Azure SQL Server? 

Per eseguire la migrazione da SQL Server ad Azure SQL Server, vedere [esercitazione: eseguire la migrazione di SQL Server in un database singolo o in pool nel database SQL di Azure offline con DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Il metastore Hive eliminato quando il cluster viene eliminato?

Dipende dal tipo di Metastore configurato per l'uso da parte del cluster.

Per un Metastore predefinito, il Metastore predefinito fa parte del ciclo di vita del cluster. Quando si elimina un cluster verranno eliminati anche il metastore e i metadati corrispondenti.

Per un Metastore personalizzato: il ciclo di vita del Metastore non è associato al ciclo di vita di un cluster. È quindi possibile creare ed eliminare i cluster senza perdere i metadati. I metadati, ad esempio gli schemi hive, vengono mantenuti anche dopo l'eliminazione e la ricreazione del cluster HDInsight.

Per altre informazioni, vedere [Use external metadata stores in Azure HDInsight (Usare archivi di metadati esterni in Azure HDInsight)](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>La migrazione di un metastore Hive esegue anche la migrazione dei criteri predefiniti del database Ranger?

No, la definizione dei criteri si trova nel database Ranger, quindi la migrazione del database Ranger eseguirà la migrazione dei criteri.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>È possibile eseguire la migrazione di un metastore Hive da un cluster di Enterprise Security Package (ESP) a un cluster non ESP e viceversa?

Sì, è possibile eseguire la migrazione di un metastore Hive da un ESP a un cluster non ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Come è possibile stimare le dimensioni di un database di metastore Hive?

Viene usato un metastore Hive per archiviare i metadati per le origini dati usate dal server hive. I requisiti di dimensione dipendono in parte dal numero e dalla complessità delle origini dati hive. Questi elementi non possono essere stimati in anticipo. Come descritto in [Metastore hive linee guida](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines), è possibile iniziare con un livello S2. Il livello fornisce 50 DTU e 250 GB di spazio di archiviazione e, se si verifica un collo di bottiglia, aumentare il livello del database.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Sono supportati altri database diversi da quelli del database SQL di Azure come Metastore esterno?

No, Microsoft supporta solo il database SQL di Azure come Metastore personalizzato esterno.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>È possibile condividere un Metastore tra più cluster?

Sì, è possibile condividere il Metastore personalizzato tra più cluster, purché stia usando la stessa versione di HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Connettività e reti virtuali  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Quali sono le implicazioni del blocco delle porte 22 e 23 sulla rete?

Se si bloccano le porte 22 e 23, non si avrà accesso SSH al cluster. Queste porte non vengono usate dal servizio HDInsight.

Per altre informazioni, vedere i documenti seguenti:

- [Controllo del traffico di rete](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Proteggere il traffico in ingresso nei cluster HDInsight in una rete virtuale con endpoint privato](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [Indirizzi IP di gestione di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>È possibile distribuire una macchina virtuale aggiuntiva all'interno della stessa subnet di un cluster HDInsight?

Sì, è possibile distribuire una macchina virtuale aggiuntiva all'interno della stessa subnet di un cluster HDInsight. Sono possibili le configurazioni seguenti:

- Nodi perimetrali: è possibile aggiungere un altro nodo perimetrale al cluster, come descritto in [usare nodi perimetrali vuoti in Apache Hadoop cluster in HDInsight](hdinsight-apps-use-edge-node.md).

- Nodi autonomi: è possibile aggiungere una macchina virtuale autonoma alla stessa subnet e accedere al cluster da tale macchina virtuale usando l'endpoint privato `https://<CLUSTERNAME>-int.azurehdinsight.net`. Per ulteriori informazioni, vedere [controllo del traffico di rete](hdinsight-plan-virtual-network-deployment.md#networktraffic).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>È necessario archiviare i dati nel disco locale di un nodo perimetrale?

No, non è consigliabile archiviare i dati su un disco locale. Se il nodo ha esito negativo, tutti i dati archiviati in locale andranno persi. Si consiglia di archiviare i dati in Azure Data Lake Storage Gen2 o nell'archiviazione BLOB di Azure oppure montando una condivisione File di Azure per archiviare i dati.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>È possibile aggiungere un cluster HDInsight esistente a un'altra rete virtuale?

No, non puoi. La rete virtuale deve essere specificata al momento del provisioning. Se durante il provisioning non viene specificata alcuna rete virtuale, la distribuzione crea una rete interna non accessibile dall'esterno. Per altre informazioni, vedere [aggiungere HDInsight a una rete virtuale esistente](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Sicurezza e certificati

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Quali sono i consigli per la protezione da malware nei cluster HDInsight di Azure?

Per informazioni su malware Protection, vedere [Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Ricerca per categorie creare un keytab per un cluster ESP HDInsight?

Creare un keytab Kerberos per il nome utente del dominio. In seguito è possibile usare questo keytab per eseguire l'autenticazione in cluster remoti aggiunti a un dominio senza immettere una password. Il nome di dominio è maiuscolo:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>È possibile usare un tenant di Azure Active Directory esistente per creare un cluster HDInsight con ESP?

Abilitare Azure Active Directory Domain Services (Azure AD DS) prima di poter creare un cluster HDInsight con ESP. Hadoop open source si basa su Kerberos per l'autenticazione (in contrapposizione a OAuth).

Per aggiungere macchine virtuali a un dominio, è necessario disporre di un controller di dominio. Azure AD DS è il controller di dominio gestito ed è considerata un'estensione di Azure Active Directory. Azure AD DS fornisce tutti i requisiti Kerberos per la creazione di un cluster Hadoop protetto in modo gestito. HDInsight come servizio gestito si integra con Azure AD DS per fornire sicurezza.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>È possibile usare un certificato autofirmato in una configurazione LDAP sicura di AAD-DS ed effettuare il provisioning di un cluster ESP?

È consigliabile usare un certificato emesso da un'autorità di certificazione. Tuttavia, l'uso di un certificato autofirmato è supportato anche in ESP. Per altre informazioni, vedi:

- [Abilitare Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Esercitazione: Configurare LDAP sicuro per un dominio gestito di Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Come è possibile effettuare il pull delle attività di accesso visualizzate in Ranger?

Per i requisiti di controllo, Microsoft consiglia di abilitare i log di monitoraggio di Azure come descritto in [usare i log di monitoraggio di Azure per monitorare i cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>È possibile disabilitare `Clamscan` nel cluster?

`Clamscan`è il software antivirus in esecuzione nel cluster HDInsight e viene usato dalla sicurezza di Azure (azsecd) per proteggere i cluster da attacchi di virus. Microsoft consiglia vivamente agli utenti di evitare di apportare modifiche alla configurazione predefinita `Clamscan` .

Questo processo non interferisce né elimina i cicli da altri processi. Verrà sempre restituito ad altri processi. I picchi di CPU `Clamscan` da devono essere visualizzati solo quando il sistema è inattivo.  

Negli scenari in cui è necessario controllare la pianificazione, è possibile eseguire i passaggi seguenti:

1. Disabilitare l'esecuzione automatica usando il comando seguente:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Aggiungere un processo cron che esegue il comando seguente come radice:
   
   `/usr/local/bin/azsecd manual -s clamav`

Per altre informazioni su come configurare ed eseguire un processo cron, vedere [ricerca per categorie configurare un processo cron](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job).

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Perché LLAP è disponibile nei cluster ESP Spark?
LLAP è abilitato per motivi di sicurezza (Apache Ranger), non per le prestazioni. Usare macchine virtuali con nodi di dimensioni maggiori per gestire l'utilizzo delle risorse di LLAP (ad esempio, D13V2 minimo). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Come è possibile aggiungere altri gruppi AAD dopo la creazione di un cluster ESP?
Esistono due modi per raggiungere questo obiettivo: 1: è possibile ricreare il cluster e aggiungere il gruppo aggiuntivo al momento della creazione del cluster. Se si usa la sincronizzazione con ambito in AAD-DS, verificare che il gruppo B sia incluso nella sincronizzazione con ambito.
2-aggiungere il gruppo come sottogruppo annidato del gruppo precedente usato per creare il cluster ESP. Se, ad esempio, è stato creato un cluster ESP con `A`gruppo, in un secondo momento è `B` possibile aggiungere gruppo come sottogruppo `A` annidato di e dopo circa un'ora verrà sincronizzato e disponibile automaticamente nel cluster. 

## <a name="storage"></a>Archiviazione

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>È possibile aggiungere un Azure Data Lake Storage Gen2 a un cluster HDInsight esistente come account di archiviazione aggiuntivo?

No, attualmente non è possibile aggiungere un account di archiviazione Azure Data Lake Storage Gen2 a un cluster con archiviazione BLOB come risorsa di archiviazione primaria. Per altre informazioni, vedere [confrontare le opzioni di archiviazione](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Come è possibile trovare l'entità servizio attualmente collegata per un account di archiviazione Data Lake?

È possibile trovare le impostazioni in **Data Lake storage Gen1 accedere** alle proprietà del cluster nel portale di Azure. Per ulteriori informazioni, vedere [verificare la configurazione del cluster](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Come è possibile calcolare l'utilizzo di account di archiviazione e contenitori BLOB per i cluster HDInsight?

Eseguire una di queste azioni:

- [Usare PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Trovare le dimensioni di */User/hive/. Cestino/* cartella nel cluster HDInsight usando la riga di comando seguente:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Come è possibile configurare il controllo per l'account di archiviazione BLOB?

Per controllare gli account di archiviazione BLOB, configurare il monitoraggio usando la procedura in [monitorare un account di archiviazione nel portale di Azure](../storage/common/storage-monitor-storage-account.md). Un log HDFS-audit fornisce solo le informazioni di controllo solo per il file System HDFS locale (hdfs://mycluster).  Non include le operazioni eseguite sull'archiviazione remota.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Come è possibile trasferire i file tra un contenitore BLOB e un nodo head di HDInsight?

Eseguire uno script simile allo script della Shell seguente sul nodo head:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Il file *nome file. txt* avrà il percorso assoluto dei file nei contenitori BLOB.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Sono disponibili plug-in Ranger per l'archiviazione?

Attualmente non esiste alcun plug-in Ranger per l'archiviazione BLOB e Azure Data Lake Storage Gen1 o Gen2. Per i cluster ESP è necessario usare Azure Data Lake Storage. È possibile impostare almeno le autorizzazioni con granularità fine manualmente a livello di file system usando gli strumenti HDFS. Inoltre, quando si usa Azure Data Lake Storage, i cluster ESP eseguiranno alcune delle file system il controllo degli accessi usando Azure Active Directory a livello di cluster. 

È possibile assegnare criteri di accesso ai dati ai gruppi di sicurezza degli utenti usando il Azure Storage Explorer. Per altre informazioni, vedi:

- [Ricerca per categorie impostare le autorizzazioni per consentire agli utenti Azure AD di eseguire query sui dati in Data Lake Storage Gen2 usando hive o altri servizi?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Impostare autorizzazioni a livello di file e directory usando Azure Storage Explorer con Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>È possibile aumentare lo spazio di archiviazione HDFS in un cluster senza aumentare le dimensioni del disco dei nodi del ruolo di lavoro?

No. Non è possibile aumentare le dimensioni del disco di qualsiasi nodo di lavoro. Quindi, l'unico modo per aumentare le dimensioni del disco è eliminare il cluster e ricrearlo con macchine virtuali di lavoro più grandi. Non usare HDFS per archiviare i dati HDInsight, perché i dati vengono eliminati se si elimina il cluster. In alternativa, archiviare i dati in Azure. La scalabilità verticale del cluster può anche aggiungere capacità aggiuntiva al cluster HDInsight.

## <a name="edge-nodes"></a>Nodi perimetrali

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>È possibile aggiungere un nodo perimetrale dopo la creazione del cluster?

Vedere [usare nodi perimetrali vuoti in Apache Hadoop cluster in HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Come è possibile connettersi a un nodo perimetrale?

Dopo aver creato un nodo perimetrale, è possibile connettersi a esso tramite SSH sulla porta 22. È possibile trovare il nome del nodo perimetrale dal portale del cluster. I nomi di solito terminano con *-ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Perché gli script salvati in modo permanente non vengono eseguiti automaticamente nei nodi perimetrali appena creati?

Usare gli script salvati in modo permanente per personalizzare i nuovi nodi del ruolo di lavoro aggiunti al cluster tramite operazioni di ridimensionamento. Gli script salvati in modo permanente non si applicano ai nodi perimetrali.

## <a name="rest-api"></a>API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Quali sono le chiamate API REST per eseguire il pull di una visualizzazione di query Tez dal cluster?

È possibile usare gli endpoint REST seguenti per eseguire il pull delle informazioni necessarie in formato JSON. Usare le intestazioni di autenticazione di base per eseguire le richieste.

- `Tez Query View`: *https:\//\<nome del cluster>. azurehdinsight.NET/WS/V1/Timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https:\//\<nome del cluster>. azurehdinsight.NET/WS/V1/Timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Ricerca per categorie recuperare i dettagli di configurazione dal cluster HDI usando un utente Azure Active Directory?

Per negoziare i token di autenticazione appropriati con l'utente di AAD, passare attraverso il gateway usando il formato seguente:

* https://`<cluster dnsname>`. azurehdinsight.NET/API/V1/Clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Ricerca per categorie usare l'API RESTful Ambari per monitorare le prestazioni di YARN?

Se si chiama il comando curl nella stessa rete virtuale o in una rete virtuale con peering, il comando è:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Se si chiama il comando dall'esterno della rete virtuale o da una rete virtuale senza peering, il formato del comando è:

- Per un cluster non ESP:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Per un cluster ESP:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl richiederà una password. È necessario immettere una password valida per il nome utente di accesso del cluster.

## <a name="billing"></a>Fatturazione

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Quanto costa distribuire un cluster HDInsight?

Per altre informazioni sui prezzi e le domande frequenti relative alla fatturazione, vedere la pagina dei [prezzi di Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) .

### <a name="when-does-hdinsight-billing-start--stop"></a>Quando viene avviata la fatturazione di HDInsight & arrestare?

La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione è pro-rated al minuto.

### <a name="how-do-i-cancel-my-subscription"></a>Ricerca per categorie annullare l'abbonamento?

Per informazioni su come annullare la sottoscrizione, vedere [annullare la sottoscrizione di Azure](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Per le sottoscrizioni con pagamento in base al consumo, cosa accade dopo aver annullato la sottoscrizione?

Per informazioni sulla sottoscrizione dopo che è stata annullata, vedere [che cosa accade dopo l'annullamento della sottoscrizione?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>Perché la versione di hive viene visualizzata come 1.2.1000 anziché 2,1 nell'interfaccia utente di Ambari anche se si esegue un cluster HDInsight 3,6?

Sebbene solo 1,2 venga visualizzato nell'interfaccia utente di Ambari, HDInsight 3,6 contiene sia hive 1,2 che hive 2,1.

## <a name="other-faq"></a>Altre domande frequenti

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>Che cosa offre HDInsight per le funzionalità di elaborazione dei flussi in tempo reale?

Per informazioni sulle funzionalità di integrazione dell'elaborazione dei flussi, vedere [scelta di una tecnologia di elaborazione dei flussi in Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Esiste un modo per terminare in modo dinamico il nodo head del cluster quando il cluster è inattivo per un periodo specifico?

Non è possibile eseguire questa azione con i cluster HDInsight. È possibile utilizzare Azure Data Factory per questi scenari.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Quali offerte di conformità sono offerte da HDInsight?

Per informazioni sulla conformità, vedere il [Centro protezione Microsoft](https://www.microsoft.com/trust-center) e la [panoramica della conformità Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).
