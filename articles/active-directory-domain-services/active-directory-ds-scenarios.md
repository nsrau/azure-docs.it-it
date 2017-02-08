---
title: 'Servizi di dominio Azure Active Directory: Scenari di distribuzione | Microsoft Docs'
description: Scenari di distribuzione per i Servizi di dominio Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 359653f29adc538a4fe2f2143e8132bdd9a9d15b


---
# <a name="deployment-scenarios-and-use-cases"></a>Scenari di distribuzione e casi d'uso
Questa sezione illustra alcuni scenari e casi d'uso che traggono vantaggio dall'utilizzo di Servizi di dominio Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Gestione sicura e semplificata delle macchine virtuali di Azure
È possibile utilizzare Servizi di dominio Azure Active Directory per gestire le macchine virtuali di Azure in modo semplificato. Le macchine virtuali di Azure possono appartenere al dominio gestito, consentendo di utilizzare le credenziali aziendali di Active Directory per effettuare l'accesso. Questo approccio consente di evitare complicazioni con la gestione delle credenziali, ad esempio la gestione degli account di amministratore locali su ciascuna delle macchine virtuali di Azure.

Le macchine virtuali del server aggiunte al dominio gestito possono inoltre essere gestite e protette tramite i criteri di gruppo. È possibile applicare le linee di base della sicurezza necessaria alle macchine virtuali di Azure e bloccarle in conformità con le linee guida sulla sicurezza aziendale. Ad esempio, è possibile utilizzare le funzionalità di gestione dei criteri di gruppo per limitare i tipi di applicazioni che possono essere avviate su tali macchine virtuali.

