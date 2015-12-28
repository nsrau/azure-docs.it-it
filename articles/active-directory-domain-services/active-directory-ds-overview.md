<properties
	pageTitle="Anteprima di Servizi di dominio Azure Active Directory: Panoramica | Microsoft Azure"
	description="Panoramica di Servizi di dominio Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="maheshu"/>

# Servizi di dominio Azure AD *(anteprima)*

## Panoramica
I servizi di infrastruttura di Azure consentono di distribuire un'ampia gamma di soluzioni di computing in modo agile. Con Macchine virtuali di Azure, è possibile eseguire la distribuzione quasi istantaneamente, con un pagamento al minuto. Grazie al supporto per Windows, Linux, SQL Server, Oracle, IBM, SAP e BizTalk, è possibile distribuire qualsiasi carico di lavoro e qualunque linguaggio su quasi tutti i sistemi operativi. Questi vantaggi permettono di eseguire la migrazione di applicazioni legacy distribuite in locale in Azure, per generare risparmi sulle spese operative.

Uno degli aspetti più importanti della migrazione delle applicazioni locali in Azure è la possibilità di gestire le esigenze relative alle identità di tali applicazioni. Le applicazioni compatibili con le directory possono usare LDAP per l'accesso in lettura o scrittura alla directory aziendale oppure usare l'autenticazione integrata di Windows (autenticazione NTLM o Kerberos) per autenticare gli utenti finali. Le applicazioni line-of-business in esecuzione in Windows Server vengono in genere distribuite in computer aggiunti a un dominio e quindi essere gestite in modo sicuro tramite Criteri di gruppo. Per spostare le applicazioni locali nel cloud, è necessario risolvere le dipendenze nell'infrastruttura delle identità aziendali.

Per soddisfare le esigenze in termini di gestione delle identità delle applicazioni distribuite in Azure, spesso gli amministratori adottano una delle soluzioni seguenti:

- Distribuzione di una connessione VPN da sito a sito tra i carichi di lavoro in esecuzione nei servizi di infrastruttura di Azure e la directory aziendale locale.
- Estensione dell'infrastruttura di dominio/foresta Active Directory aziendale tramite la configurazione di controller di dominio di replica usando macchine virtuali di Azure.
- Distribuzione di un dominio autonomo in Azure tramite controller di dominio distribuiti come macchine virtuali di Azure.

Tutti questi approcci tuttavia prevedono costi elevati e un carico di lavoro amministrativo significativo. Gli amministratori devono distribuire i controller di dominio tramite macchine virtuali in Azure. Devono quindi gestire, proteggere, applicare patch, monitorare, eseguire il backup e risolvere i problemi relativi a tali macchine virtuali. L'utilizzo di connessioni VPN alla directory locale rende i carichi di lavoro distribuiti in Azure vulnerabili a errori o interruzioni di rete temporanee. Questo a sua volta determina tempi di attività inferiori e affidabilità ridotta per le applicazioni.

Servizi di dominio Azure AD è un'alternativa molto più semplice.


## Introduzione a Servizi di dominio Azure AD
Servizi di dominio Azure AD offre servizi di dominio gestiti, ad esempio aggiunta a un dominio, Criteri di gruppo, LDAP, autenticazione Kerberos/NTLM e così via, completamente compatibili con Windows Server Active Directory. Servizi di dominio Azure AD consente di utilizzare questi servizi di dominio senza dover distribuire, gestire e applicare patch ai controller di dominio nel cloud. Servizi di dominio Azure AD si integra con il tenant Azure AD esistente per consentire agli utenti di eseguire l'accesso usando le proprie credenziali aziendali. È anche possibile usare i gruppi e gli account utente esistenti per proteggere l'accesso alle risorse e garantire uno spostamento semplificato delle risorse locali nei servizi di infrastruttura di Azure.

Servizi di dominio Azure AD funziona perfettamente indipendentemente dal fatto che il tenant Azure AD sia di tipo solo cloud o sincronizzato con l'istanza di Active Directory locale.

