---
title: Azure HDInsight-domande frequenti
description: Viene fornita una panoramica di Azure HDInsight
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: v-miegge
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4accd8c2f58dd89fb5f918ca6e58e71caeaed57f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212747"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Domande frequenti

Questo articolo fornisce le risposte ad alcune delle domande più comuni su come eseguire [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Creazione o eliminazione di cluster HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Ricerca per categorie effettuare il provisioning di un cluster HDInsight?

Per esaminare i tipi di cluster HDInsight disponibili e i metodi di provisioning, vedere [configurare i cluster in HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Ricerca per categorie eliminare un cluster HDInsight esistente?

Per altre informazioni sull'eliminazione di un cluster quando non è più in uso, vedere [eliminare un cluster HDInsight usando il browser, PowerShell o l'interfaccia della](https://docs.microsoft.com/azure/hdinsight/hdinsight-delete-cluster)riga di comando di Azure. Si consiglia di disporre di almeno 30 a 60 minuti tra l'operazione di creazione ed eliminazione in un singolo cluster. In caso contrario, l'operazione potrebbe non riuscire e restituire il messaggio di errore seguente:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Ricerca per categorie selezionare il numero corretto di core o nodi per il carico di lavoro?

Il numero appropriato di core e altre opzioni di configurazione dipendono da diversi fattori.

Per altre informazioni, vedere [pianificazione della capacità per i cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Cosa è possibile fare quando il provisioning del cluster ha esito negativo a causa di un problema di capacità?

In questa sezione sono disponibili gli errori e le tecniche di mitigazione comuni relativi alla capacità.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Errore: La distribuzione supererà la quota di ' 800'

Azure prevede un limite di quota di 800 distribuzioni per gruppo di risorse. Si applicano quote diverse per gruppo di risorse, sottoscrizione, account o per altri ambiti. Ad esempio, la sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Se si prova a distribuire una macchina virtuale con un numero di core superiore a quello consentito, viene visualizzato un messaggio di errore che indica che la quota è stata superata.

Per risolvere questo problema, eliminare le distribuzioni che non sono più necessarie usando il portale di Azure, l'interfaccia della riga di comando o PowerShell.

Per altre informazioni, vedere [Risolvere gli errori delle quote di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Errore: Il nodo massimo supera i core disponibili in questa area

La sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Se si prova a distribuire una risorsa con un numero di core superiore a quello consentito, viene visualizzato un messaggio di errore che indica che la quota è stata superata.

Per richiedere un aumento delle quote, seguire questi passaggi:

1. Passare alla portale di Azure e selezionare Guida e supporto.
2. Selezionare nuova richiesta di supporto.
3. Nella pagina nuova richiesta di supporto selezionare le opzioni seguenti nella scheda nozioni di base:

   * Tipo di problema: Limiti del servizio e della sottoscrizione (quote)
   * Sottoscrizione: Sottoscrizione che si desidera modificare
   * Tipo di quota: HDInsight

Per altre informazioni, vedere [creare un ticket di supporto per aumentare i core](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Quali sono i vari tipi di nodi in un cluster HDInsight?

I cluster Azure HDInsight hanno tipi diversi di macchine virtuali o nodi. Ogni tipo di nodo svolge un ruolo nel funzionamento del sistema.

Per altre informazioni, vedere [tipi di risorse nei cluster HDInsight di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-virtual-network-architecture#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Singoli componenti

### <a name="can-i-install-additional-components-on-my-cluster"></a>È possibile installare componenti aggiuntivi nel cluster?

Sì, è possibile installare componenti aggiuntivi o personalizzare la configurazione del cluster usando uno dei metodi seguenti:

* Utilizzo di script durante o dopo la creazione. Tali script vengono richiamati tramite un' [azione di script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux). Questa opzione di configurazione può essere usata dai cmdlet di Windows PowerShell portale di Azure, HDInsight o HDInsight .NET SDK. 
* Usare sudo o altri metodi dopo il provisioning del cluster.
* Uso della [piattaforma applicativa HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) per installare le applicazioni dell'ecosistema.

Tuttavia, i team supporto tecnico Microsoft possono offrire supporto solo per le situazioni seguenti:

* Problemi o errori che si verificano durante il caricamento dello script. Gli eventuali errori che si verificano durante l'esecuzione di script personalizzati esulano dall'ambito di un ticket di supporto. 
* Applicazioni aggiuntive che fanno parte del processo di creazione del cluster. 

Per un elenco dei componenti supportati [, vedere quali sono i componenti e le versioni di Apache Hadoop disponibili con HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

Il supporto dei singoli componenti può variare anche in base al tipo di cluster. Ad esempio, Spark non è supportato in un cluster Kafka e viceversa.

Per le applicazioni o i servizi all'esterno del processo di creazione del cluster, contattare il rispettivo fornitore o provider di servizi per assistenza. È inoltre possibile utilizzare i siti della community per supportare tali azioni. Sono disponibili molti siti della community. Gli esempi includono [Forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/home?forum=hdinsight) e [stack overflow](https://stackoverflow.com/questions/tagged/hdinsight). Anche per i progetti Apache sono disponibili siti specifici nel [sito Web di Apache](https://apache.org/). Un esempio è [Hadoop](https://hadoop.apache.org/).

 Per altre domande correlate al supporto tecnico di Azure, vedere le domande [frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/en-us/support/faq/).

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>È possibile aggiornare i singoli componenti preinstallati nel cluster?

Se si esegue l'aggiornamento di componenti predefiniti o di applicazioni preinstallate nel cluster, la configurazione risultante non sarà supportata da Microsoft. Queste configurazioni di sistema non sono state testate da Microsoft. Provare a usare una versione diversa del cluster HDInsight in cui potrebbe essere già installata la versione aggiornata del componente.

Ad esempio, l'aggiornamento di hive come singolo componente non è supportato. HDInsight è un servizio gestito e molti servizi sono integrati con il server Ambari e testato. Se si aggiorna un hive in modo autonomo, i file binari indicizzati di altri componenti cambiano e causeranno problemi di integrazione dei componenti nel cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark e Kafka possono essere eseguiti nello stesso cluster HDInsight?

No, non è possibile eseguire Apache Kafka e Apache Spark nello stesso cluster HDInsight. Per evitare problemi di conflitti di risorse, gli utenti devono creare cluster distinti.

### <a name="how-do-i-change-timezone-in-ambari"></a>Ricerca per categorie modificare il fuso orario in Ambari?

1. Aprire l'interfaccia utente Web di https://CLUSTERNAME.azurehdinsight.net Ambari all'indirizzo, dove clustername è il nome del cluster.
2. Nell'angolo in alto a destra selezionare admin | Impostazioni. 

   ![Impostazioni Ambari](media/hdinsight-faq/ambari-settings.png)

3. Nella finestra impostazioni utente selezionare il nuovo fuso orario dall'elenco a discesa fuso orario, quindi fare clic su Salva.

   ![Impostazioni utente Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Come è possibile eseguire la migrazione dal Metastore esistente ad Azure SQL Server? 

Per eseguire la migrazione da SQL Server ad Azure SQL Server [, vedere Esercitazione: Eseguire la migrazione di SQL Server a un database singolo o a un database in pool nel database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)SQL di Azure offline con DMS.

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Il metastore Hive eliminato quando il cluster viene eliminato?

Dipende dal tipo di Metastore configurato per l'uso da parte del cluster.

Per un Metastore predefinito: Il Metastore predefinito fa parte del ciclo di vita del cluster. Quando si elimina un cluster verranno eliminati anche il metastore e i metadati corrispondenti.

Per un Metastore personalizzato: Il ciclo di vita del Metastore non è associato al ciclo di vita di un cluster. Pertanto, è possibile creare ed eliminare i cluster senza perdere i metadati. I metadati, ad esempio gli schemi hive, vengono mantenuti anche dopo l'eliminazione e la ricreazione del cluster HDInsight.

Per altre informazioni, vedere [Use external metadata stores in Azure HDInsight (Usare archivi di metadati esterni in Azure HDInsight)](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>La migrazione di un metastore Hive esegue anche la migrazione dei criteri predefiniti del database Ranger?

No, la definizione dei criteri si trova nel database Ranger. La migrazione del database Ranger, quindi, non comporta la migrazione dei criteri.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>È possibile eseguire la migrazione di un metastore Hive da un cluster di Enterprise Security Package (ESP) a un cluster non ESP e viceversa?

Sì, è possibile eseguire la migrazione di un metastore Hive da un ESP a un cluster non ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Come è possibile stimare le dimensioni di un database di metastore Hive?

Viene usato un metastore Hive per archiviare i metadati per le origini dati usate dal server hive. I requisiti di dimensione sono pertanto interessati dal numero di origini dati che potrebbe essere necessario utilizzare per l'hive e dalla complessità delle origini dati. Pertanto, le dimensioni non possono essere stimate in anticipo. Come descritto in [Metastore hive procedure consigliate](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores#hive-metastore-best-practices), a partire da un livello S2, fornisce 50 DTU e 250 GB di spazio di archiviazione. Se si verifica un collo di bottiglia, è possibile scalare verticalmente il database.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Sono supportati altri database diversi da quelli del database SQL di Azure come Metastore esterno?

No, Microsoft supporta solo il database SQL di Azure come Metastore personalizzato esterno.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>È possibile condividere un Metastore tra più cluster?

Sì, è possibile condividere il Metastore personalizzato tra più cluster, purché stia usando la stessa versione di HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Connettività e reti virtuali  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Quali sono le implicazioni del blocco delle porte 22 e 23 sulla rete?

Se si bloccano le porte 22 e 23, non si avrà accesso SSH al cluster. Queste porte non vengono usate dal servizio HDInsight.

Per altre informazioni, vedere i documenti seguenti:

* [Controllo del traffico di rete](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)
* [Proteggere il traffico in ingresso nei cluster HDInsight in una rete virtuale con endpoint privato](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
* [Indirizzi IP di gestione di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>È possibile distribuire una macchina virtuale aggiuntiva all'interno della stessa subnet di un cluster HDInsight?

Sì, è possibile distribuire una macchina virtuale aggiuntiva all'interno della stessa subnet di un cluster HDInsight. Sono possibili le configurazioni seguenti:

* Nodi perimetrali: È possibile aggiungere un altro nodo perimetrale al cluster, come descritto in [usare nodi perimetrali vuoti in Apache Hadoop cluster in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node). 
* Nodi autonomi:  È possibile aggiungere una macchina virtuale autonoma alla stessa subnet e accedere al cluster da tale macchina virtuale usando l'endpoint `https://<CLUSTERNAME>-int.azurehdinsight.net`privato. Per ulteriori informazioni, vedere [controllo del traffico di rete](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic).

### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>È possibile aggiungere un cluster HDInsight esistente a un'altra rete virtuale?

No, non è possibile aggiungere un cluster HDInsight esistente a un'altra rete virtuale. La rete virtuale deve essere specificata al momento del provisioning. Se durante il provisioning non viene specificata alcuna rete virtuale, la distribuzione crea una rete interna non accessibile dall'esterno. Per altre informazioni, vedere [aggiungere HDInsight a una rete virtuale esistente](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#existingvnet).

## <a name="security-and-certificates"></a>Sicurezza e certificati

Che cosa sono i consigli Microsoft per la protezione da malware nei cluster Azure HD Insight?

Per informazioni su malware Protection, vedere [Microsoft antimalware per servizi cloud e macchine virtuali di Azure](https://docs.microsoft.com/azure/security/fundamentals/antimalware).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Ricerca per categorie creare un keytab per un cluster ESP HDInsight?

È possibile creare un keytab Kerberos per il nome utente del dominio. In seguito è possibile usare questo keytab per eseguire l'autenticazione in cluster remoti aggiunti a un dominio senza immettere una password. Si notino i caratteri MAIUSCOLi nel nome di dominio:

```
o ktutil 
o ktutil: addent -password -p stXX@ DOMAIN.COM -k 1 -e RC4-HMAC 
o Password for stXX@DOMAIN.COM: provided by lab instructors 
o ktutil: wkt stXX.keytab 
o ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>È possibile usare un tenant di Azure Active Directory esistente per creare un cluster HDInsight con ESP?

L'abilitazione di Azure Active Directory Domain Services (AAD-DS) è un prerequisito prima che sia possibile creare un cluster HDInsight con ESP. Hadoop open source si basa su Kerberos per l'autenticazione (in contrapposizione a OAuth).

Per aggiungere macchine virtuali a un dominio, è necessario un controller di dominio. AAD-DS è il controller di dominio gestito. Viene considerata un'estensione di AAD che fornisce tutti i requisiti Kerberos per creare un cluster Hadoop sicuro in modo gestito. HDInsight come servizio gestito si integra con AAD-DS per fornire la protezione end-to-end.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>È possibile usare un certificato autofirmato in una configurazione LDAP sicura di AAD-DS ed effettuare il provisioning di un cluster ESP?

Sebbene sia consigliabile utilizzare un certificato emesso da un'autorità di certificazione (CA), l'utilizzo di un certificato autofirmato è supportato anche in ESP.

Per altre informazioni, vedere:

* [Abilitare Azure AD-DS](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#enable-azure-ad-ds)
* [Esercitazione: Configurare LDAP sicuro per un dominio gestito Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Come è possibile effettuare il pull delle attività di accesso visualizzate in Ranger?

Per i requisiti di controllo, Microsoft consiglia di abilitare i log di monitoraggio di Azure come descritto in [usare i log di monitoraggio di Azure per monitorare i cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>È possibile disabilitare clamscan nel cluster?

Clamscan è il software antivirus in esecuzione nel cluster HDInsight e viene usato dalla sicurezza di Azure (azsecd) per proteggere i cluster da attacchi di virus. Microsoft consiglia vivamente agli utenti di evitare di apportare modifiche alla configurazione predefinita di clamscan.

Questo processo non interferisce né elimina i cicli da altri processi. Verrà sempre restituito ad altri processi. I picchi di CPU da clamscan devono essere visualizzati solo quando il sistema è inattivo.  

Negli scenari in cui è necessario controllare la pianificazione, è possibile eseguire i passaggi seguenti:

1. Disabilitare l'esecuzione automatica usando il comando seguente:

   ``/usr/local/vbin/azsecd config -s clamav -d Disabled``

2. Creare un processo cron che esegue questo comando come radice:

   ``/usr/local/bin/azsecd manual -s clamav``

Per altre informazioni su come configurare ed eseguire un processo cron, vedere [ricerca per categorie configurare un processo cron](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job).

## <a name="storage"></a>Archiviazione

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>È possibile aggiungere un Azure Data Lake Storage Gen2 a un cluster HDInsight esistente come account di archiviazione aggiuntivo?

No. Attualmente, non è possibile aggiungere un Azure Data Lake Storage Gen2 a un cluster HDInsight esistente che usa l'archiviazione BLOB come risorsa di archiviazione primaria. Per altre informazioni, vedere [confrontare le opzioni di archiviazione per l'uso con i cluster HDInsight di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options). 

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Come è possibile trovare l'entità servizio attualmente collegata per un account di archiviazione Data Lake?

È possibile verificare queste impostazioni esaminando Data Lake Storage Gen1 accesso nelle proprietà del cluster nel portale. Per ulteriori informazioni, vedere [verificare la configurazione del cluster](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#verify-cluster-set-up).
 
Come è possibile calcolare l'utilizzo di account di archiviazione e contenitori BLOB per i cluster HDInsight?
È possibile utilizzare una delle procedure seguenti:

* [Usare PowerShell](https://docs.microsoft.com/azure/storage/scripts/storage-blobs-container-calculate-size-powershell)
* È anche possibile trovare le dimensioni di/User/hive/. Cestino/cartella nei cluster HDI usando il comando seguente:

   ``hdfs dfs -du -h /user/hive/.Trash/``

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Come è possibile configurare il controllo per l'account di archiviazione BLOB?

Per controllare gli account di archiviazione BLOB, è necessario configurare il monitoraggio per l'account di archiviazione BLOB usando la procedura documentata in [monitorare un account di archiviazione nel portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account). Un log HDFS-audit fornisce solo le informazioni di controllo solo per il file System HDFS locale (hdfs://mycluster).  Non include le operazioni eseguite sull'archiviazione remota.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Come è possibile trasferire i file tra un contenitore BLOB e un nodo head di HDInsight?

È possibile trasferire i file tra un contenitore BLOB e un nodo head di HDInsight eseguendo uno script della shell sul nodo Head, in modo simile all'esempio seguente:

```
for i in cat filenames.txt

do
         hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Il file *nome file. txt* avrà il percorso assoluto dei file nei contenitori BLOB.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Sono disponibili plug-in Ranger per l'archiviazione?

Attualmente non esiste alcun plug-in Ranger per l'archiviazione BLOB, Azure Data Lake Storage (ADLS) Gen1 o Azure Data Lake Storage Gen2. Per i cluster ESP, usare ADLS come procedura consigliata e impostare manualmente le autorizzazioni con granularità fine a livello di file system usando gli strumenti di HDFS. Inoltre, i cluster ESP eseguiranno alcune delle file system controllo di accesso usando AAD a livello di cluster quando si usa ADLS. 

Dovrebbe essere possibile usare il Azure Storage Explorer per assegnare criteri di accesso ai dati ai gruppi di sicurezza in cui si trovano gli utenti usando le procedure descritte negli articoli seguenti:

* [Ricerca per categorie impostare le autorizzazioni per consentire agli utenti Azure AD di eseguire query sui dati in Data Lake Storage Gen2 usando hive o altri servizi?](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
* [Impostare autorizzazioni a livello di file e directory usando Azure Storage Explorer con Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>È possibile aumentare lo spazio di archiviazione HDFS in un cluster senza aumentare le dimensioni del disco dei nodi del ruolo di lavoro?

No, non è possibile aumentare le dimensioni del disco di qualsiasi nodo di lavoro. L'unico modo per aumentare le dimensioni del disco è eliminare il cluster e ricrearlo nelle macchine virtuali di lavoro più grandi.

Microsoft non consiglia di usare HDFS per archiviare i dati in HDInsight perché i dati vengono eliminati quando si elimina il cluster. È invece consigliabile archiviare i dati in Azure. La scalabilità verticale del cluster può anche aumentare la capacità del cluster HDInsight.

## <a name="edge-nodes"></a>Nodi perimetrali

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>È possibile aggiungere un nodo perimetrale dopo la creazione del cluster?

Sì, è possibile aggiungere un nodo perimetrale vuoto a un cluster HDInsight esistente o a un nuovo cluster quando si crea il cluster.

Per altre informazioni, vedere [Usare i nodi perimetrali vuoti sui cluster Apache Hadoop in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node).

### <a name="how-can-i-connect-to-an-edge-node"></a>Come è possibile connettersi a un nodo perimetrale?

Dopo aver creato un nodo perimetrale, è possibile connettersi al nodo perimetrale usando SSH sulla porta 22. È possibile trovare il nome del nodo perimetrale dal portale del cluster (i nomi di solito terminano con "-ed").

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Perché gli script salvati in modo permanente non vengono eseguiti automaticamente nei nodi perimetrali appena creati?

Gli script salvati in modo permanente vengono usati per personalizzare i nuovi nodi del ruolo di lavoro aggiunti al cluster tramite operazioni di ridimensionamento e non si applicano ai nodi perimetrali.

## <a name="rest-api"></a>API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Quali sono le chiamate API REST per eseguire il pull di una visualizzazione di query Tez dal cluster?

È possibile usare gli endpoint REST seguenti per eseguire il pull delle informazioni necessarie in cui la risposta sarà in formato JSON. Per eseguire queste richieste, è possibile usare le intestazioni di autenticazione di base.

* Visualizzazione "Tez query": https://`<cluster name>`. azurehdinsight.NET/WS/V1/Timeline/HIVE_QUERY_ID/

* "Tez DAG" View- https://`<cluster name>`. azurehdinsight.NET/WS/V1/Timeline/TEZ_DAG_ID/

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Ricerca per categorie recuperare i dettagli di configurazione dal cluster HDI usando un utente Azure Active Directory?

Per negoziare i token di autenticazione appropriati con l'utente di AAD, passare attraverso il gateway usando il formato seguente:

* https://`<cluster dnsname>`. azurehdinsight.NET/API/V1/Clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Ricerca per categorie usare l'API RESTful Ambari per monitorare le prestazioni di YARN?

Se il comando curl viene chiamato nello stesso VNet o VNet con peering, usare il comando seguente:

``curl -u <cluster login username> -sS -G http://headnodehost:8080/api/v1/clusters/anssenllap/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
Se il comando viene chiamato dall'esterno del VNet o da un VNet senza peering, il formato del comando è:
 
* Per un cluster non ESP:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
* Per un cluster ESP:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``

> [!NOTE]
> Curl richiederà una password. È necessario immettere una password valida per il nome utente di accesso del cluster.

## <a name="billing"></a>Fatturazione

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Quanto costa distribuire un cluster HDInsight?

Per altre informazioni sui prezzi e le domande frequenti relative alla fatturazione, vedere la pagina dei [prezzi di Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) .

### <a name="how-do-i-cancel-my-subscription"></a>Ricerca per categorie annullare l'abbonamento?

Per informazioni su come annullare la sottoscrizione, vedere [annullare la sottoscrizione di Azure](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Per le sottoscrizioni con pagamento in base al consumo, cosa accade dopo aver annullato la sottoscrizione?

Per informazioni sulla sottoscrizione dopo che è stata annullata, vedere [che cosa accade dopo l'annullamento della sottoscrizione?](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription#what-happens-after-i-cancel-my-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Perché la versione di hive viene visualizzata come 1.2.1000 anziché 2,1 nell'interfaccia utente di Ambari anche se si esegue un cluster HDInsight 3,6?

Sebbene solo 1,2 venga visualizzato nell'interfaccia utente di Ambari, HDInsight 3,6 contiene sia hive 1,2 che hive 2,1.

## <a name="other-faq"></a>Altre domande frequenti

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Che cosa offre HDInsight in termini di funzionalità di elaborazione dei flussi in tempo reale?

Per informazioni sulle funzionalità di integrazione dell'elaborazione di flussi in Azure HDInsight, vedere [scelta di una tecnologia di elaborazione dei flussi in Azure](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Esiste un modo per terminare dinamicamente il nodo head del cluster quando il cluster è inattivo per un periodo specifico?

No, non è possibile terminare dinamicamente il nodo head del cluster. Per questo scenario è possibile usare Azure Data Factory.
