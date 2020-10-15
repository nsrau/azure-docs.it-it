---
title: Linee guida generali per la sicurezza aziendale in Azure HDInsight
description: Alcune procedure consigliate per semplificare la distribuzione e la gestione di Enterprise Security Package.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 8d97886232eecc369746e33df484cbfb9d40da72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87530266"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Informazioni generali e linee guida per la sicurezza aziendale in Azure HDInsight

Quando si distribuisce un cluster HDInsight sicuro, sono disponibili alcune procedure consigliate che semplificano la distribuzione e la gestione dei cluster. Di seguito sono illustrate alcune informazioni generali e linee guida.

## <a name="use-of-secure-cluster"></a>Uso del cluster sicuro

### <a name="recommended"></a>Consigliato

* Il cluster verrà usato da più utenti nello stesso momento.
* Gli utenti hanno diversi livelli di accesso agli stessi dati.

### <a name="not-necessary"></a>Non necessario

* Verranno eseguiti solo i processi automatizzati, ad esempio un singolo account utente, un cluster standard è sufficientemente adatto.
* È possibile eseguire l'importazione dei dati usando un cluster standard e usare lo stesso account di archiviazione in un cluster sicuro diverso, in cui gli utenti possono eseguire processi di analisi.

## <a name="use-of-local-account"></a>Uso dell'account locale

* Se si usa un account utente condiviso o un account locale, sarà difficile identificare chi ha usato l'account per modificare la configurazione o il servizio.
* L'uso di account locali è problematico quando gli utenti non fanno più parte dell'organizzazione.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Criteri

* Per impostazione predefinita, Ranger USA **Deny** come criterio.

* Quando l'accesso ai dati viene eseguito tramite un servizio in cui è abilitata l'autorizzazione:
  * Il plug-in di autorizzazione Ranger viene richiamato e dato il contesto della richiesta.
  * Ranger applica i criteri configurati per il servizio. Se i criteri del Ranger hanno esito negativo, il controllo di accesso viene rinviato al file system. Alcuni servizi come MapReduce controllano solo se il file o la cartella di proprietà dello stesso utente che invia la richiesta. Servizi come hive, verificano la corrispondenza di proprietà o le autorizzazioni del file System appropriate ( `rwx` ).

* Per hive, oltre a disporre delle autorizzazioni per eseguire le autorizzazioni di creazione/aggiornamento/eliminazione, l'utente deve disporre `rwx` delle autorizzazioni per la directory di archiviazione e per tutte le sottodirectory.

* I criteri possono essere applicati a gruppi (preferibili) anziché a singoli utenti.

* Il provider di autorizzazioni Ranger valuterà tutti i criteri Ranger per il servizio per ogni richiesta. Questa valutazione potrebbe avere un effetto sul tempo necessario per accettare il processo o la query.

### <a name="storage-access"></a>Accesso all'archiviazione

* Se il tipo di archiviazione è WASB, non è necessario alcun token OAuth.
* Se Ranger ha eseguito l'autorizzazione, l'accesso all'archiviazione si verifica usando l'identità gestita.
* Se Ranger non ha eseguito alcuna autorizzazione, l'accesso all'archiviazione si verifica usando il token OAuth dell'utente.

### <a name="hierarchical-name-space"></a>Spazio nomi gerarchico

Quando lo spazio del nome gerarchico in non è abilitato:

* Non sono presenti autorizzazioni ereditate.
* Solo l'autorizzazione filesystem che funziona è il ruolo **dati di archiviazione xxxx** Azure, da assegnare all'utente direttamente in portale di Azure.

### <a name="default-hdfs-permissions"></a>Autorizzazioni HDFS predefinite

