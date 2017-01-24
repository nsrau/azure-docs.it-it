---
title: Gestione di applicazioni con Azure Active Directory | Documentazione Microsoft
description: Questo articolo illustra i vantaggi dell&quot;integrazione di Azure Active Directory con le applicazioni locali, cloud e SaaS.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 95b96f10-2d5c-4b78-8af8-d3657a24140f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f6bceb2fd03230e4a39c22605ad1fea8dd422be1


---
# <a name="managing-applications-with-azure-active-directory"></a>Gestione di applicazioni con Azure Active Directory
Oltre alla gestione del flusso di lavoro o del contenuto effettivo, in generale le aziende prevedono due requisiti di base per tutte le applicazioni:

1. Per aumentare la produttività, è necessario che le applicazioni siano facili da individuare e con una procedura di accesso veloce
2. Per garantire la sicurezza e la governance, l'organizzazione deve controllare chi può accedere alle applicazioni e chi vi accede effettivamente

Nel mondo delle applicazioni cloud è possibile usare le identità per controllare "*CHI può fare COSA*".

Detto con la terminologia IT:

* *Chi*corrisponde a *identità*, ovvero la gestione di utenti e gruppi
* *Cosa* corrisponde a *gestione degli accessi*, ovvero la gestione degli accessi alle risorse protette

La combinazione di questi due componenti è nota come *Gestione delle identità e degli accessi*, definita dal gruppo [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) come "*la disciplina di sicurezza che consente alle persone corrette di accedere alle risorse appropriate, al momento opportuno e per i giusti motivi*".

Allora qual è il problema? Se le identità e gli accessi *non sono gestiti* in un'unica posizione con una soluzione integrata:

* Gli amministratori delle identità devono creare e aggiornare i singoli account utente in tutte le applicazioni separatamente, un'attività ridondante dispendiosa in termini di tempo.
* Gli utenti devono memorizzare più credenziali per accedere alle app che vogliono usare. Di conseguenza, gli utenti tendono a prendere nota delle password o a usare soluzioni di gestione delle password che comportano altri rischi per la sicurezza dei dati.
* Le attività ridondanti e dispendiose in termini di tempo riducono il tempo utile che gli utenti e gli amministratori possono dedicare ad attività aziendali che incrementano i profitti dell'azienda.

Quali sono i fattori che ostacolano l'adozione di soluzioni di gestione delle identità e degli accessi da parte delle aziende?

* La maggior parte delle soluzioni tecniche è basata su piattaforme software che devono essere distribuite e adattate da ogni organizzazione in base alle applicazioni specifiche.
* L'adozione di applicazioni cloud è spesso così rapida che le organizzazioni IT faticano a stare al passo con l'integrazione con le soluzioni di gestione delle identità e degli accessi esistenti.
* Gli strumenti di sicurezza e monitoraggio richiedono attività di personalizzazione e integrazione aggiuntive per soddisfare scenari end-to-end completi.

## <a name="azure-active-directory-integrated-with-applications"></a>Integrazione di Azure Active Directory con le applicazioni
Azure Active Directory è una soluzione IDaaS (Identity as a Service) Microsoft completa che offre i vantaggi seguenti:

