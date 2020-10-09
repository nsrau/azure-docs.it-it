---
title: Come usare la replica Apache Hive nei cluster HDInsight di Azure
description: Informazioni su come usare la replica hive nei cluster HDInsight per replicare il metastore Hive e la Azure Data Lake Storage generazione 2 Data Lake.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: af74392b3368a25e5d238f774292c80de5f91c65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91857742"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>Come usare la replica Apache Hive nei cluster HDInsight di Azure

Nel contesto di database e warehouse, la replica è il processo di duplicazione delle entità da un warehouse a un altro. La duplicazione può essere applicata a un intero database o a un livello inferiore, ad esempio una tabella o una partizione. L'obiettivo consiste nel disporre di una replica che cambia ogni volta che viene modificata l'entità di base. La replica su Apache Hive si concentra sul ripristino di emergenza e offre una replica di copia primaria unidirezionale. Nei cluster HDInsight la replica hive può essere usata per replicare in modo unidirezionale il metastore Hive e il data Lake sottostante associato in Azure Data Lake Storage Gen2.  

La replica di hive si è evoluta nel corso degli anni con versioni più recenti che forniscono funzionalità migliori e sono più veloci e meno intensivo di risorse. In questo articolo viene illustrata la replica hive (Replv2) supportata nei tipi di cluster HDInsight 3,6 e HDInsight 4,0.

## <a name="advantages-of-replv2"></a>Vantaggi di Replv2

[Hive ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) o (Replv2) presenta i vantaggi seguenti rispetto alla prima versione della replica di hive che usa l' [importazione/esportazione](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)hive:

- Replica incrementale basata su eventi
- Replica temporizzata  
- Requisiti per la larghezza di banda ridotta  
- Riduzione del numero di copie intermedie  
- Stato di replica mantenuto
- Replica vincolata  
- Supporto per un modello hub e spoke  
- Supporto per le tabelle ACID (in HDInsight 4,0)

## <a name="replication-phases"></a>Fasi di replica

La replica basata su eventi hive è configurata tra i cluster primari e secondari. Questa replica è costituita da due fasi distinte: bootstrap e esecuzioni incrementali.

### <a name="bootstrapping"></a>Bootstrap

Il bootstrap è progettato per essere eseguito una sola volta per replicare lo stato di base dei database dal database primario a quello secondario. Se necessario, è possibile configurare il bootstrap per includere un subset delle tabelle nel database di destinazione in cui deve essere abilitata la replica.

### <a name="incremental-runs"></a>Esecuzioni incrementali

Dopo il bootstrap, le esecuzioni incrementali vengono automatizzate nel cluster primario e gli eventi generati durante le esecuzioni incrementali vengono riprodotti nel cluster secondario. Quando il cluster secondario si aggiorna con il cluster primario, il database secondario diventa coerente con gli eventi del database primario.

## <a name="replication-commands"></a>Comandi di replica

Hive offre un set di comandi REPL ( `DUMP` , `LOAD` e) `STATUS` per orchestrare il flusso di eventi. Il `DUMP` comando genera un log locale di tutti gli eventi DDL/DML nel cluster primario. Il `LOAD` comando è un approccio per copiare in modo differito i metadati e i dati registrati nell'output del dump della replica estratto e viene eseguito nel cluster di destinazione. Il `STATUS` comando viene eseguito dal cluster di destinazione per fornire l'ID evento più recente che è stato replicato correttamente.

### <a name="set-replication-source"></a>Imposta origine replica

Prima di iniziare con la replica, verificare che il database da replicare sia impostato come origine della replica. È possibile usare il `DESC DATABASE EXTENDED <db_name>` comando per determinare se il parametro `repl.source.for` è impostato con il nome del criterio.

Se il criterio è pianificato e il `repl.source.for` parametro non è impostato, è necessario innanzitutto impostare questo parametro usando `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` .

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>Dump dei metadati in data Lake

Il `REPL DUMP [database name]. => location / event_id` comando viene usato nella fase bootstrap per eseguire il dump dei metadati rilevanti in Azure Data Lake storage Gen2. `event_id`Specifica l'evento minimo in cui i metadati rilevanti sono stati inseriti Azure Data Lake storage Gen2. 
 
```sql
repl dump tpcds_orc; 
```
Output di esempio:

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>Caricare i dati nel cluster di destinazione

Il `REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }` comando viene usato per caricare i dati nel cluster di destinazione sia per il bootstrap che per le fasi incrementali della replica. `[database name]`Può corrispondere all'origine o a un nome diverso nel cluster di destinazione. L'oggetto `[location]` rappresenta la posizione dall'output del `REPL DUMP` comando precedente. Questo significa che il cluster di destinazione deve essere in grado di comunicare con il cluster di origine. La `WITH` clausola è stata aggiunta principalmente per impedire il riavvio del cluster di destinazione, consentendo la replica.

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>Output dell'ultimo ID evento replicato

Il `REPL STATUS [database name]` comando viene eseguito nei cluster di destinazione e restituisce l'ultimo oggetto replicato `event_id` . Il comando consente inoltre agli utenti di conoscere lo stato in cui è stato replicato il cluster di destinazione. È possibile utilizzare l'output di questo comando per creare il `REPL DUMP` comando successivo per la replica incrementale.

```sql
repl status tpcds_orc;
```

Output di esempio:

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>Dump di dati e metadati rilevanti in data Lake

Il `REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }` comando viene utilizzato per eseguire il dump dei dati e dei metadati rilevanti in Azure Data Lake storage. Questo comando viene utilizzato nella fase incrementale e viene eseguito nel data warehouse di origine. `FROM [event-id]`È necessario per la fase incrementale e il valore di `event-id` può essere derivato eseguendo il `REPL STATUS [database name]` comando nel warehouse di destinazione.

