---
title: Sincronizzazione LDAP in Ranger e Apache Ambari in Azure HDInsight
description: Risolvere la sincronizzazione LDAP in Ranger e Ambari e fornire linee guida generali.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 0779ac261fbb4ee91bf63021bb0cc685a371c2b2
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234070"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Sincronizzazione LDAP in Ranger e Apache Ambari in Azure HDInsight

I cluster HDInsight Enterprise Security Package (ESP) usano Ranger per l'autorizzazione. Apache Ambari e Ranger sincronizzano gli utenti e i gruppi in modo indipendente e funzionano in modo leggermente diverso. Questo articolo ha lo scopo di risolvere la sincronizzazione LDAP in Ranger e Ambari.

## <a name="general-guidelines"></a>Linee guida generali

* Distribuire sempre i cluster con uno o più gruppi.
* Se si desidera utilizzare più gruppi nel cluster, verificare se è opportuno aggiornare l'appartenenza a gruppi in Azure Active Directory (Azure AD).
* Se si desidera modificare i gruppi di cluster, è possibile modificare i filtri di sincronizzazione utilizzando Ambari.
* Tutte le modifiche apportate all'appartenenza al gruppo in Azure AD vengono riflesse nel cluster nelle sincronizzazioni successive. Le modifiche devono essere sincronizzate prima di Azure AD Domain Services (Azure AD DS) e quindi ai cluster.
* I cluster HDInsight usano samba/winbind per proiettare le appartenenze ai gruppi nei nodi del cluster.
* I membri del gruppo vengono sincronizzati in modo transitivo (tutti i sottogruppi e i relativi membri) sia per Ambari che per Ranger. 

## <a name="users-are-synced-separately"></a>Gli utenti vengono sincronizzati separatamente

 * Ambari e Ranger non condividono il database utente perché servono a due scopi diversi. 
   * Se un utente deve usare l'interfaccia utente di Ambari, l'utente deve essere sincronizzato con Ambari. 
   * Se l'utente non viene sincronizzato con Ambari, l'API o l'interfaccia utente di Ambari la rifiuterà, ma altre parti del sistema funzioneranno (sono sorvegliate da Ranger o Gestione risorse e non da Ambari).
   * Per includere utenti o gruppi nei criteri Ranger, le entità devono essere sincronizzate in modo esplicito in Ranger.

## <a name="ambari-user-sync-and-configuration"></a>Configurazione e sincronizzazione utente Ambari

Dai nodi head, un processo cron, `/opt/startup_scripts/start_ambari_ldap_sync.py` , viene eseguito ogni ora per pianificare la sincronizzazione dell'utente. Il processo cron chiama le API REST di Ambari per eseguire la sincronizzazione. Lo script Invia un elenco di utenti e gruppi da sincronizzare (poiché gli utenti potrebbero non appartenere ai gruppi specificati, entrambi sono specificati singolarmente). Ambari sincronizza l'oggetto sAMAccountName come nome utente e tutti i membri del gruppo, in modo transitivo.

I log devono essere presenti in `/var/log/ambari-server/ambari-server.log` . Per altre informazioni, vedere [configurare il livello di registrazione Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

In Data Lake cluster, l'hook per la creazione di post utente viene usato per creare le Home directory degli utenti sincronizzati e vengono impostate come proprietari delle Home Directory. Se l'utente non viene sincronizzato con Ambari correttamente, l'utente potrebbe affrontare gli errori nei processi in esecuzione perché la home directory potrebbe non essere configurata correttamente.

## <a name="ranger-user-sync-and-configuration"></a>Configurazione e sincronizzazione utente Ranger

Ranger dispone di un motore di sincronizzazione incorporato che viene eseguito ogni ora per sincronizzare gli utenti. Non condivide il database utente con Ambari. HDInsight configura il filtro di ricerca per sincronizzare l'utente amministratore, l'utente watchdog e i membri del gruppo specificato durante la creazione del cluster. I membri del gruppo verranno sincronizzati in modo transitivo:

1. Disabilitare la sincronizzazione incrementale.
1. Abilitare la mappa di sincronizzazione del gruppo di utenti.
1. Specificare il filtro di ricerca per includere i membri del gruppo transitivi.
1. Sincronizzare l'attributo sAMAccountName per gli utenti e l'attributo Name per i gruppi.

### <a name="group-or-incremental-sync"></a>Gruppo o sincronizzazione incrementale

Ranger supporta un'opzione di sincronizzazione di gruppo, ma funziona come intersezione con filtro utente e non come unione tra le appartenenze ai gruppi e il filtro utente. Un caso di utilizzo tipico per il filtro di sincronizzazione del gruppo in Ranger è filtro di gruppo: (DN = clusteradmingroup), filtro utente: (City = Seattle).

La sincronizzazione incrementale funziona solo per gli utenti già sincronizzati (la prima volta). Incremental non sincronizza i nuovi utenti aggiunti ai gruppi dopo la sincronizzazione iniziale.

### <a name="update-ranger-sync-filter"></a>Aggiorna filtro sincronizzazione Ranger

Il filtro LDAP è disponibile nell'interfaccia utente di Ambari, nella sezione configurazione della sincronizzazione utente di Ranger. Il filtro esistente sarà nel formato `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Assicurarsi di aggiungere un predicato alla fine e testare il filtro usando il `net ads` comando di ricerca o ldp.exe o un valore simile.

## <a name="ranger-user-sync-logs"></a>Log di sincronizzazione utente Ranger

La sincronizzazione utente Ranger può essere eseguita da uno dei nodi head. I log si trovano in `/var/log/ranger/usersync/usersync.log` . Per aumentare il livello di dettaglio dei log, seguire questa procedura:

1. Accedere a Ambari.
1. Passare alla sezione di configurazione di Ranger.
1. Passare alla sezione Advanced **usersync-log4j** .
1. Modificare il `log4j.rootLogger` `DEBUG` livello in. (Dopo la modifica, l'aspetto dovrebbe essere simile a `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Salvare la configurazione e riavviare Ranger.

## <a name="known-issues-with-ranger-user-sync"></a>Problemi noti relativi alla sincronizzazione utente di Ranger
* Se il nome del gruppo contiene caratteri Unicode, la sincronizzazione di Ranger non riesce a sincronizzare tale oggetto. Se un utente appartiene a un gruppo con caratteri internazionali, Ranger sincronizza l'appartenenza parziale al gruppo
* Il nome utente (sAMAccountName) e il nome del gruppo (nome) devono avere una lunghezza di 20 caratteri o meno. Se il nome del gruppo è più lungo, l'utente verrà considerato come se non appartenga al gruppo, durante il calcolo delle autorizzazioni.

## <a name="next-steps"></a>Passaggi successivi

* [Problemi di autenticazione in Azure HDInsight](./domain-joined-authentication-issues.md)
* [Sincronizzare gli utenti di Azure AD con un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
