---
title: Linee guida generali sulla sicurezza aziendale in Azure HDInsight
description: Alcune procedure consigliate che dovrebbero semplificare la distribuzione e la gestione dei pacchetti di sicurezza aziendale.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463206"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Informazioni generali sulla sicurezza aziendale e linee guida in Azure HDInsight

Quando si distribuisce un cluster HDInsight sicuro, esistono alcune procedure consigliate che dovrebbero semplificare la distribuzione e la gestione del cluster. Alcune informazioni e linee guida generali sono discusse qui.

## <a name="use-of-secure-cluster"></a>Utilizzo del cluster protetto

### <a name="recommended"></a>Consigliato

* Il cluster verrà utilizzato da più utenti contemporaneamente.
* Gli utenti hanno diversi livelli di accesso agli stessi dati.

### <a name="not-necessary"></a>Non necessario

* Si sta andando a eseguire solo processi automatizzati (come singolo account utente), un cluster standard è sufficiente.
* È possibile eseguire l'importazione dei dati usando un cluster standard e usare lo stesso account di archiviazione in un cluster sicuro diverso in cui gli utenti possono eseguire processi di analisi.

## <a name="use-of-local-account"></a>Utilizzo dell'account locale

* Se si utilizza un account utente condiviso o un account locale, sarà difficile identificare chi ha utilizzato l'account per modificare la configurazione o il servizio.
* L'utilizzo di account locali è problematico quando gli utenti non fanno più parte dell'organizzazione.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Criteri

* Per impostazione predefinita, Ranger utilizza **Deny** come criterio.

* Quando l'accesso ai dati viene effettuato tramite un servizio in cui è abilitata l'autorizzazione:When data access is made through a service where authorization is enabled:
  * Il plug-in di autorizzazione Ranger viene richiamato e dato il contesto della richiesta.
  * Ranger applica i criteri configurati per il servizio. Se i criteri Ranger hanno esito negativo, il controllo di accesso viene rinviato al file system. Alcuni servizi come MapReduce controllano solo se il file / cartella è di proprietà dello stesso utente che invia la richiesta. Servizi come Hive, verificare la corrispondenza`rwx`della proprietà o le autorizzazioni appropriate del file system ( ).

* Per Hive, oltre ad avere le autorizzazioni per fare le autorizzazioni `rwx`di creazione / aggiornamento / eliminazione, l'utente deve disporre delle autorizzazioni per la directory di archiviazione e tutte le directory secondarie.

* I criteri possono essere applicati ai gruppi (preferibilmente) anziché ai singoli utenti.

* L'autorizzatore Ranger valuterà tutte le politiche Ranger per quel servizio per ogni richiesta. Questa valutazione potrebbe avere un impatto sul tempo necessario per accettare il processo o la query.

### <a name="storage-access"></a>Accesso allo storage

* Se il tipo di archiviazione è WASB, non è coinvolto alcun token OAuth.
* Se Ranger ha eseguito l'autorizzazione, l'accesso all'archiviazione avviene utilizzando l'identità gestita.
* Se Ranger non ha eseguito alcuna autorizzazione, l'accesso allo spazio di archiviazione avviene utilizzando il token OAuth dell'utente.

### <a name="hierarchical-name-space"></a>Spazio dei nomi gerarchico

Quando lo spazio dei nomi gerarchico non è abilitato:

* Non sono presenti autorizzazioni ereditate.
* Solo l'autorizzazione del file system che funziona è il ruolo RBAC Dati di archiviazione XXXX da assegnare all'utente direttamente nel portale di Azure.Only filesystem permission that works is **Storage Data XXXX** RBAC role, to be assigned to the user directly in Azure portal.

### <a name="default-hdfs-permissions"></a>Autorizzazioni HDFS predefinite

