---
title: Sincronizzazione LDAP in Ranger e Apache Ambari in Azure HDInsight
description: Risolvere la sincronizzazione LDAP in Ranger e Ambari e fornire linee guida generali.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463219"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Sincronizzazione LDAP in Ranger e Apache Ambari in Azure HDInsight

I cluster HDInsight Enterprise Security Package (ESP) usano Ranger per l'autorizzazione. Apache Ambari e Ranger sincronizzano gli utenti e i gruppi in modo indipendente e funzionano in modo leggermente diverso. Questo articolo ha lo scopo di risolvere la sincronizzazione LDAP in Ranger e Ambari.

## <a name="general-guidelines"></a>Linee guida generali

* Distribuire sempre cluster con gruppi.
* Anziché modificare i filtri di gruppo in Ambari e Ranger, provare a gestirli tutti in Azure AD e usare i gruppi annidati per importare gli utenti necessari.
* Quando un utente viene sincronizzato, non viene rimosso anche se l'utente non fa parte dei gruppi.
* Se è necessario modificare direttamente i filtri LDAP, usare prima l'interfaccia utente perché contiene alcune convalide.

## <a name="users-are-synced-separately"></a>Gli utenti vengono sincronizzati separatamente

Ambari e Ranger non condividono il database utente perché servono a due scopi diversi. Se un utente deve usare l'interfaccia utente di Ambari, l'utente deve essere sincronizzato con Ambari. Se l'utente non viene sincronizzato con Ambari, l'interfaccia utente o l'API Ambari la rifiuterà, ma le altre parti del sistema funzioneranno (sono sorvegliate da Ranger o Gestione risorse e non da Ambari). Se si vuole includere l'utente in un criterio Ranger, sincronizzare l'utente con Ranger.

Quando viene distribuito un cluster protetto, i membri del gruppo vengono sincronizzati in modo transitivo (tutti i sottogruppi e i relativi membri) sia per Ambari che per Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Configurazione e sincronizzazione utente Ambari

Dai nodi head, un processo cron, `/opt/startup_scripts/start_ambari_ldap_sync.py`, viene eseguito ogni ora per pianificare la sincronizzazione dell'utente. Il processo cron chiama le API REST di Ambari per eseguire la sincronizzazione. Lo script Invia un elenco di utenti e gruppi da sincronizzare (poiché gli utenti potrebbero non appartenere ai gruppi specificati, entrambi sono specificati singolarmente). Ambari sincronizza l'oggetto sAMAccountName come nome utente e tutti i membri del gruppo, in modo transitivo.

I log devono essere in `/var/log/ambari-server/ambari-server.log`. Per altre informazioni, vedere [configurare il livello di registrazione Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

In Data Lake cluster, l'hook per la creazione di post utente viene usato per creare le Home directory degli utenti sincronizzati e vengono impostate come proprietari delle Home Directory. Se l'utente non viene sincronizzato con Ambari correttamente, l'utente potrebbe non essere in grado di accedere alla gestione temporanea e ad altre cartelle temporanee.

### <a name="update-groups-to-be-synced-to-ambari"></a>Aggiornare i gruppi da sincronizzare con Ambari

Se non è possibile gestire le appartenenze ai gruppi in Azure AD, sono disponibili due opzioni:

* Eseguire una sincronizzazione una volta, come descritto più completamente, in [sincronizzare utenti e gruppi LDAP](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Ogni volta che l'appartenenza al gruppo cambia, sarà necessario ripetere la sincronizzazione.

* Scrivere un processo cron, chiamare [periodicamente l'API Ambari](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) con i nuovi gruppi.

## <a name="ranger-user-sync-and-configuration"></a>Configurazione e sincronizzazione utente Ranger

Ranger dispone di un motore di sincronizzazione incorporato che viene eseguito ogni ora per sincronizzare gli utenti. Non condivide il database utente con Ambari. HDInsight configura il filtro di ricerca per sincronizzare l'utente amministratore, l'utente watchdog e i membri del gruppo specificato durante la creazione del cluster. I membri del gruppo verranno sincronizzati in modo transitivo:

* Disabilitare la sincronizzazione incrementale.
* Abilita mappa di sincronizzazione gruppi di utenti.
* Specificare il filtro di ricerca per includere i membri del gruppo transitivi.
* Sincronizzare sAMAccountName per gli utenti e l'attributo nome per i gruppi.

### <a name="group-or-incremental-sync"></a>Gruppo o sincronizzazione incrementale

Ranger supporta un'opzione di sincronizzazione di gruppo, ma funziona come intersezione con filtro utente. Non è un'unione tra le appartenenze ai gruppi e il filtro utente. Un caso di utilizzo tipico per il filtro di sincronizzazione del gruppo in Ranger è filtro di gruppo: (DN = clusteradmingroup), filtro utente: (City = Seattle).

La sincronizzazione incrementale funziona solo per gli utenti già sincronizzati (la prima volta). Incremental non sincronizza i nuovi utenti aggiunti ai gruppi dopo la sincronizzazione iniziale.

### <a name="update-ranger-sync-filter"></a>Aggiorna filtro sincronizzazione Ranger

Il filtro LDAP è disponibile nell'interfaccia utente di Ambari, nella sezione configurazione della sincronizzazione utente di Ranger. Il filtro esistente sarà nel formato `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`. Assicurarsi di aggiungere un predicato alla fine e testare il filtro usando `net ads` comando Search o LDP. exe o un valore simile.

## <a name="ranger-user-sync-logs"></a>Log di sincronizzazione utente Ranger

La sincronizzazione utente Ranger può essere eseguita da uno dei nodi head. I log si trovano in `/var/log/ranger/usersync/usersync.log`. Per aumentare il livello di dettaglio dei log, seguire questa procedura:

1. Accedere a Ambari.
1. Passare alla sezione di configurazione di Ranger.
1. Passare alla sezione Advanced **usersync-log4j** .
1. Modificare il `log4j.rootLogger` a livello di `DEBUG` (dopo la modifica dovrebbe essere simile `log4j.rootLogger = DEBUG,logFile,FilterLog`).
1. Salvare la configurazione e riavviare Ranger.

## <a name="next-steps"></a>Passaggi successivi

* [Problemi di autenticazione in Azure HDInsight](./domain-joined-authentication-issues.md)
* [Sincronizzare Azure AD utenti a un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
