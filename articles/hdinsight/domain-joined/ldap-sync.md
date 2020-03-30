---
title: Sincronizzazione LDAP in Ranger e Apache Ambari in Azure HDInsight
description: Rispondere alla sincronizzazione LDAP in Ranger e Ambari e fornire linee guida generali.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463219"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Sincronizzazione LDAP in Ranger e Apache Ambari in Azure HDInsight

I cluster HDInsight Enterprise Security Package (ESP) usano Ranger per l'autorizzazione. Apache Ambari e Ranger sincronizzano gli utenti e i gruppi in modo indipendente e funzionano in modo leggermente diverso. Questo articolo ha lo scopo di indirizzare la sincronizzazione LDAP in Ranger e Ambari.

## <a name="general-guidelines"></a>Linee guida generali

* Distribuire sempre i cluster con i gruppi.
* Invece di modificare i filtri di gruppo in Ambari e Ranger, provare a gestire tutti questi in Azure AD e usare i gruppi nidificati per importare gli utenti necessari.
* Una volta sincronizzato, un utente non viene rimosso anche se non fa parte dei gruppi.
* Se è necessario modificare direttamente i filtri LDAP, utilizzare prima l'interfaccia utente in quanto contiene alcune convalide.

## <a name="users-are-synced-separately"></a>Gli utenti vengono sincronizzati separatamente

Ambari e Ranger non condividono il database degli utenti perché hanno due scopi diversi. Se un utente deve usare l'interfaccia utente di Ambari, l'utente deve essere sincronizzato con Ambari. Se l'utente non è sincronizzato con Ambari, Ambari UI / API lo rifiuterà, ma altre parti del sistema funzioneranno (queste sono custodite da Ranger o Resource Manager e non Ambari). Se si desidera includere l'utente in un criterio Ranger, quindi sincronizzare l'utente con Ranger.

Quando viene distribuito un cluster protetto, i membri del gruppo vengono sincronizzati in modo transitivo (tutti i sottogruppi e i relativi membri) con Ambari e Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Sincronizzazione e configurazione dell'utente Ambari

Dai nodi head, un `/opt/startup_scripts/start_ambari_ldap_sync.py`processo cron, , viene eseguito ogni ora per pianificare la sincronizzazione dell'utente. Il processo cron chiama le API di riposo Ambari per eseguire la sincronizzazione. Lo script invia un elenco di utenti e gruppi da sincronizzare (poiché gli utenti potrebbero non appartenere ai gruppi specificati, entrambi vengono specificati singolarmente). Ambari sincronizza sAMAccountName come nome utente e tutti i membri del gruppo, in modo transitivo.

I registri devono `/var/log/ambari-server/ambari-server.log`essere in . Per ulteriori informazioni, consultate Configurare il livello di [registrazione di Ambari.](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)

Nei cluster Data Lake, l'hook per la creazione di post-utente viene utilizzato per creare le cartelle Inizio per gli utenti sincronizzati e vengono impostati come proprietari delle cartelle inizio. Se l'utente non è sincronizzato correttamente con Ambari, l'utente potrebbe affrontare errori nell'accesso alla gestione temporanea e ad altre cartelle temporanee.

### <a name="update-groups-to-be-synced-to-ambari"></a>Aggiornare i gruppi da sincronizzare con Ambari

Se non è possibile gestire le appartenenze ai gruppi in Azure AD, sono disponibili due opzioni:If you can't manage groups memberships in Azure AD, you have two choices:

* Eseguire una sincronizzazione una tant'altro come descritto più completamente in [Sincronizza utenti e gruppi LDAP](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Ogni volta che l'appartenenza al gruppo cambia, dovrai eseguire di nuovo questa sincronizzazione.

* Scrivere un processo cron, chiamare [periodicamente l'API Ambari](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) con i nuovi gruppi.

## <a name="ranger-user-sync-and-configuration"></a>Sincronizzazione e configurazione dell'utente Ranger

Ranger dispone di un motore di sincronizzazione integrato che viene eseguito ogni ora per sincronizzare gli utenti. Non condivide il database utenti con Ambari. HDInsight configura il filtro di ricerca per sincronizzare l'utente amministratore, l'utente watchdog e i membri del gruppo specificato durante la creazione del cluster. I membri del gruppo verranno sincronizzati in modo transitivo:

* Disabilitare la sincronizzazione incrementale.
* Abilitare la mappa di sincronizzazione dei gruppi di utenti.
* Specificare il filtro di ricerca per includere i membri del gruppo transitivo.
* Sincronizzare sAMAccountName per gli utenti e l'attributo name per i gruppi.

### <a name="group-or-incremental-sync"></a>Raggruppamento o sincronizzazione incrementale

Ranger supporta un'opzione di sincronizzazione di gruppo, ma funziona come un'intersezione con il filtro utente. Non è un'unione tra le appartenenze ai gruppi e il filtro utente. Un caso d'uso tipico per il filtro di sincronizzazione di gruppo in Ranger è - filtro di gruppo: (dn-clusteradmingroup), filtro utente: (città-seattle).

La sincronizzazione incrementale funziona solo per gli utenti già sincronizzati (la prima volta). Incrementale non sincronizzerà i nuovi utenti aggiunti ai gruppi dopo la sincronizzazione iniziale.

### <a name="update-ranger-sync-filter"></a>Aggiorna filtro di sincronizzazione Ranger

Il filtro LDAP si trova nell'interfaccia utente di Ambari, nella sezione di configurazione della sincronizzazione utente Di Ranger. Il filtro esistente sarà `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`nel formato . Assicurarsi di aggiungere predicato alla fine e `net ads` testare il filtro utilizzando il comando di ricerca o ldp.exe o qualcosa di simile.

## <a name="ranger-user-sync-logs"></a>Registri di sincronizzazione degli utenti Ranger

La sincronizzazione degli utenti Ranger può avvenire da uno dei nodi di testa. I registri `/var/log/ranger/usersync/usersync.log`sono in formato . Per aumentare il livello di dettaglio dei registri, attenersi alla seguente procedura:

1. Accedi ad Ambari.
1. Vai alla sezione di configurazione Ranger.
1. Passare alla sezione Advanced **usersync-log4j.**
1. Modificare `log4j.rootLogger` il `DEBUG` al livello (dopo `log4j.rootLogger = DEBUG,logFile,FilterLog`la modifica dovrebbe essere simile ).
1. Salvare la configurazione e riavviare ranger.

## <a name="next-steps"></a>Passaggi successivi

* [Problemi di autenticazione in Azure HDInsightAuthentication issues in Azure HDInsight](./domain-joined-authentication-issues.md)
* [Sincronizzare gli utenti di Azure AD con un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