![Gestione ottimizzata delle macchine virtuali di Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Man mano che i server e altri elementi dell'infrastruttura raggiungono la fine del ciclo di vita, Contoso sposta molte delle applicazioni attualmente ospitate in locale nel cloud. Lo standard IT corrente prevede che i server che ospitano applicazioni aziendali sia aggiunto a un dominio e gestito tramite Criteri di gruppo. L'amministratore IT di Contoso preferisce aggiungere a un dominio le macchine virtuali distribuite in Azure per semplificarne la gestione. Di conseguenza, gli amministratori e gli utenti possono accedere utilizzando le proprie credenziali aziendali. Allo stesso tempo, le macchine possono essere configurate per essere conformi alle linee di base della sicurezza necessaria utilizzando i criteri di gruppo. Contoso preferisce non dover distribuire, monitorare e gestire i controller di dominio in Azure per proteggere le macchine virtuali di Azure. Servizi di dominio Azure Active Directory è quindi un'ottima soluzione per questo caso di utilizzo.

**Note sulla distribuzione**

Considerare i seguenti punti importanti per questo scenario di distribuzione:

* Per impostazione predefinita, i domini gestiti forniti da Servizi di dominio Azure AD offrono una singola struttura di unità organizzativa di tipo semplice. Tutte le macchine aggiunte al dominio si trovano in una singola unità organizzativa di tipo semplice. È tuttavia possibile scegliere di creare unità organizzative personalizzate.
* Servizi di dominio Azure AD supporta Criteri di gruppo semplici nel formato di un oggetto Criteri di gruppo predefinito per i contenitori di utenti e computer. Non è possibile applicare Criteri di gruppo per unità organizzativa o reparto, ma è necessario applicare un filtro Strumentazione gestione Windows (WMI) o creare oggetti Criteri di gruppo personalizzati.
* Servizi di dominio Azure AD supporta lo schema dell'oggetto computer di Active Directory di base. Non è possibile estendere lo schema dell'oggetto computer.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Spostamento di un'applicazione locale che usa l'autenticazione di binding LDAP nei servizi di infrastruttura di Azure
![Binding LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso ha un'applicazione locale acquistata da un fornitore di software indipendente molti anni fa. L'applicazione è attualmente in modalità manutenzione presso il fornitore di software indipendente e le modifiche apportate all'applicazione sono estremamente costose per Contoso. L'applicazione ha un front-end basato sul Web che raccoglie le credenziali degli utenti tramite un modulo Web e che autentica gli utenti tramite un binding LDAP con l'istanza di Active Directory aziendale. Contoso vorrebbe eseguire la migrazione di questa applicazione ai servizi di infrastruttura di Azure. Sarebbe opportuno che l'applicazione funzionasse così com'è, senza richiedere modifiche. Gli utenti dovrebbero inoltre poter eseguire l'autenticazione usando le credenziali aziendali esistenti senza che sia necessaria una formazione sulle nuove procedure. In altre parole, gli utenti non devono accorgersi che è stata eseguita la migrazione dell'applicazione e che l'applicazione non è più in esecuzione in locale.

**Note sulla distribuzione**

Considerare i seguenti punti importanti per questo scenario di distribuzione:

* Assicurarsi che l'applicazione non debba eseguire operazioni di modifica/scrittura nella directory. L'accesso LDAP in scrittura ai domini gestiti forniti da Servizi di dominio Azure AD non è supportato.
* Non è possibile modificare direttamente le password nel dominio gestito. Gli utenti finali possono modificare le password tramite il meccanismo di reimpostazione della password self-service o nella directory locale. Queste modifiche vengono automaticamente sincronizzate e rese disponibili nel dominio gestito.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Spostamento di un'applicazione locale che usa la lettura LDAP per accedere alla directory nei servizi di infrastruttura di Azure
Contoso ha un'applicazione line-of-business locale (LOB) che è stata sviluppata quasi un decennio fa. L'applicazione è compatibile con le directory ed è stata progettata per funzionare con Windows Server Active Directory. L'applicazione usa LDAP (Lightweight Directory Access Protocol) per la lettura di informazioni/attributi relativi agli utenti da Active Directory. L'applicazione non modifica gli attributi né esegue operazioni di scrittura nella directory. Contoso desidera eseguire la migrazione di questa applicazione ai servizi di infrastruttura di Azure e dismettere l'hardware locale obsoleto che ospita l'applicazione. L'applicazione non può essere riscritta per l'uso di API di directory moderne, ad esempio l'API Graph basata su REST di Azure AD. Di conseguenza, un'opzione di spostamento è opportuna quando è possibile eseguire la migrazione dell'applicazione per l'esecuzione nel cloud senza modificare il codice o riscrivere l'applicazione.

**Note sulla distribuzione**

Considerare i seguenti punti importanti per questo scenario di distribuzione:

* Assicurarsi che l'applicazione non debba eseguire operazioni di modifica/scrittura nella directory. L'accesso LDAP in scrittura ai domini gestiti forniti da Servizi di dominio Azure AD non è supportato.
* Assicurarsi che l'applicazione non necessiti di uno schema Active Directory personalizzato o esteso. Le estensioni dello schema non sono supportate in Servizi di dominio Azure AD.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Eseguire la migrazione di un servizio locale o di un'applicazione daemon ai servizi di infrastruttura di Azure
Alcune applicazioni sono costituite da più livelli, dove uno dei livelli deve eseguire chiamate autenticate a un livello di back-end, ad esempio un livello di database. Gli account di servizio di Active Directory vengono comunemente utilizzati per questi casi di utilizzo. È possibile sollevare e spostare tali applicazioni per i servizi dell'infrastruttura Azure e utilizzare Servizi di dominio Azure Active Directory per le esigenze di identità di queste applicazioni. È possibile scegliere di utilizzare lo stesso account di servizio che viene sincronizzato dalla directory locale ad Azure AD. In alternativa, è possibile creare innanzitutto un'unità organizzativa, quindi un account di servizio separato nell'unità organizzativa, per distribuire tali applicazioni.

![Account del servizio mediante WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso ha un'applicazione di insieme di credenziali appositamente sviluppata e personalizzata che include un front-end Web, un server SQL e un server FTP back-end. L'autenticazione integrata di Windows degli account del servizio viene usata per eseguire l'autenticazione del front-end Web con il server FTP. Il front-end Web è configurato per l'esecuzione come account del servizio. Il server back-end è configurato per autorizzare l'accesso dall'account del servizio per il front-end Web. Contoso preferisce non dover distribuire una macchina virtuale del controller di dominio nel cloud per spostare questa applicazione nei servizi dell'infrastruttura di Azure. L'amministratore IT di Contoso può distribuire i server che ospitano il front-end Web, il server SQL e il server FTP in macchine virtuali di Azure. Dopodiché, queste macchine virtuali vengono aggiunte al dominio gestito di Servizi di dominio Azure AD. Successivamente, sarà possibile usare lo stesso account del servizio nella directory locale per l'autenticazione dell'app. Questo account del servizio viene sincronizzato con il dominio gestito di servizi di Servizi di dominio Azure Active Directory ed è disponibile per l'utilizzo.

**Note sulla distribuzione**

Considerare i seguenti punti importanti per questo scenario di distribuzione:

* Assicurarsi che l'applicazione usi nome utente e password per l'autenticazione. L'autenticazione basata su certificati/smart card non è supportata da Servizi di dominio Azure AD.
* Non è possibile modificare direttamente le password nel dominio gestito. Gli utenti finali possono modificare le password tramite il meccanismo di reimpostazione della password self-service o nella directory locale. Queste modifiche vengono automaticamente sincronizzate e rese disponibili nel dominio gestito.

## <a name="azure-remoteapp"></a>Azure RemoteApp
Azure RemoteApp consente all'amministratore di Contoso di creare una raccolta aggiunta al dominio. Questa funzione consente alle applicazioni remote gestite da Azure RemoteApp potranno quindi essere eseguite in computer aggiunti al dominio e potranno accedere altre risorse tramite l'autenticazione integrata di Windows. Contoso può usare Servizi di dominio Azure Active Directory per fornire un dominio gestito usato dalle raccolte aggiunte al dominio di Azure RemoteApp.

![Azure RemoteApp](./media/active-directory-domain-services-scenarios/azure-remoteapp.png)

Per altre informazioni su questo scenario di distribuzione, vedere il post del blog su Servizi Desktop remoto che illustra come [sollevare e spostare i carichi di lavoro con Azure RemoteApp e Servizi di dominio Azure Active Directory](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).




<!--HONumber=Dec16_HO4-->