* Per impostazione predefinita, gli utenti **/** non hanno accesso alla cartella in HDFS (devono essere nel ruolo di proprietario del BLOB di archiviazione affinché l'accesso abbia esito positivo).
* Per la directory di gestione temporanea per mapreduce e `sticky _wx` altri, viene creata una directory specifica dell'utente e vengono fornite le autorizzazioni. Gli utenti possono creare file e cartelle sotto, ma non possono guardare altri elementi.

### <a name="url-auth"></a>Autenticazione URL

Se l'autenticazione URL è abilitata:

* La configurazione conterrà i prefissi trattati nell'autenticazione url (come `adl://`).
* Se l'accesso è per questo URL, Ranger controllerà se l'utente è nell'elenco Consenti.
* Ranger non controllerrà nessuna delle polizze a grana fine.

## <a name="resource-groups"></a>Gruppi di risorse

Usare un nuovo gruppo di risorse per ogni cluster in modo da poter distinguere tra le risorse cluster.

## <a name="nsgs-firewalls-and-internal-gateway"></a>Gruppi di sicurezza di rete, firewall e gateway interno

* Usare i gruppi di sicurezza di rete per bloccare le reti virtuali.
* Utilizzare il firewall per gestire i criteri di accesso in uscita.
* Usare il gateway interno non aperto a Internet pubblico.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) è il servizio di gestione di identità e accessi basato su cloud di Microsoft.Azure Active Directory (Azure AD) is Microsoft's cloud-based identity and access management service.

### <a name="policies"></a>Criteri

* Disabilitare i criteri di accesso condizionale usando il criterio basato sull'indirizzo IP. Ciò richiede l'abilitazione degli endpoint di servizio nelle VNET in cui vengono distribuiti i cluster. Se si utilizza un servizio esterno per l'autenticazione a più fattori (diverso da AAD), il criterio basato sull'indirizzo IP non funzionerà

* `AllowCloudPasswordValidation`è necessario per gli utenti federati. Poiché HDInsight usa direttamente il nome utente/password per ottenere i token da Azure AD, questo criterio deve essere abilitato per tutti gli utenti federati.

* Abilitare gli endpoint del servizio se è necessario il bypass dell'accesso condizionale tramite indirizzi IP attendibili.

### <a name="groups"></a>Gruppi

* Distribuire sempre i cluster con un gruppo.
* Usare Azure AD per gestire le appartenenze ai gruppi (più semplice che tentare di gestire i singoli servizi nel cluster).

### <a name="user-accounts"></a>Account utente

* Utilizzare un account utente univoco per ogni scenario. Ad esempio, utilizzare un account per l'importazione, utilizzare un altro per query o altri processi di elaborazione.
* Utilizzare criteri Ranger basati su gruppo anziché singoli criteri.
* Avere un piano su come gestire gli utenti che non dovrebbero più avere accesso ai cluster.

## <a name="azure-active-directory-domain-services"></a>Servizi di dominio Azure Active Directory

[Servizi di dominio Azure Active Directory](../../active-directory-domain-services/overview.md) (Azure AD DS) fornisce servizi di dominio gestito, ad esempio l'aggiunta a un dominio, criteri di gruppo, LDAP (Lightweight Directory Access Protocol) e l'autenticazione Kerberos / NTLM completamente compatibile con Windows Server Active Directory.

Servizi di dominio Active Directory di Azure è necessario per l'aggiunta di cluster protetti a un dominio.
HDInsight non può dipendere da controller di dominio locali o da controller di dominio personalizzati, in quanto introduce troppi punti di errore, condivisione delle credenziali, autorizzazioni DNS e così via. Per altre informazioni, vedere Domande frequenti su Servizi di dominio Active Directory di [Azure AD](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Istanza di Servizi di dominio Active Directory di AzureAzure AD DS instance

* Creare l'istanza `.onmicrosoft.com domain`con l'oggetto . In questo modo, non ci saranno più server DNS che servono il dominio.
* Creare un certificato autofirmato per LDAPS e caricarlo in Azure AD DS.
* Usare una rete virtuale con peering per la distribuzione di cluster (quando si dispone di un numero di team che distribuiscono cluster ESP HDInsight, questo sarà utile). In questo modo non è necessario aprire le porte (NSG) nella rete virtuale con il controller di dominio.
* Configurare correttamente il DNS per la rete virtuale (il nome di dominio di Azure AD DS deve essere risolto senza voci di file host).
* Se si limita il traffico in uscita, assicurarsi di aver letto il [supporto del firewall in HDInsightIf](../hdinsight-restrict-outbound-traffic.md) you're restricting outbound traffic, make sure that you have read through the firewall support in HDInsight

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Proprietà sincronizzate da Azure AD ad Azure AD DSProperties synced from Azure AD to Azure AD DS

* La connessione di Azure AD viene sincronizzata dall'ambiente locale ad Azure AD.
* Sincronizzazioni di Servizi di dominio Active Directory di Azure da Azure AD.

Servizi di dominio Active Directory di Azure sincronizza periodicamente gli oggetti da Azure AD. Il pannello Servizi di dominio Active Directory di Azure nel portale di Azure visualizza lo stato di sincronizzazione. Durante ogni fase della sincronizzazione, le proprietà univoche possono entrare in conflitto e rinominarle. Prestare attenzione al mapping delle proprietà da Azure AD ad Azure AD DS.

Per altre informazioni, vedere [Popolazione di UserPrincipalName](../../active-directory/hybrid/plan-connect-userprincipalname.md)di Azure AD e Funzionamento della sincronizzazione di Servizi di dominio Active Directory di [Azure.](../../active-directory-domain-services/synchronization.md)

### <a name="password-hash-sync"></a>Sincronizzazione dell'hash delle password

* Le password vengono sincronizzate in modo diverso dagli altri tipi di oggetto. Solo gli hashe delle password non reversibili vengono sincronizzati in Azure AD e Azure AD DS
* L'on-premise in Azure AD deve essere abilitato tramite AD Connect
* La sincronizzazione da Azure AD ad Azure AD DS è automatica (le latenze sono meno di 20 minuti).
* Gli hashe della password vengono sincronizzati solo quando viene modificata una password. Quando si abilita la sincronizzazione dell'hash delle password, tutte le password esistenti non vengono sincronizzate automaticamente in quanto vengono archiviate in modo irreversibile. Quando si modifica la password, gli isemi delle password vengono sincronizzati.

### <a name="computer-objects-location"></a>Posizione degli oggetti computer

Ogni cluster è associato a una singola unità organizzativa. Viene eseguito il provisioning di un utente interno nell'unità organizzativa. Tutti i nodi sono aggiunti a un dominio nella stessa unità organizzativa.

### <a name="active-directory-administrative-tools"></a>Strumenti di amministrazione di Active Directory

Per la procedura di installazione degli strumenti di amministrazione di Active Directory in una macchina virtuale Windows Server, vedere Installare gli strumenti di [gestione.](../../active-directory-domain-services/tutorial-create-management-vm.md)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="cluster-creation-fails-repeatedly"></a>Creazione cluster non riesce ripetutamente

Motivi più comuni:

* La configurazione DNS non è corretta, l'aggiunta a un dominio dei nodi del cluster ha esito negativo.
* I gruppi di sicurezza di sicurezza di sicurezza sono troppo restrittivi, impedendo l'aggiunta a un dominio.
* L'identità gestita non dispone di autorizzazioni sufficienti.
* Il nome del cluster non è univoco nei primi sei caratteri (con un altro cluster attivo o con un cluster eliminato).

## <a name="next-steps"></a>Passaggi successivi

* [Configurazioni dei pacchetti di sicurezza aziendali con Servizi di dominio Azure Active Directory in HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Sincronizzare gli utenti di Azure Active Directory con un cluster HDInsight.](../hdinsight-sync-aad-users-to-cluster.md)
