---
title: Domande frequenti su Azure HDInsightAzure HDInsight frequently asked questions
description: Domande frequenti su HDInsight
keywords: domande frequenti, domande frequenti
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: b05b83086cc9d8449d9517897f347b6e2685aa95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720351"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: domande frequentiAzure HDInsight: Frequently asked questions

Questo articolo fornisce le risposte ad alcune delle domande più comuni su come eseguire [Azure HDInsight.](https://azure.microsoft.com/services/hdinsight/)

## <a name="creating-or-deleting-hdinsight-clusters"></a>Creazione o eliminazione di cluster HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Come si esegue il provisioning di un cluster HDInsight?

Per esaminare i tipi di cluster HDInsight disponibili e i metodi di provisioning, vedere [Configurare i cluster in HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Come si elimina un cluster HDInsight esistente?

Per altre informazioni sull'eliminazione di un cluster quando non è più in uso, vedere [Eliminare un cluster HDInsight.](hdinsight-delete-cluster.md)

Lasciare almeno 30-60 minuti tra le operazioni di creazione ed eliminazione. In caso contrario, l'operazione potrebbe non riuscire con il seguente messaggio di errore:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Come si seleziona il numero corretto di core o nodi per il carico di lavoro?

Il numero appropriato di core e altre opzioni di configurazione dipende da vari fattori.

Per ulteriori informazioni, vedere [Pianificazione della capacità per i cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Cosa è possibile fare quando il provisioning del cluster ha esito negativo a causa di un problema di capacità?

In questa sezione vengono forniti errori comuni relativi a problemi di capacità e tecniche di attenuazione.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Errore: la distribuzione supererebbe la quota di '800'

Azure prevede un limite di quota di 800 distribuzioni per gruppo di risorse. Vengono applicate quote diverse per gruppo di risorse, sottoscrizione, account o altri ambiti. Ad esempio, la sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Se si tenta di distribuire una macchina virtuale con più core rispetto alla quantità consentita, viene visualizzato un messaggio di errore che indica che la quota è stata superata.

Per risolvere questo problema, eliminare le distribuzioni che non sono più necessarie utilizzando il portale di Azure, CLI o PowerShell.To resolve this issue, delete the deployments that are no longer needed by using the Azure portal, CLI, or PowerShell.

Per altre informazioni, vedere [Risolvere gli errori delle quote di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Errore: il numero massimo di nodi ha superato i core disponibili in questa area

La sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Se si tenta di distribuire una risorsa con più core rispetto alla quantità consentita, viene visualizzato un messaggio di errore che indica che la quota è stata superata.

Per richiedere un aumento delle quote, seguire questi passaggi:

1. Passare al [portale di Azure](https://portal.azure.com)e selezionare Guida e **supporto**.
   
1. Selezionare **Nuova richiesta di supporto**.
   
1. Nella scheda **Nozioni di base** della pagina **Nuova richiesta di supporto** fornire le informazioni seguenti:
   
   - **Tipo di problema:** Selezionare **Limiti di servizio e sottoscrizione (quote)**.
   - **Abbonamento:** Selezionare la sottoscrizione da modificare.
   - **Tipo di quota:** Selezionare **HDInsight**.

Per altre informazioni, vedere [Creare un ticket di supporto per aumentare i core](hdinsight-capacity-planning.md#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Quali sono i vari tipi di nodi in un cluster HDInsight?

I cluster di Azure HDInsight hanno tipi diversi di macchine virtuali o nodi. Ogni tipo di nodo svolge un ruolo nel funzionamento del sistema.

Per altre informazioni, vedere Tipi di risorse nei cluster di Azure HDInsight.For more information, see [Resource types in Azure HDInsight clusters.](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)

## <a name="individual-components"></a>Singoli componenti

### <a name="can-i-install-additional-components-on-my-cluster"></a>È possibile installare componenti aggiuntivi nel cluster?

Sì. Per installare componenti aggiuntivi o personalizzare la configurazione del cluster, utilizzare:

- Script durante o dopo la creazione. Gli script vengono richiamati tramite [l'azione script,](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)ovvero un'opzione di configurazione che è possibile utilizzare dal portale di Azure, dai cmdlet hdInsight di Windows PowerShell o dall'SDK di HDInsight .NET. Questa opzione di configurazione può essere usata dal portale di Azure, dai cmdlet di Windows PowerShell HDInsight o da HDInsight .NET SDK.

- [Piattaforma applicativa HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) per installare le applicazioni dell'ecosistema.

Per un elenco dei componenti supportati, vedere Quali sono i componenti e le [versioni Apache Hadoop disponibili con HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>È possibile aggiornare i singoli componenti preinstallati nel cluster?

Se si aggiornano i componenti incorporati o le applicazioni preinstallate nel cluster, la configurazione risultante non sarà supportata da Microsoft. Queste configurazioni di sistema non sono state testate da Microsoft. Provare a utilizzare una versione diversa del cluster HDInsight che potrebbe già avere la versione aggiornata del componente preinstallato.

Ad esempio, l'aggiornamento di Hive come singolo componente non è supportato. HDInsight è un servizio gestito e molti servizi sono integrati con il server Ambari e testati. L'aggiornamento di un Hive da solo causa la modifica dei file binari indicizzati di altri componenti e causa problemi di integrazione dei componenti nel cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark e Kafka possono essere eseguite nello stesso cluster HDInsight?

No, non è possibile eseguire Apache Kafka e Apache Spark sullo stesso cluster HDInsight. Creare cluster separati per Kafka e Spark per evitare problemi di contesa delle risorse.

### <a name="how-do-i-change-timezone-in-ambari"></a>Come faccio a cambiare il fuso orario in Ambari?

1. Aprire l'interfaccia utente `https://CLUSTERNAME.azurehdinsight.net`Web Ambari in , dove CLUSTERNAME è il nome del cluster.
2. Nell'angolo in alto a destra, seleziona admin Impostazioni. 

   ![Impostazioni Ambari](media/hdinsight-faq/ambari-settings.png)

3. Nella finestra Impostazioni utente selezionare il nuovo fuso orario dall'elenco a discesa Fuso orario e quindi fare clic su Salva.

   ![Impostazioni utente Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Come è possibile eseguire la migrazione dal metastore esistente ad Azure SQL Server? 

Per eseguire la migrazione da SQL Server ad Azure SQL Server, vedere Esercitazione: Eseguire la migrazione di SQL Server a un singolo database o database in pool nel database SQL di [Azure offline tramite DMS.](../dms/tutorial-sql-server-to-azure-sql.md)

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Il metastore Hive viene eliminato quando il cluster viene eliminato?

Dipende dal tipo di metastore che il cluster è configurato per utilizzare.

Per un metastore predefinito: il metastore predefinito fa parte del ciclo di vita del cluster. Quando si elimina un cluster verranno eliminati anche il metastore e i metadati corrispondenti.

Per un metastore personalizzato: il ciclo di vita del metastore non è legato al ciclo di vita di un cluster. Pertanto, è possibile creare ed eliminare cluster senza perdere i metadati. Metadati come gli schemi Hive persistono anche dopo aver eliminato e ricreato il cluster HDInsight.Metadata such as your Hive schemas persists even after you delete and re-create the HDInsight cluster.

Per altre informazioni, vedere [Use external metadata stores in Azure HDInsight (Usare archivi di metadati esterni in Azure HDInsight)](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>La migrazione di un metastore Hive esegue anche la migrazione dei criteri predefiniti del database Ranger?

No, la definizione dei criteri si trova nel database Ranger, pertanto la migrazione del database Ranger eseguirà la migrazione dei criteri.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>È possibile eseguire la migrazione di un metastore Hive da un cluster Enterprise Security Package (ESP) a un cluster non ESP e viceversa?

Sì, è possibile eseguire la migrazione di un metastore Hive da un cluster ESP a un cluster non ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Come è possibile stimare le dimensioni di un database metastore Hive?

Un metastore Hive viene usato per archiviare i metadati per le origini dati utilizzate dal server Hive. I requisiti di dimensione dipendono in parte dal numero e dalla complessità delle origini dati Hive e non possono essere stimati in anticipo. Come descritto nelle procedure consigliate per il [metastore Hive](hdinsight-use-external-metadata-stores.md#hive-metastore-best-practices), è possibile iniziare con un livello S2, che fornisce 50 DTU e 250 GB di spazio di archiviazione e, se viene visualizzato un collo di bottiglia, è possibile aumentare la scalabilità del database.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Supportate qualsiasi altro database diverso dal database SQL di Azure come metastore esterno?

No, Microsoft supporta solo il database SQL di Azure come metastore personalizzato esterno.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>È possibile condividere un metastore tra più cluster?

Sì, è possibile condividere il metastore personalizzato tra più cluster, purché utilizzino la stessa versione di HDInsight.Yes, you can share custom metastore across multiple clusters as as they are using the same version of HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Connettività e reti virtuali  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Quali sono le implicazioni del blocco delle porte 22 e 23 sulla rete?

Se si bloccano le porte 22 e la porta 23, non si avrà accesso SSH al cluster. Queste porte non vengono utilizzate dal servizio HDInsight.These ports are not used by HDInsight service.

Per altre informazioni, vedere i documenti seguenti:

- [Controllo del traffico di rete](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Proteggere il traffico in ingresso ai cluster HDInsight in una rete virtuale con endpoint privatoSecure incoming traffic to HDInsight clusters in a virtual network with private endpoint](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [Indirizzi IP di gestione HDInsightHDInsight management IP addresses](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>È possibile distribuire una macchina virtuale aggiuntiva all'interno della stessa subnet di un cluster HDInsight?

Sì, è possibile distribuire una macchina virtuale aggiuntiva all'interno della stessa subnet di un cluster HDInsight.Yes, you can deploy an additional virtual machine within the same subnet as an HDInsight cluster. Sono possibili le seguenti configurazioni:

- Nodi perimetrali: è possibile aggiungere un altro nodo perimetrale al cluster, come descritto in [Usare nodi perimetrali vuoti nei cluster Apache Hadoop in HDInsight](hdinsight-apps-use-edge-node.md).

- Nodi autonomi: è possibile aggiungere una macchina virtuale autonoma alla stessa subnet e accedere `https://<CLUSTERNAME>-int.azurehdinsight.net`al cluster da tale macchina virtuale usando l'estremità privata. Per ulteriori informazioni, vedere [Controllo del traffico](hdinsight-plan-virtual-network-deployment.md#networktraffic)di rete .

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>È consigliabile archiviare i dati nel disco locale di un nodo perimetrale?

No, l'archiviazione dei dati su un disco locale non è una buona idea. Se si verifica un errore nel nodo, tutti i dati archiviati localmente andranno persi. È consigliabile archiviare i dati nell'archiviazione di Azure Data Lake Gen2 o nell'archiviazione BLOB di Azure oppure montare una condivisione File di Azure per archiviare i dati.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>È possibile aggiungere un cluster HDInsight esistente a un'altra rete virtuale?

No, non puoi. La rete virtuale deve essere specificata al momento del provisioning. Se durante il provisioning non viene specificata alcuna rete virtuale, la distribuzione crea una rete interna non accessibile dall'esterno. Per altre informazioni, vedere Aggiungere HDInsight a una [rete virtuale esistente.](hdinsight-plan-virtual-network-deployment.md#existingvnet)

## <a name="security-and-certificates"></a>Sicurezza e certificati

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Quali sono i consigli per la protezione da malware nei cluster di Azure HDInsight?

Per informazioni sulla protezione da malware, vedere [Microsoft Antimalware per i servizi cloud di Azure e le macchine virtuali.](../security/fundamentals/antimalware.md)

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Come si crea una scheda chiave per un cluster ESP HDInsight?

Creare una scheda chiave Kerberos per il nome utente del dominio. In seguito è possibile utilizzare questa scheda chiave per eseguire l'autenticazione ai cluster remoti aggiunti al dominio senza immettere una password. Il nome di dominio è maiuscolo:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>È possibile usare un tenant di Azure Active Directory esistente per creare un cluster HDInsight con ESP?

È necessario abilitare Servizi di dominio Azure Active Directory (Azure AD DS) prima di poter creare un cluster HDInsight con ESP. Hadoop open-source si basa su Kerberos per l'autenticazione (a differenza di OAuth).

Per aggiungere macchine virtuali a un dominio, è necessario disporre di un controller di dominio. Servizi di dominio Active Directory di Azure è il controller di dominio gestito ed è considerato un'estensione di Azure Active Directory che fornisce tutti i requisiti Kerberos per creare un cluster Hadoop sicuro in modo gestito. HDInsight come servizio gestito si integra con Servizi di dominio Active Directory di Azure per garantire la sicurezza end-to-end.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>È possibile utilizzare un certificato autofirmato in una configurazione LDAP sicura di AAD-DS ed eseguire il provisioning di un cluster ESP?

L'utilizzo di un certificato emesso da un'autorità di certificazione è consigliato, ma l'utilizzo di un certificato autofirmato è supportato anche in ESP. Per altre informazioni, vedere:

- [Abilitare Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Esercitazione: Configurare LDAP sicuro per un dominio gestito di Servizi di dominio Azure Active DirectoryTutorial: Configure secure LDAP for an Azure Active Directory Domain Services managed domain](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Come posso estrarre l'attività di login mostrata in Ranger?

Per i requisiti di controllo, Microsoft consiglia di abilitare i log di Monitoraggio di Azure come descritto in Usare i log di Monitoraggio di Azure per monitorare i cluster HDInsight.For auditing requirements, Microsoft recommends enabling Azure Monitor logs as described in [Use Azure Monitor logs to monitor HDInsight clusters.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)

### <a name="can-i-disable-clamscan-on-my-cluster"></a>È possibile disabilitare Clamscan nel cluster?

Clamscan è il software antivirus che viene eseguito nel cluster HDInsight e viene usato dalla sicurezza di Azure (azsecd) per proteggere i cluster da attacchi di virus. Microsoft consiglia vivamente agli utenti di astenersi dall'apportare modifiche alla configurazione predefinita di Clamscan.

Questo processo non interferisce o toglie alcun ciclo ad altri processi. Essa cederà sempre ad altri processi. I picchi di CPU da Clamscan devono essere visualizzati solo quando il sistema è inattivo.  

Negli scenari in cui è necessario controllare la pianificazione, è possibile utilizzare la procedura seguente:In scenarios in which you must control the schedule, you can use the following steps:

1. Disattivare l'esecuzione automatica utilizzando il comando seguente:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Aggiungere un processo Cron che esegue il comando seguente come root:Add a Cron job that runs the following command as root:
   
   `/usr/local/bin/azsecd manual -s clamav`

Per ulteriori informazioni su come impostare ed eseguire un processo cron, vedere [Come configurare un processo Cron?](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Perché LLAP è disponibile nei cluster Spark ESP?
Nei cluster ESP Spark, LLAP è abilitato per motivi di sicurezza (ad esempio Apache Ranger), non per le prestazioni. È consigliabile usare macchine virtuali del nodo più grandi per supportare l'utilizzo delle risorse di LLAP (ad esempio, d13V2 minimo). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Come è possibile aggiungere altri gruppi AAD dopo la creazione di un cluster ESP?
È possibile ottenere due modi: 1- È possibile ricreare il cluster e aggiungere il gruppo aggiuntivo al momento della creazione del cluster. Se si utilizza la sincronizzazione con ambito in AAD-DS, assicurarsi che il gruppo B sia incluso nella sincronizzazione con ambito.
2- Aggiungere il gruppo come sottogruppo nidificato del gruppo precedente utilizzato per creare il cluster ESP. Ad esempio, se è stato creato `A`un cluster ESP `B` con gruppo , `A` è possibile aggiungere successivamente un gruppo come sottogruppo nidificato di e dopo circa un'ora verrà sincronizzato e disponibile automaticamente nel cluster. 

## <a name="storage"></a>Archiviazione

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>È possibile aggiungere un'archiviazione di Azure Data Lake Gen2 a un cluster HDInsight esistente come account di archiviazione aggiuntivo?

No, attualmente non è possibile aggiungere un account di archiviazione Azure Data Lake Storage Gen2 a un cluster con archiviazione BLOB come archiviazione primaria. Per ulteriori informazioni, vedere [Confrontare le opzioni di archiviazione.](hdinsight-hadoop-compare-storage-options.md)

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Come è possibile trovare l'entità servizio attualmente collegata per un account di archiviazione di Data Lake?

È possibile trovare le impostazioni **nell'accesso Data Lake Storage Gen1** nelle proprietà del cluster nel portale di Azure.You can find your settings in Data Lake Storage Gen1 access under your cluster properties in the Azure portal. Per ulteriori informazioni, vedere [Verificare l'installazione del cluster](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Come è possibile calcolare l'utilizzo di account di archiviazione e contenitori BLOB per i cluster HDInsight?

Eseguire una delle operazioni seguenti:

- [Usare PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Trovare la dimensione di */user/hive/. Trash/* cartella nel cluster HDInsight, utilizzando la seguente riga di comando:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Come è possibile configurare il controllo per l'account di archiviazione BLOB?

Per controllare gli account di archiviazione BLOB, configurare il monitoraggio usando la procedura descritta in Monitorare un account di archiviazione nel portale di Azure.To audit blob storage accounts, configure monitoring using the procedure in [Monitor a storage account in the Azure portal.](../storage/common/storage-monitor-storage-account.md) Un registro di controllo HDFS fornisce solo informazioni di controllo solo per il file system HDFS locale (hdfs://mycluster).  Non sono incluse le operazioni eseguite sull'archiviazione remota.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Come è possibile trasferire file tra un contenitore BLOB e un nodo head HDInsight?

Eseguire uno script simile al seguente script della shell nel nodo head:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Il file *filenames.txt* avrà il percorso assoluto dei file nei contenitori BLOB.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Ci sono plugin Ranger per l'archiviazione?

Attualmente, non esiste alcun plug-in Ranger per l'archiviazione BLOB e Azure Data Lake Storage Gen1 o Gen2. Per i cluster ESP, è consigliabile usare Archiviazione di Azure Data Lake, perché è possibile impostare manualmente le autorizzazioni specifiche a livello di file system usando gli strumenti HDFS. Inoltre, quando si usa Archiviazione data lake di Azure, i cluster ESP eseguiranno alcuni dei controlli di accesso al file system usando Azure Active Directory a livello di cluster. 

È possibile assegnare criteri di accesso ai dati ai gruppi di sicurezza degli utenti usando Esplora archivi di Azure.You can assign data access policies to your users' security groups by using the Azure Storage Explorer. Per altre informazioni, vedere:

- [Come si impostano le autorizzazioni per gli utenti di Azure AD per eseguire query sui dati in Data Lake Storage Gen2 usando Hive o altri servizi?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Impostare autorizzazioni a livello di file e directory usando Azure Storage Explorer con Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>È possibile aumentare lo spazio di archiviazione HDFS in un cluster senza aumentare le dimensioni del disco dei nodi di lavoro?

No, non è possibile aumentare le dimensioni del disco di qualsiasi nodo di lavoro, pertanto l'unico modo per aumentare le dimensioni del disco consiste nell'eliminare il cluster e ricrearlo con macchine virtuali di lavoro più grandi. Non usare HDFS per archiviare i dati di HDInsight, perché i dati vengono eliminati se si elimina il cluster. Archiviare invece i dati in Azure.Instead, store your data in Azure. La scalabilità verticale del cluster può anche aggiungere ulteriore capacità al cluster HDInsight.Scaling up the cluster can also add additional capacity to your HDInsight cluster.

## <a name="edge-nodes"></a>Nodi perimetrali

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>È possibile aggiungere un nodo perimetrale dopo la creazione del cluster?

cluster HDInsight o in un nuovo cluster durante la creazione del cluster. Per altre informazioni, vedere [Usare i nodi perimetrali vuoti sui cluster Apache Hadoop in HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Come è possibile connettersi a un nodo perimetrale?

Dopo aver creato un nodo perimetrale, è possibile connettersi ad esso utilizzando SSH sulla porta 22.After you create an edge node, you can connect to it by using SSH on port 22. È possibile trovare il nome del nodo perimetrale dal portale del cluster. I nomi di solito terminano con *-ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Perché gli script persistenti non vengono eseguiti automaticamente nei nodi perimetrali appena creati?

Gli script persistenti vengono utilizzati per personalizzare i nuovi nodi di lavoro aggiunti al cluster tramite operazioni di ridimensionamento. Gli script persistenti non si applicano ai nodi perimetrali.

## <a name="rest-api"></a>API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Quali sono le chiamate all'API REST per eseguire il pull di una visualizzazione query Tez dal cluster?

È possibile usare gli endpoint REST seguenti per estrarre le informazioni necessarie in formato JSON. Utilizzare le intestazioni di autenticazione di base per effettuare le richieste.

- Visualizzazione query Tez: *https:\//\<nome cluster>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- Vista Tez Dag: *https:\//\<nome del cluster>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Come è possibile recuperare i dettagli di configurazione dal cluster HDI utilizzando un utente di Azure Active Directory?

Per negoziare i token di autenticazione corretti con l'utente AAD, passare attraverso il gateway utilizzando il formato seguente:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Come si usa l'API Restful Ambari per monitorare le prestazioni di YARN?

Se si chiama il comando Curl nella stessa rete virtuale o in una rete virtuale con peered, il comando è:If you call the Curl command in the same virtual network or a peered virtual network, the command is:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Se si chiama il comando dall'esterno della rete virtuale o da una rete virtuale senza peer, il formato del comando è:If you call the command from outside the virtual network or from a non-peered virtual network, the command format is:

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
> Curl ti chiederà una password. È necessario immettere una password valida per il nome utente di accesso al cluster.

## <a name="billing"></a>Fatturazione

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Quanto costa distribuire un cluster HDInsight?

Per altre informazioni sui prezzi e sulle domande frequenti relative alla fatturazione, vedere la pagina Prezzi di [Azure HDInsight.For](https://azure.microsoft.com/pricing/details/hdinsight/) more information about pricing and FAQ related to billing, see the Azure HDInsight Pricing page.

### <a name="when-does-hdinsight-billing-start--stop"></a>Quando viene avviata la fatturazione HDInsight & interrotta?

La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione è ripartito proporzionalmente al minuto.

### <a name="how-do-i-cancel-my-subscription"></a>Come posso cancellare il mio abbonamento?

Per informazioni su come annullare la sottoscrizione, vedere Annullare la sottoscrizione di Azure.For information about how to cancel your subscription, see [Cancel your Azure subscription.](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Per gli abbonamenti con pagamento in base al pagamento, cosa accade dopo l'annullamento dell'abbonamento?

Per informazioni sull'abbonamento dopo l'annullamento, vedere [Cosa succede dopo l'annullamento dell'abbonamento?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Perché la versione Hive viene visualizzata come 1.2.1000 anziché 2.1 nell'interfaccia utente di Ambari anche se si esegue un cluster HDInsight 3.6?

Anche se solo 1.2 viene visualizzato nell'interfaccia utente di Ambari, HDInsight 3.6 contiene sia Hive 1.2 che Hive 2.1.

## <a name="other-faq"></a>Altre FAQ

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Cosa offre HDInsight in termini di funzionalità di elaborazione del flusso in tempo reale?

Per informazioni sulle funzionalità di integrazione dell'elaborazione del flusso in Azure HDInsight, vedere Scelta di una tecnologia di elaborazione del flusso in Azure.For information about the integration capabilities of stream processing in Azure HDInsight, see [Choosing a stream processing technology in Azure.](/azure/architecture/data-guide/technology-choices/stream-processing)

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Esiste un modo per terminare dinamicamente il nodo head del cluster quando il cluster è inattivo per un periodo specifico?

Non è possibile eseguire questa operazione con i cluster HDInsight.You can't do this with HDInsight clusters. È possibile usare Azure Data Factory per questi scenari.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Quali offerte di conformità offrono HDInsight?

Per informazioni sulla conformità, vedere il [Centro protezione Microsoft](https://www.microsoft.com/trust-center) e [panoramica della conformità di Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).
