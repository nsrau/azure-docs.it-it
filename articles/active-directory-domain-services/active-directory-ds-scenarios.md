<properties
	pageTitle="Anteprima dei servizi di dominio di Azure Active Directory: Scenari di distribuzione | Microsoft Azure"
	description="Scenari di distribuzione per i Servizi di dominio Azure Active Directory"
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
	ms.date="04/11/2016"
	ms.author="maheshu"/>


# Scenari di distribuzione e casi d'uso
Questa sezione illustra alcuni scenari e casi d'uso che possono trarre vantaggio dall'utilizzo di Servizi di dominio Azure Active Directory (AD).

## Gestione sicura e semplificata delle macchine virtuali di Azure
Man mano che i server e altri elementi dell'infrastruttura raggiungono la fine del ciclo di vita, Contoso sposta molte delle applicazioni attualmente ospitate in locale nel cloud. Lo standard IT corrente prevede che i server che ospitano applicazioni aziendali sia aggiunto a un dominio e gestito tramite Criteri di gruppo. L'amministratore IT di Contoso preferisce aggiungere a un dominio le macchine virtuali distribuite in Azure per semplificarne la gestione, ovvero eseguire l'accesso usando le proprie credenziali aziendali. Contoso preferisce non dover distribuire, monitorare e gestire i controller di dominio in Azure per proteggere le macchine virtuali di Azure.

Aspetti importanti di cui tenere conto durante la valutazione di questo scenario:

- I domini gestiti forniti da Servizi di dominio Azure AD supportano solo una struttura di unità organizzativa di tipo semplice. Tutte i computer aggiunti a un dominio devono risiedere in un'unica unità organizzativa semplice e le strutture di unità organizzative di tipo gerarchico non sono supportate.
- Servizi di dominio Azure AD supporta Criteri di gruppo semplici nel formato di un oggetto Criteri di gruppo predefinito per i contenitori di utenti e computer. Non è possibile applicare Criteri di gruppo per unità organizzativa o reparto, ma è necessario applicare un filtro Strumentazione gestione Windows (WMI) o creare oggetti Criteri di gruppo personalizzati.
- Servizi di dominio Azure AD supporta lo schema dell'oggetto computer di Active Directory di base. Non è possibile estendere lo schema dell'oggetto computer.


## Spostamento di un'applicazione locale che usa l'autenticazione di binding LDAP nei servizi di infrastruttura di Azure
Contoso ha un'applicazione locale acquistata da un fornitore di software indipendente molti anni fa. L'applicazione è attualmente in modalità manutenzione presso il fornitore di software indipendente e le modifiche apportate all'applicazione sono estremamente costose per Contoso. L'applicazione ha un front-end basato sul Web che raccoglie le credenziali degli utenti tramite un modulo Web e che autentica gli utenti tramite un binding LDAP con l'istanza di Active Directory aziendale. Contoso vorrebbe eseguire la migrazione di questa applicazione ai servizi di infrastruttura di Azure. Sarebbe opportuno che l'applicazione funzionasse così com'è, senza richiedere modifiche. Gli utenti dovrebbero inoltre poter eseguire l'autenticazione usando le credenziali aziendali esistenti senza che sia necessaria una formazione sulle nuove procedure. In altre parole, gli utenti non devono accorgersi che è stata eseguita la migrazione dell'applicazione e che l'applicazione non è più in esecuzione in locale.

Aspetti importanti di cui tenere conto durante la valutazione di questo scenario:

- Assicurarsi che l'applicazione non debba eseguire operazioni di modifica/scrittura nella directory. L'accesso LDAP in scrittura ai domini gestiti forniti da Servizi di dominio Azure AD non è supportato.
- Gli utenti non possono modificare direttamente le proprie password nel dominio gestito. Gli utenti possono modificare le password tramite il meccanismo di reimpostazione della password self-service o nella directory locale. Queste modifiche verranno automaticamente sincronizzate e disponibili nel dominio gestito.


## Spostamento di un'applicazione locale che usa la lettura LDAP per accedere alla directory nei servizi di infrastruttura di Azure
Contoso ha un'applicazione line-of-business locale che è stata sviluppata quasi un decennio fa. L'applicazione è compatibile con le directory ed è stata progettata per funzionare con Windows Server Active Directory. L'applicazione usa LDAP (Lightweight Directory Access Protocol) per la lettura di informazioni/attributi relativi agli utenti da Active Directory. L'applicazione non modifica gli attributi né esegue operazioni di scrittura nella directory. Contoso desidera eseguire la migrazione di questa applicazione ai servizi di infrastruttura di Azure e dismettere l'hardware locale obsoleto che ospita l'applicazione. L'applicazione non può essere riscritta per l'uso di API di directory moderne, ad esempio l'API Graph basata su REST di Azure AD. Di conseguenza, un'opzione di spostamento è opportuna quando è possibile eseguire la migrazione dell'applicazione per l'esecuzione nel cloud senza modificare il codice o riscrivere l'applicazione.

Aspetti importanti di cui tenere conto durante la valutazione di questo scenario:

- Assicurarsi che l'applicazione non debba eseguire operazioni di modifica/scrittura nella directory. L'accesso LDAP in scrittura ai domini gestiti forniti da Servizi di dominio Azure AD non è supportato.
- Assicurarsi che l'applicazione non necessiti di uno schema Active Directory personalizzato o esteso. Le estensioni dello schema non sono supportate in Servizi di dominio Azure AD.
- Assicurarsi che l'applicazione non richieda l'accesso LDAPS. LDAPS non è supportato da Servizi di dominio Azure AD.


## Eseguire la migrazione di un servizio locale o di un'applicazione daemon ai servizi di infrastruttura di Azure
Contoso ha un'applicazione di insieme di credenziali appositamente sviluppata e personalizzata che include un front-end Web, un server SQL e un server FTP back-end. Prevede l'autenticazione integrata di Windows e usa gli account del servizio per eseguire l'autenticazione del front-end Web con il server FTP. Contoso vuole spostare questa applicazione nei servizi di infrastruttura di Azure e preferisce non dover distribuire una macchina virtuale controller di dominio nel cloud al fine di supportare le esigenze in termini di gestione delle identità per questa applicazione. L'amministratore IT di Contoso può distribuire i server che ospitano il front-end Web, il server SQL e il server FTP in macchine virtuali in Azure e aggiungerle a un dominio di Servizi di dominio Azure AD. In questo modo, sarà possibile usare lo stesso account del servizio nella directory per l'autenticazione dell'app.

Aspetti importanti di cui tenere conto durante la valutazione di questo scenario:

- Assicurarsi che l'applicazione usi nome utente e password per l'autenticazione. L'autenticazione basata su certificati/smart card non è supportata da Servizi di dominio Azure AD.


## Azure RemoteApp
Azure RemoteApp consente all'amministratore di Contoso di creare una raccolta aggiunta al dominio. Le applicazioni remote gestite da Azure RemoteApp potranno quindi essere eseguite in computer aggiunti al dominio e potranno accedere altre risorse tramite l'autenticazione integrata di Windows. Contoso può usare Servizi di dominio Azure Active Directory per fornire un dominio gestito usato dalle raccolte aggiunte al dominio di Azure RemoteApp.

Per altre informazioni su questo scenario di distribuzione, vedere il post del blog su Servizi Desktop remoto che illustra come [sollevare e spostare i carichi di lavoro con Azure RemoteApp e Servizi di dominio Azure Active Directory](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).

<!---HONumber=AcomDC_0427_2016-->