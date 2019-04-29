---
title: Ripristino di emergenza a livello di area per Azure Databricks
description: Questo articolo illustra un approccio per eseguire il ripristino di emergenza in Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: bd91d9201e81c884b48b41de27146c186eeb9598
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784661"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Ripristino di emergenza a livello di area per cluster di Azure Databricks

Questo articolo descrive un'architettura di ripristino di emergenza per i cluster di Azure Databricks e illustra i passaggi per implementare l'architettura.

## <a name="azure-databricks-architecture"></a>Architettura di Azure Databricks

A livello generale, quando si crea un'area di lavoro di Azure Databricks dal portale di Azure, viene distribuita un'[appliance gestita](../managed-applications/overview.md) come risorsa di Azure nella sottoscrizione, nell'area di Azure selezionata (ad esempio, Stati Uniti occidentali). L'appliance viene distribuita in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) con un [gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md) e un account di archiviazione di Azure, disponibile nella sottoscrizione. La rete virtuale offre sicurezza a livello di perimetro all'area di lavoro di Databricks ed è protetta tramite il gruppo di sicurezza di rete. Nell'area di lavoro è possibile creare i cluster di Databricks specificando il tipo di macchina virtuale del ruolo di lavoro e del driver e la versione del runtime di Databricks. I dati salvati in modo permanente sono disponibili nell'account di archiviazione, che può essere Archiviazione BLOB di Azure o Azure Data Lake Store. Dopo aver creato il cluster, è possibile eseguire i processi tramite notebook, API REST ed endpoint ODBC/JDBC associandoli a un cluster specifico.

Il piano di controllo di Databricks gestisce e monitora l'ambiente dell'area di lavoro di Databricks. Qualsiasi operazione di gestione, come la creazione di un cluster, verrà avviata dal piano di controllo. Tutti i metadati, ad esempio i processi pianificati, vengono archiviati in un database di Azure con replica geografica per la tolleranza di errore.

![Architettura di Databricks](media/howto-regional-disaster-recovery/databricks-architecture.png)

Uno dei vantaggi di questa architettura consiste nel fatto che gli utenti possono connettere Azure Databricks a qualsiasi risorsa di archiviazione nell'account. Un vantaggio significativo è che il calcolo (Azure Databricks) e l'archiviazione possono essere ridimensionati in modo indipendente.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Come creare una topologia di ripristino di emergenza a livello di area

Come si nota nella descrizione dell'architettura precedente, esistono diversi componenti usati per una pipeline di Big Data con Azure Databricks:  Archiviazione di Azure, Database di Azure e altre origini dati. Azure Databricks è la risorsa di *calcolo* per la pipeline di Big Data. Si tratta di una risorsa di natura *temporanea*. Infatti, anche se i dati rimangono disponibili in Archiviazione di Azure, il *calcolo* (cluster di Azure Databricks) può essere terminato per evitare che vengano applicati addebiti quando non è necessario. Le origini di *calcolo* (Azure Databricks) e di archiviazione devono trovarsi nella stessa area in modo da evitare problemi di latenza elevata dei processi.  

Per creare una topologia di ripristino di emergenza a livello di area, rispettare i requisiti seguenti:

   1. Effettuare il provisioning di più aree di lavoro di Azure Databricks in aree di Azure separate. Ad esempio, creare l'area di lavoro principale di Azure Databricks in Stati Uniti orientali 2. Creare l'area di lavoro secondaria di Azure Databricks per il ripristino di emergenza in un'area separata, ad esempio Stati Uniti occidentali.

   2. Usare l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). I dati associati a Azure Databricks vengono archiviati per impostazione predefinita in Archiviazione di Azure. Anche i risultati dei processi di Databricks vengono archiviati per impostazione predefinita in Archiviazione BLOB di Azure e pertanto i dati elaborati rimangono altamente disponibili dopo che il cluster è terminato. Poiché la risorsa di archiviazione e il cluster di Databricks si trovano nella stessa area, è necessario usare l'archiviazione con ridondanza geografica in modo da poter accedere ai dati nell'area secondaria se quella primaria non è più accessibile.

   3. Dopo aver creato l'area secondaria, è necessario eseguire la migrazione di utenti, cartelle degli utenti, notebook, configurazione di cluster, configurazione di processi, librerie, dati di archiviazione e script di inizializzazione e riconfigurare il controllo di accesso. Altri dettagli sono descritti nella sezione seguente.

## <a name="detailed-migration-steps"></a>Passaggi dettagliati della migrazione