* Per impostazione predefinita, gli utenti non hanno accesso alla **/** cartella in HDFS (è necessario che il ruolo proprietario del BLOB di archiviazione per l'accesso abbia esito positivo).
* Per la directory di gestione temporanea per MapReduce e altre, viene creata una directory specifica dell'utente e vengono fornite le `sticky _wx` autorizzazioni. Gli utenti possono creare file e cartelle sottostanti, ma non possono esaminare altri elementi.

### <a name="url-auth"></a>Autenticazione URL

Se è abilitata l'autenticazione URL:

* Il file di configurazione conterrà i prefissi inclusi nell'autenticazione dell'URL (ad esempio `adl://` ).
* Se l'accesso è per questo URL, Ranger verificherà se l'utente è presente nell'elenco Consenti.
* Ranger non verificherà nessuno dei criteri con granularità fine.

## <a name="resource-groups"></a>Gruppi di risorse

Usare un nuovo gruppo di risorse per ogni cluster, in modo da poter distinguere le risorse del cluster.

## <a name="nsgs-firewalls-and-internal-gateway"></a>Gruppi, firewall e gateway interno

* Usare i gruppi di sicurezza di rete (gruppi) per bloccare le reti virtuali.
* Usare il firewall per gestire i criteri di accesso in uscita.
* Usare il gateway interno non aperto alla rete Internet pubblica.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) è il servizio Microsoft di gestione delle identità e degli accessi basato sul cloud.

### <a name="policies"></a>Criteri

* Disabilitare i criteri di accesso condizionale usando i criteri basati sull'indirizzo IP. Per questa operazione è necessario che gli endpoint di servizio siano abilitati nella reti virtuali in cui vengono distribuiti i cluster. Se si usa un servizio esterno per l'autenticazione a più fattori (ad eccezione di AAD), i criteri basati sull'indirizzo IP non funzioneranno

* `AllowCloudPasswordValidation` il criterio è obbligatorio per gli utenti federati. Poiché HDInsight utilizza direttamente il nome utente/password per ottenere i token da Azure AD, questo criterio deve essere abilitato per tutti gli utenti federati.

* Abilitare gli endpoint servizio se è necessario il bypass dell'accesso condizionale usando indirizzi IP attendibili.

### <a name="groups"></a>Gruppi

* Distribuire sempre i cluster con un gruppo.
* Usare Azure AD per gestire le appartenenze ai gruppi (più semplice che tentare di gestire i singoli servizi nel cluster).

### <a name="user-accounts"></a>Account utente

* Usare un account utente univoco per ogni scenario. Ad esempio, usare un account per l'importazione, usare un altro per query o altri processi di elaborazione.
* Usare i criteri di Ranger basati sui gruppi invece dei singoli criteri.
* Pianificare la gestione di utenti che non dovrebbero più accedere ai cluster.

## <a name="azure-active-directory-domain-services"></a>Servizi di dominio Azure Active Directory

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) fornisce servizi di dominio gestiti, ad esempio aggiunta a un dominio, criteri di gruppo, LDAP (Lightweight Directory Access Protocol) e autenticazione Kerberos/NTLM completamente compatibile con Windows Server Active Directory.

Azure AD DS è necessario affinché i cluster sicuri siano aggiunti a un dominio.
HDInsight non può dipendere da controller di dominio locali o controller di dominio personalizzati, perché introduce troppi punti di errore, condivisione delle credenziali, autorizzazioni DNS e così via. Per altre informazioni, vedere [domande frequenti su Azure AD DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Istanza di Azure AD DS

* Creare l'istanza di con `.onmicrosoft.com domain` . In questo modo, non saranno presenti più server DNS che fanno parte del dominio.
* Creare un certificato autofirmato per LDAPs e caricarlo in Azure AD DS.
* Usare una rete virtuale con peering per la distribuzione di cluster (quando si dispone di un numero di team che distribuiscono cluster HDInsight ESP, questo sarà utile). In questo modo non è necessario aprire le porte (gruppi) nella rete virtuale con il controller di dominio.
* Configurare il DNS per la rete virtuale correttamente (il nome di dominio Azure AD DS deve risolversi senza alcuna voce di file host).
* Se si sta limitando il traffico in uscita, assicurarsi di aver letto il [supporto del firewall in HDInsight](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Proprietà sincronizzate da Azure AD a Azure AD DS

* Azure AD connettere le sincronizzazioni da locale a Azure AD.
* Azure AD la sincronizzazione di DS da Azure AD.

Azure AD DS sincronizza periodicamente gli oggetti da Azure AD. Il pannello Azure AD DS nel portale di Azure Visualizza lo stato di sincronizzazione. Durante ogni fase della sincronizzazione, le proprietà univoche possono entrare in conflitto e rinominarle. Prestare attenzione al mapping delle proprietà da Azure AD a Azure AD DS.

Per altre informazioni, vedere [Azure ad popolamento di userPrincipalName](../../active-directory/hybrid/plan-connect-userprincipalname.md)e [come funziona la sincronizzazione di Azure AD DS](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Sincronizzazione dell'hash delle password

* Le password vengono sincronizzate in modo diverso rispetto ad altri tipi di oggetti. In Azure AD e Azure AD DS vengono sincronizzati solo gli hash delle password non reversibili.
* Da locale a Azure AD deve essere abilitato tramite AD Connect
* Azure AD per Azure AD la sincronizzazione DS è automatica (le latenze sono inferiori a 20 minuti).
* Gli hash delle password vengono sincronizzati solo quando è presente una password modificata. Quando si Abilita la sincronizzazione dell'hash delle password, tutte le password esistenti non vengono sincronizzate automaticamente quando vengono archiviate in modo irreversibile. Quando si modifica la password, gli hash delle password vengono sincronizzati.

### <a name="computer-objects-location"></a>Posizione degli oggetti computer

Ogni cluster è associato a una singola unità organizzativa. Viene eseguito il provisioning di un utente interno nell'unità organizzativa. Tutti i nodi sono aggiunti a un dominio nella stessa unità organizzativa.

### <a name="active-directory-administrative-tools"></a>Strumenti di amministrazione Active Directory

Per i passaggi relativi all'installazione degli strumenti di amministrazione Active Directory in una macchina virtuale Windows Server, vedere [Install Management Tools](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="cluster-creation-fails-repeatedly"></a>La creazione del cluster non riesce ripetutamente

Motivi più comuni:

* La configurazione DNS non è corretta. l'aggiunta a un dominio dei nodi del cluster non riesce.
* Gruppi sono troppo restrittive, impedendo l'aggiunta a un dominio.
* L'identità gestita non dispone di autorizzazioni sufficienti.
* Il nome del cluster non è univoco nei primi sei caratteri (con un altro cluster attivo o con un cluster eliminato).

## <a name="authentication-setup-and-configuration"></a>Configurazione e configurazione dell'autenticazione

### <a name="user-principal-name-upn"></a>Nome dell'entità utente (UPN)

* Usare lettere minuscole per tutti i servizi. i UPN non fanno distinzione tra maiuscole e minuscole nei cluster ESP, ma
* Il prefisso UPN deve corrispondere sia a SAMAccountName in Azure AD-DS. La corrispondenza con il campo mail non è obbligatoria.

### <a name="ldap-properties-in-ambari-configuration"></a>Proprietà LDAP nella configurazione di Ambari

Per un elenco completo delle proprietà di Ambari che interessano la configurazione del cluster HDInsight, vedere la pagina relativa alla configurazione dell' [autenticazione LDAP di Ambari](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html).

## <a name="next-steps"></a>Passaggi successivi

* [Enterprise Security Package configurazioni con Azure Active Directory Domain Services in HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Sincronizzare Azure Active Directory utenti a un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md).