### Servizi di dominio Azure AD per le organizzazioni solo cloud
Un tenant Azure AD solo cloud, spesso definito anche tenant gestito, non dispone di un sistema di gestione delle identità locale. In altre parole, gli utenti, le relative password e le appartenenze ai gruppi sono nativi nel cloud, ovvero creati e gestiti in Azure AD. Si supponga che Contoso sia un tenant Azure AD solo cloud. Come illustrato nella figura seguente, l'amministratore di Contoso ha configurato una rete virtuale nei servizi di infrastruttura di Azure. I carichi di lavoro di server e applicazioni sono distribuiti in questa rete virtuale in macchine virtuali di Azure. Poiché Contoso è un tenant solo cloud, tutte le identità degli utenti, le relative credenziali e le appartenenze ai gruppi sono create e gestite in Azure.

![Panoramica di Servizi di dominio Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

L'amministratore IT di Contoso può abilitare Servizi di dominio Azure AD per il tenant Azure AD e scegliere di rendere disponibili i servizi di dominio in questa rete virtuale. Una volta configurato, Servizi di dominio Azure AD esegue il provisioning di un dominio gestito e lo rende disponibile nella rete virtuale. Tutti gli account utente, le appartenenze ai gruppi e le credenziali utente disponibili nel tenant Azure AD di Contoso sono anche disponibili in questo dominio appena creato. Questa funzionalità consente agli utenti di accedere al dominio usando le credenziali aziendali, ad esempio per la connessione remota ai computer aggiunti al dominio tramite Desktop remoto. Gli amministratori possono eseguire il provisioning dell'accesso alle risorse nel dominio usando le appartenenze ai gruppi esistenti. Le applicazioni distribuite nelle macchine virtuali all'interno della rete virtuale possono usufruire dei servizi di dominio, ad esempio aggiunta a un dominio, lettura LDAP, binding LDAP, autenticazione NTLM e Kerberos, Criteri di gruppo e così via.

Di seguito sono illustrati alcuni degli aspetti più importanti del dominio gestito di cui Servizi di dominio Azure AD esegue il provisioning:

- L'amministratore IT di Contoso non deve gestire, applicare patch o monitorare questo dominio o i controller di dominio per questo dominio gestito.
- Non è necessario gestire la replica Active Directory per il dominio. Gli account utente, le appartenenze ai gruppi e le credenziali del tenant Azure AD di Contoso sono automaticamente disponibili all'interno del dominio gestito.
- Poiché il dominio è gestito da Servizi di dominio Azure AD, l'amministratore IT di Contoso non dispone di privilegi di amministratore di dominio o amministratore dell'organizzazione per questo dominio.


### Servizi di dominio Azure AD per le organizzazioni ibride
Le organizzazioni con un'infrastruttura IT ibrida utilizzano una combinazione di risorse cloud e risorse locali. Queste organizzazioni sincronizzano le informazioni relative alle identità della directory locale con il tenant Azure AD. Poiché le organizzazioni ibride hanno l'obiettivo di eseguire la migrazione della maggior parte delle applicazioni locali nel cloud, in particolare le applicazioni legacy compatibili con le directory, Servizi di dominio Azure AD può risultare estremamente utile.

Litware Corporation ha distribuito [Azure AD Connect](../active-directory/active-directory-aadconnect.md) in modo da sincronizzare le informazioni relative alle identità della directory locale con il tenant Azure AD. Sono inclusi gli account utente, i relativi hash delle credenziali per l'autenticazione (sincronizzazione delle password) e le appartenenze ai gruppi. Si noti che per **usare Servizi di dominio Azure AD, la sincronizzazione delle password è un requisito obbligatorio per le organizzazioni ibride**. Questo perché nel dominio gestito fornito da Servizi di dominio Azure AD le credenziali degli utenti sono necessarie per autenticare gli utenti tramite i metodi di autenticazione NTLM o Kerberos.

![Servizi di dominio Azure AD per Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

La figura precedente mostra l'uso di Servizi di dominio Azure AD nelle organizzazioni con un'infrastruttura IT ibrida, come Litware Corporation. I carichi di lavoro di server e applicazioni di Litware che richiedono i servizi di dominio sono distribuiti in una rete virtuale nei servizi di infrastruttura di Azure. L'amministratore IT di Litware può abilitare Servizi di dominio Azure AD per il tenant Azure AD e scegliere di rendere disponibile un dominio gestito in questa rete virtuale. Poiché Litware è un'organizzazione con un'infrastruttura IT ibrida, gli account utente, i gruppi e le credenziali vengono sincronizzati nel tenant Azure AD dalla directory locale. Questa funzionalità consente agli utenti di accedere al dominio usando le credenziali aziendali, ad esempio per la connessione remota ai computer aggiunti al dominio tramite Desktop remoto. Gli amministratori possono eseguire il provisioning dell'accesso alle risorse nel dominio usando le appartenenze ai gruppi esistenti. Le applicazioni distribuite nelle macchine virtuali all'interno della rete virtuale possono usufruire dei servizi di dominio, ad esempio aggiunta a un dominio, lettura LDAP, binding LDAP, autenticazione NTLM e Kerberos, Criteri di gruppo e così via.

Di seguito sono illustrati alcuni degli aspetti più importanti del dominio gestito di cui Servizi di dominio Azure AD esegue il provisioning:

- Si tratta di un dominio gestito autonomo. Non si tratta di un'estensione del dominio locale di Litware.
- L'amministratore IT di Litware non deve gestire, applicare patch o monitorare questo dominio o i controller di dominio per questo dominio gestito.
- Non è necessario gestire la replica Active Directory nel dominio. Gli account utente, le appartenenze ai gruppi e le credenziali della directory locale di Litware sono sincronizzati in Azure AD tramite Azure AD Connect. Queste informazioni saranno automaticamente disponibili all'interno del dominio gestito.
- Poiché il dominio è gestito da Servizi di dominio Azure AD, l'amministratore IT di Litware non dispone di privilegi di amministratore di dominio o amministratore dell'organizzazione per questo dominio.


## Vantaggi
Servizi di dominio Azure AD offre i vantaggi seguenti:

-	**Semplicità**: consente di soddisfare le esigenze di gestione delle identità delle macchine virtuali distribuite nei servizi di infrastruttura di Azure con pochi semplici clic, senza dover distribuire e gestire un'infrastruttura di identità in Azure o configurare di nuovo la connettività all'infrastruttura di identità locale.

-	**Integrazione**: Servizi di dominio Azure AD è strettamente integrato con il tenant Azure AD. Azure AD è una directory aziendale basata sul cloud integrata che soddisfa le esigenze delle applicazioni moderne e delle applicazioni compatibili con le directory tradizionali.

-	**Compatibilità**: poiché Servizi di dominio Azure AD è basato sulla comprovata infrastruttura di classe enterprise di Windows Server Active Directory, le applicazioni possono usufruire di un maggiore livello di compatibilità con le funzionalità di Windows Server Active Directory. Si noti che non tutte le funzionalità disponibili in Windows Server Active Directory sono attualmente disponibili in Servizi di dominio Azure AD. Le funzionalità disponibili sono tuttavia compatibili con le funzionalità di Windows Server Active Directory corrispondenti usate nell'infrastruttura locale. Le funzionalità LDAP, Kerberos, NTLM, Criteri di gruppo e di aggiunta a un dominio fornite da Servizi di dominio Azure AD costituiscono un'offerta matura testata e perfezionata nel corso di diverse versioni di Windows Server.

-	**Conveniente**: Servizi di dominio Azure AD consente di evitare i costi di gestione e di infrastruttura associati alla gestione dell'infrastruttura di identità per il supporto delle applicazioni compatibili con le directory tradizionali. È possibile spostare queste applicazioni nei servizi di infrastruttura di Azure e ottenere maggiori risparmi sulle spese operative.

<!---HONumber=AcomDC_1217_2015-->