* Abilitazione della gestione delle identità e degli accessi come servizio cloud. 
* Gestione centrale dell'accesso, accesso Single Sign-On (SSO) e creazione di report. 
* Supporto per la gestione integrata degli accessi per [migliaia di applicazioni](https://azure.microsoft.com/marketplace/active-directory/) nella raccolta di applicazioni, ad esempio Salesforce, Google Apps, Box, Concur e così via. 

Con Azure Active Directory tutte le applicazioni pubblicate per partner e clienti (aziende o utenti privati) hanno le stesse funzionalità di gestione delle identità e degli accessi.<br>  Ciò consente una riduzione notevole dei costi operativi.

Che cosa accade se è necessario implementare un'applicazione non ancora pubblicata nella raccolta di applicazioni? Nonostante la procedura risulti leggermente più dispendiosa in termini di tempo rispetto alla configurazione dell'accesso Single Sign-On delle applicazioni disponibili nella raccolta, Azure AD offre una procedura di configurazione guidata.

Il valore di Azure AD va oltre le "sole" applicazioni cloud. È anche possibile usarlo con applicazioni locali, fornendo accesso remoto sicuro, che consente di eliminare la necessità di VPN o altre implementazioni tradizionali di gestione dell'accesso remoto.

Poiché fornisce funzionalità di gestione degli accessi centralizzata e di accesso Single Sign-On per tutte le applicazioni aziendali, Azure AD è la soluzione che consente di risolvere i problemi principali legati alla sicurezza dei dati e alla produttività.

* Gli utenti possono accedere a più applicazioni con le stesse credenziali di accesso e avere più tempo disponibile per generare reddito o svolgere le operazioni aziendali.
* Gli amministratori delle identità possono gestire l'accesso alle applicazioni da un'unica posizione.

I vantaggi per l'utente e per l'azienda sono evidenti. Di seguito sono illustrati in modo più dettagliato i vantaggi per gli amministratori delle identità e per l'organizzazione.

## <a name="integrated-application-benefits"></a>Vantaggi dell'applicazione integrata
Il processo di accesso Single Sign-On prevede due passaggi:

* Autenticazione, ovvero la convalida dell'identità dell'utente.
* Autorizzazione, ovvero la decisione di consentire o bloccare l'accesso a una risorsa tramite criteri di accesso.

Quando si usa Azure AD per gestire le applicazioni e abilitare l'accesso Single Sign-On:

* L'autenticazione viene effettuata in base all'account dell'utente locale, ad esempio Active Directory, o all'account Azure AD.
* L'autorizzazione viene eseguita in base ai criteri di protezione e assegnazione di Azure AD per garantire un'esperienza utente coerente e per consentire all'amministratore di aggiungere assegnazioni, località e condizioni di autenticazione a più fattori (MFA) per ogni applicazione, indipendentemente dalle rispettive funzionalità interne.

È importante comprendere che la modalità in cui l'autorizzazione viene applicata all'applicazione di destinazione varia in base al modo in cui l'applicazione è stata integrata con Azure AD.

* **Applicazioni preintegrate dal provider di servizi** : come Office 365 e Azure, si tratta di applicazioni basate su Azure AD e che dipendono da Azure AD per tutte le funzionalità di gestione delle identità e degli accessi. L'accesso a queste applicazioni viene abilitato tramite le informazioni disponibili nella directory e il rilascio di token.
* **Applicazioni preintegrate da Microsoft e applicazioni personalizzate** : si tratta di applicazioni cloud indipendenti che si basano su una directory dell'applicazione interna e che possono operare indipendentemente da Azure AD. L'accesso a queste applicazioni viene abilitato tramite il rilascio di credenziali specifiche dell'applicazione mappate a un account dell'applicazione. A seconda delle funzionalità dell'applicazione, le credenziali possono essere costituite da un token federativo o da nome utente e password per un account di cui è stato precedentemente eseguito il provisioning nell'applicazione.
* **Applicazioni locali** : si tratta di applicazioni pubblicate tramite il proxy di applicazione di Azure AD che abilita l'accesso alle applicazioni locali. Queste applicazioni si basano su una directory locale centralizzata, ad esempio Windows Server Active Directory. L'accesso a queste applicazioni viene abilitato tramite l'attivazione del proxy per la distribuzione del contenuto dell'applicazione all'utente finale, rispettando il requisito di accesso locale.

Ad esempio, quando un utente viene assunto dall'organizzazione, sarà necessario creare un account utente in Azure AD per le operazioni di accesso principali. Se l'utente deve accedere a un'applicazione gestita, come ad esempio Salesforce, sarà necessario creare un account per l'utente in Salesforce e collegarlo all'account Azure, affinché l'accesso Single Sign-On possa funzionare. Quando un utente lascia l'organizzazione, è opportuno eliminare l'account Azure AD e tutti gli altri account negli archivi di gestione delle identità e degli accessi delle applicazioni a cui l'utente era autorizzato ad accedere.

## <a name="access-detection"></a>Rilevamento degli accessi
Nelle aziende moderne, i reparti IT spesso non sono a conoscenza di tutte le applicazioni cloud usate dai dipendenti. Con Cloud App Discovery, Azure AD fornisce anche una soluzione per individuare tali applicazioni.

## <a name="account-management"></a>Gestione account
Per tradizione, la gestione degli account nelle applicazioni è un processo manuale svolto dal personale IT o di supporto all'interno dell'organizzazione. Azure AD offre una soluzione di gestione degli account completamente automatizzata per tutte le applicazioni integrate dai provider di servizi e per quelle preintegrate da Microsoft che supportano il provisioning utenti automatizzato o il formato SAML JIT.

## <a name="automated-user-provisioning"></a>Provisioning utenti automatizzato
Alcune applicazioni offrono interfacce di automazione per la creazione e la rimozione (o disattivazione) di account. Se un provider offre un'interfaccia di questo tipo, questa verrà usata da Azure AD. In questo modo è possibile ridurre i costi operativi perché le attività amministrative vengono svolte automaticamente e migliorare la sicurezza dell'ambiente riducendo al minimo i rischi derivanti da accessi non autorizzati.

## <a name="access-management"></a>gestione degli accessi
Azure AD permette di gestire gli accessi alle applicazioni tramite assegnazioni singole o basate su ruolo. È anche possibile delegare la gestione degli accessi alle persone appropriate all'interno dell'organizzazione prevedendo una supervisione ottimale e riducendo il carico di lavoro del supporto tecnico.

## <a name="on-premises-applications"></a>Applicazioni locali
Il proxy di applicazione incorporato consente di pubblicare le applicazioni locali per gli utenti, al fine di garantire un'esperienza di accesso coerente per le applicazioni cloud e offrire i vantaggi delle funzionalità di monitoraggio, creazione di report e sicurezza disponibili in Azure AD.

## <a name="reporting-and-monitoring"></a>Monitoraggio e creazione di report
Azure AD offre funzionalità di creazione di report e monitoraggio preintegrate che consentono di ottenere informazioni sugli utenti autorizzati ad accedere alle applicazioni e su quando gli utenti le usano effettivamente.

## <a name="related-capabilities"></a>Funzionalità correlate
Azure AD consente di proteggere le applicazioni tramite criteri di accesso granulari e autenticazione a più fattori (MFA) preintegrata. Per altre informazioni sull'autenticazione a più fattori (MFA) di Azure, vedere [Autenticazione a più fattori di Azure](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Introduzione
Per iniziare a integrare applicazioni con Azure AD, vedere [Guida introduttiva all'integrazione di Azure Active Directory con le applicazioni](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Vedere anche
[Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Dec16_HO4-->