1. **Configurare l'interfaccia della riga di comando di Databricks nel computer in uso**

   Questo articolo mostra alcuni esempi di codice che usano l'interfaccia della riga di comando per la maggior parte dei passaggi automatizzati, poiché è un wrapper di semplice utilizzo applicato all'API REST di Azure Databricks.

   Prima di eseguire i passaggi di migrazione, installare databricks-cli nel computer desktop o in una macchina virtuale in cui si prevede di eseguire il lavoro. Per altre informazioni, vedere [Install Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (Installare l'interfaccia della riga di comando di Databricks).

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Tutti gli script python forniti in questo articolo dovrebbero funzionare con Python 2.7+ < 3.x.

2. **Configurare due profili**

   Configurarne un profilo per l'area di lavoro primaria e un altro per quella secondaria:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   I blocchi di codice in questo articolo alternano i profili in ogni passaggio successivo usando il comando dell'area di lavoro corrispondente. Verificare che per i profili creati vengano sostituiti i nomi in ogni blocco di codice.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   È possibile passare manualmente alla riga di comando, se necessario:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Eseguire la migrazione degli utenti di Azure Active Directory**

   Aggiungere manualmente all'area di lavoro secondaria gli stessi utenti di Azure Active Directory presenti in quella primaria.

4. **Eseguire la migrazione dei notebook e delle cartelle degli utenti**

   Usare il codice python seguente per eseguire la migrazione degli ambienti sandbox degli utenti, che includono la struttura di cartelle annidate e i notebook per ogni utente.

   > [!NOTE]
   > Le librerie non vengono copiate in questo passaggio poiché non sono supportate dall'API sottostante.

   Copiare e salvare lo script python seguente in un file ed eseguirlo nella riga di comando di Databricks. Ad esempio: `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **Eseguire la migrazione delle configurazioni di cluster**

   Dopo la migrazione dei notebook, è possibile eseguire facoltativamente la migrazione delle configurazioni di cluster nella nuova area di lavoro. Se si usa databricks-cli questo passaggio è quasi completamente automatizzato, a meno che non si voglia eseguire la migrazione selettiva delle configurazioni di cluster anziché quella completa.

   > [!NOTE]
   > Purtroppo non è disponibile un endpoint per la creazione di configurazioni di cluster e lo script prova a creare direttamente ogni cluster. Se nella sottoscrizione non sono disponibili core sufficienti, è possibile che la creazione del cluster non riesca. L'errore può essere ignorato, purché la configurazione venga trasferita correttamente.

   Lo script seguente restituisce un mapping degli ID di cluster esistenti con quelli nuovi, che può essere usato per eseguire la migrazione dei processi in un secondo momento (per i processi configurati per l'uso di cluster esistenti).

   Copiare e salvare lo script python seguente in un file ed eseguirlo nella riga di comando di Databricks. Ad esempio: `python scriptname.py`.

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **Eseguire la migrazione della configurazione di processi**

   Se nel passaggio precedente sono state migrate configurazioni di cluster, è possibile decidere di eseguire anche la migrazione di configurazioni di processi nella nuova area di lavoro. Se si usa databricks-cli questo passaggio è completamente automatizzato, a meno che non si voglia eseguire la migrazione selettiva delle configurazioni di processi anziché quella completa.

   > [!NOTE]
   > La configurazione relativa a un processo pianificato contiene anche le informazioni di pianificazione, in modo che venga avviato in base al tempo configurato non appena è stata completata la migrazione. Di conseguenza, il blocco di codice seguente rimuove eventuali informazioni di pianificazione durante la migrazione (per evitare esecuzioni duplicate tra l'area di lavoro precedente e quella nuova). Configurare le pianificazioni per questi processi quando si è pronti per la migrazione completa.

   Per la configurazione di un processo devono essere definite le impostazioni per un nuovo cluster o un cluster esistente. Se si usa un cluster esistente, lo script con codice seguente proverà a sostituire l'ID del cluster precedente con quello del nuovo cluster.

   Copiare e salvare lo script python seguente in un file. Sostituire il valore per `old_cluster_id` e `new_cluster_id` con l'output risultante dalla migrazione del cluster eseguita nel passaggio precedente. Eseguirlo nella riga di comando databricks-cli, ad esempio `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Eseguire la migrazione di librerie**

   Attualmente non è disponibile un modo semplice per eseguire la migrazione di librerie da un'area di lavoro a un'altra. Come soluzione alternativa, è possibile reinstallare le librerie nella nuova area di lavoro manualmente oppure automatizzare questa operazione usando in combinazione l'[interfaccia della riga di comando per Databricks](https://github.com/databricks/databricks-cli#dbfs-cli-examples), per caricare le librerie personalizzate nell'area di lavoro, e l'[interfaccia della riga di comando per le librerie](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Eseguire la migrazione dei punti di montaggio di Archiviazione BLOB di Azure e Azure Data Lake Store**

   Reinstallare manualmente tutti [archiviazione Blob di Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html) e [Azure Data Lake Store (generazione 2)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) usando una soluzione basata su notebook di punti di montaggio. Se le risorse di archiviazione sono state montate nell'area di lavoro primaria, questa operazione deve essere ripetuta nell'area di lavoro secondaria. Non è disponibile alcuna API esterna per le operazioni di montaggio.

9. **Eseguire la migrazione degli script di inizializzazione dei cluster**

   È possibile eseguire la migrazione degli script di inizializzazione dei cluster dall'area di lavoro esistente a quella nuova usando l'[interfaccia della riga di comando per Databricks](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Copiare prima gli script necessari da `dbfs:/dat abricks/init/..` nella macchina virtuale o nel desktop locale e successivamente copiarli nella nuova area di lavoro nello stesso percorso.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Riconfigurare e riapplicare manualmente il controllo di accesso**

    Se l'area di lavoro primaria esistente è configurata per l'uso del livello Premium (SKU), è probabile che si usi anche la [funzionalità Controllo di accesso](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control).

    In tal caso, riapplicare manualmente il controllo di accesso alle risorse (notebook, cluster, processi, tabelle).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Ripristino di emergenza per l'ecosistema di Azure

Se si usa altri servizi di Azure, assicurarsi di implementare disaster recovery le procedure consigliate per tali servizi, troppo. Ad esempio, se si sceglie di utilizzare un'istanza di metastore Hive esterna, è consigliabile per il ripristino di emergenza [Server SQL di Azure](../sql-database/sql-database-disaster-recovery.md), [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md), e/o [Database di Azure per MySQL ](../mysql/concepts-business-continuity.md). Per informazioni generali sul ripristino di emergenza, vedere [ripristino di emergenza per le applicazioni Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [documentazione su Azure Databricks](https://docs.azuredatabricks.net/user-guide/index.html).
