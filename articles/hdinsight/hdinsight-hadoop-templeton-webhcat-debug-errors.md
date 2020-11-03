---
title: Comprendere e risolvere gli errori di WebHCat in HDInsight - Azure
description: Informazioni sugli errori comuni restituiti da WebHCat in HDInsight e su come risolverli.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 951dba6e64561301dc7dbb6ebd6fd6b641c90a47
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285678"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Comprendere e risolvere gli errori ricevuti da WebHCat in HDInsight

Informazioni sugli errori che si ricevono durante l'utilizzo di WebHCat con HDInsight e su come risolverli. WebHCat viene usato internamente dagli strumenti sul lato client, ad esempio Azure PowerShell e Strumenti Data Lake per Visual Studio.

## <a name="what-is-webhcat"></a>Che cos'è WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) è un'API REST per [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), un livello di gestione tabelle e risorse di archiviazione per Apache Hadoop. WebHCat è abilitato per impostazione predefinita nei cluster HDInsight e viene usato da vari strumenti per inviare processi, recuperare lo stato dei processi e così via, senza eseguire l'accesso al cluster.

## <a name="modifying-configuration"></a>Modifica della configurazione

Alcuni degli errori elencati in questo documento si verificano perché è stato superato un valore massimo configurato. Quando il passaggio di risoluzione indica che è possibile modificare un valore, usare Apache Ambari (API Web o REST) per modificarlo. Per altre informazioni, vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Configurazione predefinita

Il superamento dei valori predefiniti seguenti può determinare una riduzione delle prestazioni di WebHCat o la generazione di errori:

| Impostazione | Funzione | Valore predefinito |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |Il numero massimo di processi che possono essere attivi contemporaneamente (in sospeso o in esecuzione) |10,000 |
| [templeton.exec.max-procs][max-procs] |Il numero massimo di richieste che possono essere gestite contemporaneamente |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Il numero di giorni durante i quali verrà mantenuta la cronologia del processo |7 giorni |

## <a name="too-many-requests"></a>Numero eccessivo di richieste

**Codice di stato HTTP** : 429

| Causa | Risoluzione |
| --- | --- |
| È stato superato il numero massimo di richieste simultanee gestite da WebHCat al minuto (il valore predefinito è 20) |Ridurre il carico di lavoro per garantire che non venga inviato più del numero massimo di richieste simultanee o aumentare il limite di richieste simultanee modificando `templeton.exec.max-procs`. Per altre informazioni, vedere [Modifica della configurazione](#modifying-configuration) |

## <a name="server-unavailable"></a>Server non disponibile

**Codice di stato HTTP** : 503

| Causa | Risoluzione |
| --- | --- |
| Questo codice di stato si verifica in genere durante il failover tra il nodo head primario e secondario per il cluster |Attendere due minuti, quindi ripetere l'operazione |

## <a name="bad-request-content-could-not-find-job"></a>Contenuto della richiesta non valida: Il processo non è stato trovato

**Codice di stato HTTP** : 400

| Causa | Risoluzione |
| --- | --- |
| I dettagli dei processi sono stati rimossi dalla pulitura della cronologia dei processi |Il periodo di conservazione predefinito per la cronologia dei processi è 7 giorni. Il periodo di conservazione predefinito può essere cambiato modificando `mapreduce.jobhistory.max-age-ms`. Per altre informazioni, vedere [Modifica della configurazione](#modifying-configuration) |
| Il processo è stato terminato a causa di un failover |Ripetere l'invio del processo per due minuti |
| È stato usato un ID processo non valido |Controllare se l'ID processo è corretto |

## <a name="bad-gateway"></a>Gateway non valido

**Codice di stato HTTP** : 502

| Causa | Risoluzione |
| --- | --- |
| Operazione interna di Garbage Collection nel processo WebHCat |Attendere che l’operazione di Garbage Collection venga completata o riavviare il servizio WebHCat |
| Timeout in attesa di una risposta dal servizio Resource Manager. Questo errore può verificarsi quando il numero di applicazioni attive supera il numero massimo configurato (il valore predefinito è 10.000) |Attendere che i processi attualmente in esecuzione vengano completati o aumentare il limite di processi simultanei modificando `yarn.scheduler.capacity.maximum-applications`. Per altre informazioni, vedere la sezione [Modifica della configurazione](#modifying-configuration). |
| Quando si tenta di recuperare tutti i processi tramite la chiamata [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) mentre `Fields`è impostato su `*` |Non recuperare i dettagli di *tutti* i processi. Usare invece `jobid` per recuperare i dettagli solo dei processi successivi a un determinato ID processo oppure non usare `Fields` |
| Il servizio WebHCat è inattivo durante il failover del nodo head |Attendere due minuti e ripetere l'operazione |
| Sono presenti più di 500 processi in sospeso inviati tramite WebHCat |Attendere il completamento dei processi attualmente in sospeso prima di inviare altri processi |

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml