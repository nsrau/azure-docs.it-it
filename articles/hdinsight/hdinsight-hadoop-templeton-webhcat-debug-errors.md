<properties
 pageTitle="Comprendere e risolvere gli errori di WebHCat in HDInsight"
 description="Informazioni sugli errori comuni restituiti da WebHCat in HDInsight e su come risolverli."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="02/05/2016"
 ms.author="larryfr"/>

#Comprendere e risolvere gli errori ricevuti da WebHCat (Templeton,) in HDInsight

Quando si usa WebHCat (precedentemente noto come Templeton,) con HDInsight, possono verificarsi degli errori. In questo documento vengono fornite indicazioni su errori comuni, perché si verificano e come è possibile risolverli.

##Informazioni su WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) è un'API REST per [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), un livello di gestione tabella e archiviazione per Hadoop. WebHCat è abilitato per impostazione predefinita nei cluster HDInsight e viene usato da vari strumenti per inviare processi, recuperare lo stato dei processi e così via, senza effettuare l'accesso al cluster.

##Modifica della configurazione

> [AZURE.IMPORTANT] Alcuni degli errori elencati in questo documento si verificano perché è stato superato un valore massimo configurato. La procedura di risoluzione indica che è possibile modificare un valore, per eseguire la modifica è necessario usare uno dei seguenti passaggi:

* Per cluster **Windows**: usare un'azione di script per configurare il valore durante la creazione del cluster. Per altre informazioni, vedere [Sviluppare azioni di script](hdinsight-hadoop-script-actions.md).

* Per cluster **Linux**: usare Ambari (web o API REST) per modificare il valore. Per altre informazioni, vedere [Gestire HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md)

###Configurazione predefinita

Di seguito sono riportati i valori di configurazione predefiniti che possono influire sulle prestazioni di WebHCat o causare errori se vengono superati:

| Impostazione | Risultato | Valore predefinito |
| ------- | ------------ | ------------- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] | Il numero massimo di processi che possono essere attivi contemporaneamente (in sospeso o in esecuzione) | 10\.000 |
| [templeton.exec.max-procs][max-procs] | Il numero massimo di richieste che possono essere gestite contemporaneamente | 20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] | Il numero di giorni durante i quali verrà mantenuta la cronologia del processo | 7 giorni |

##Numero eccessivo di richieste

**Codice di stato HTTP**: 429

| Causa | Risoluzione |
| ----- | ---------- |
| È stato superato il numero massimo di richieste contemporanee gestite da WebHCat al minuto (il valore predefinito è 20) | Ridurre il carico di lavoro per garantire che non venga inviato più del numero massimo di richieste contemporanee o aumentare il limite di richieste contemporanee modificando `templeton.exec.max-procs`. Per altre informazioni vedere [Modifica della configurazione](#modifying-configuration) |

##Server non disponibile

**Codice di stato HTTP**: 503

| Causa | Risoluzione |
| ---------------- | ------------------- |
| Il problema si verifica in genere durante il failover tra il nodo head primario e secondario per il cluster | Attendere due minuti, quindi ripetere l'operazione |

##Contenuto richiesta non valido: impossibile trovare il processo

**Codice di stato HTTP**: 400

| Causa | Risoluzione |
| ---------------- | ------------------- |
| I dettagli dei processi sono stati rimossi dalla pulitura della cronologia dei processi | Il periodo di conservazione predefinito per la cronologia dei processi è 7 giorni. Questo periodo può essere cambiato modificando `mapreduce.jobhistory.max-age-ms`. Per altre informazioni vedere [Modifica della configurazione](#modifying-configuration) |
| Il processo è stato terminato a causa di un failover | Ripetere l'invio del processo per due minuti |
| È stato utilizzato un ID processo non valido | Controllare se l'ID processo è corretto |

##Gateway non valido

**Codice di stato HTTP**: 502

| Causa | Risoluzione |
| ---------------- | ------------------- |
| Operazione interna di Garbage Collection nel processo WebHCat | Attendere che l’operazione di Garbage Collection venga completata o riavviare il servizio WebHCat |
| Timeout in attesa di una risposta dal servizio ResourceManager. Questo problema può verificarsi quando il numero di applicazioni attive supera il numero massimo configurato (il valore predefinito è 10.000) | Attendere che i processi attualmente in esecuzione vengano completati o aumentare il limite di processi simultanei modificando `yarn.scheduler.capacity.maximum-applications`. Per altre informazioni vedere [Modifica della configurazione](#modifying-configuration) |
| Quando si tenta di recuperare tutti i processi tramite la chiamata [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) mentre `Fields` è impostato su `*` | Non recuperare *tutti* i dettagli dei processi, utilizzare invece `jobid` per recuperare solo i dettagli dei processi maggiori di determinati ID processo. Oppure, non utilizzare `Fields` |
| Il servizio WebHCat è inattivo durante il failover del nodo head | Attendere due minuti e ripetere l'operazione |
| Sono presenti più di 500 processi in sospeso inviati tramite WebHCat | Attendere il completamento dei processi attualmente in sospeso prima di inviare altri processi |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 

<!---HONumber=AcomDC_0211_2016-->