```sql
repl dump tpcds_orc from 2925;
```

Output di esempio:

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Processo di replica hive

I passaggi seguenti sono gli eventi sequenziali che si verificano durante il processo di replica di hive.

1. Verificare che le tabelle da replicare siano impostate come origine della replica per un determinato criterio.

1. Il `REPL_DUMP` comando viene emesso nel cluster primario con vincoli associati, ad esempio nome del database, intervallo di ID evento e URL di archiviazione Azure Data Lake storage Gen2.

1. Il sistema serializza un dump di tutti gli eventi rilevati dal Metastore a quello più recente. Questo dump viene archiviato nell'account di archiviazione Azure Data Lake Storage Gen2 nel cluster primario in corrispondenza dell'URL specificato da `REPL_DUMP` .  

1. Il cluster primario rende permanente i metadati di replica nell'archiviazione Azure Data Lake Storage Gen2 del cluster primario. Il percorso è configurabile nell'interfaccia utente di configurazione di hive in Ambari. Il processo fornisce il percorso in cui vengono archiviati i metadati e l'ID dell'evento DML/DDL rilevato più recente.

1. Il `REPL_LOAD` comando viene emesso dal cluster secondario. Il comando punta al percorso configurato nel passaggio 3.

1. Il cluster secondario legge il file di metadati con gli eventi rilevati creati nel passaggio 3. Verificare che il cluster secondario disponga di connettività di rete per l'archiviazione Azure Data Lake Storage Gen2 del cluster primario da cui vengono archiviati gli eventi rilevati da `REPL_DUMP` .  

1. Il cluster secondario genera la copia distribuita ( `DistCP` ) di calcolo.

1. Il cluster secondario copia i dati dalla risorsa di archiviazione del cluster primario.  

1. Il Metastore nel cluster secondario viene aggiornato.  

1. L'ultimo ID evento rilevato viene archiviato nel Metastore primario.

La replica incrementale segue lo stesso processo e richiede l'ultimo ID evento replicato come input. Ciò comporta una copia incrementale dall'ultimo evento di replica. Le repliche incrementali vengono in genere automatizzate con una frequenza predeterminata per ottenere gli obiettivi del punto di ripristino (RPO) richiesti.

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Diagramma della replica hive":::

## <a name="replication-patterns"></a>Modelli di replica  

La replica viene in genere configurata in modo unidirezionale tra il database primario e quello secondario, in cui il primario è in grado di soddisfare le richieste di lettura e scrittura. Il cluster secondario soddisfa solo le richieste di lettura. Le Scritture sono consentite nel database secondario se si verifica un'emergenza, ma la replica inversa deve essere configurata di nuovo nel database primario.

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Diagramma della replica hive":::

Sono disponibili diversi modelli per la replica hive, tra cui Primary-Secondary, Hub, spoke e Relay.

In HDInsight Active Primary-standby Secondary è un modello comune di continuità aziendale e ripristino di emergenza (BCDR) e HiveReplicationV2 può usare questo modello con cluster HDInsight Hadoop separati a livello di area con peering VNet. Per ospitare gli script di automazione della replica, è possibile usare una macchina virtuale comune con peering a entrambi i cluster. Per ulteriori informazioni sui possibili modelli BCDR di HDInsight, vedere la [documentazione relativa alla continuità aziendale di HDInsight](../hdinsight-business-continuity.md).  

### <a name="hive-replication-with-enterprise-security-package"></a>Replica hive con Enterprise Security Package  

Nei casi in cui è pianificata la replica hive nei cluster Hadoop di HDInsight con Enterprise Security Package, è necessario fattorizzare i meccanismi di replica per Metastore Ranger e Azure Active Directory Domain Services (AD DS).  

Usare la funzionalità set di repliche di Azure AD DS per creare più di un set di repliche Azure AD DS per Azure AD tenant in più aree. Ogni singolo set di repliche deve essere sottoposto a peering con HDInsight reti virtuali nelle rispettive aree. In questa configurazione, le modifiche apportate a Azure AD DS, inclusi configurazione, identità utente e credenziali, gruppi, oggetti Criteri di gruppo, oggetti computer e altre modifiche vengono applicate a tutti i set di repliche nel dominio gestito tramite la replica di Azure AD DS.
  
È possibile eseguire periodicamente il backup e la replica dei criteri Ranger dal database primario al database secondario usando la funzionalità Import-Export di Ranger. È possibile scegliere di replicare tutti o un subset di criteri Ranger a seconda del livello di autorizzazione che si sta tentando di implementare nel cluster secondario.  

## <a name="sample-code"></a>Codice di esempio  

Nella sequenza di codice riportata di seguito viene fornito un esempio in cui il bootstrap e la replica incrementale possono essere implementati in una tabella di esempio denominata `tpcds_orc` .  

1. Impostare la tabella come origine per i criteri di replica.

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. Dump bootstrap nel cluster primario.

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   Output di esempio:
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. Caricamento bootstrap nel cluster secondario. 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. Controllare lo `REPL` stato nel cluster secondario.

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. Dump incrementale nel cluster primario.

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   Output di esempio:
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. Caricamento incrementale nel cluster secondario.  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. Verificare `REPL` lo stato nel cluster secondario.

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sugli elementi trattati in questo articolo, vedere:

- [Continuità aziendale di Azure HDInsight](../hdinsight-business-continuity.md)
- [Architetture di continuità aziendale di Azure HDInsight](../hdinsight-business-continuity-architecture.md)
- [Architettura della soluzione a disponibilità elevata di Azure HDInsight case study](../hdinsight-high-availability-case-study.md)
- [Cosa sono Apache Hive e HiveQL in Azure HDInsight](../hadoop/hdinsight-use-hive.md)