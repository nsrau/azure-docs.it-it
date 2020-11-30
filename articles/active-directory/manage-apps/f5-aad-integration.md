---
title: Azure AD l'accesso ibrido sicuro con F5 | Microsoft Docs
description: F5 BIG-IP Access Policy Manager e integrazione Azure Active Directory per l'accesso ibrido sicuro
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e011417b936ed83b4658e6dad25bf8e8ee88aed
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96318030"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 BIG-IP Access Policy Manager e integrazione Azure Active Directory per l'accesso ibrido sicuro

La proliferazione della mobilità e del panorama delle minacce in continua evoluzione sta ponendo un ulteriore controllo sull'accesso alle risorse e sulla governance, ponendo [zero Trust](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) front e Center di tutti i programmi di modernizzazione.
Microsoft e F5 sono consapevoli che questa trasformazione digitale è in genere un percorso multianno per qualsiasi azienda, lasciando potenzialmente le risorse critiche esposte fino alla modernizzazione. La Genesi dietro F5 BIG-IP e Azure Active Directory Secure Hybrid Access (SHA) non mira solo a migliorare l'accesso remoto alle applicazioni locali, ma anche a rafforzare il comportamento di sicurezza complessivo di questi servizi vulnerabili.

Per il contesto, la ricerca stima che il 60-80% delle applicazioni locali è di natura legacy o in altre parole che non possono essere integrate direttamente con Azure Active Directory (AD). Lo stesso studio ha indicato anche che una gran parte di questi sistemi viene eseguita nelle versioni di livello inferiore di SAP, Oracle, SAGE e in altri carichi di lavoro noti che forniscono servizi critici.

SHA risolve questo punto cieco consentendo alle organizzazioni di continuare a usare gli investimenti F5 per la distribuzione di reti e applicazioni superiore. Combinato con Azure AD colma il paesaggio dell'applicazione eterogeneo con il piano di controllo delle identità moderno.

La possibilità di Azure AD l'accesso pre-autenticazione ai servizi pubblicati per BIG-IP offre molti vantaggi:

- Autenticazione senza password con le chiavi di [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install), [Fast Identity online (fido)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)e [autenticazione basata su certificati](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)

- [Accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) preemptive e [multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

- [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#:~:text=Identity%20Protection%20is%20a%20tool%20that%20allows%20organizations,detection%20data%20to%20third-party%20utilities%20for%20further%20analysis) -controllo adattivo tramite la profilatura dei rischi per utenti e sessioni

- [Rilevamento di credenziali perse](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

- [Reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-sspr)

- [Collaborazione tra partner](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users) : gestione dei diritti per l'accesso ai Guest regolamentato

- [Cloud app Security (CASB)](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) per l'individuazione e il controllo completi delle app

- Monitoraggio delle minacce: [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) per Advanced Threat Analytics

- [Portale di Azure ad](https://azure.microsoft.com/features/azure-portal/) : un singolo piano di controllo per la gestione dell'identità e dell'accesso

## <a name="scenario-description"></a>Descrizione dello scenario

Come un controller di distribuzione delle applicazioni (ADC) e una VPN SSL, un BIG-IP System fornisce accesso locale e remoto a tutti i tipi di servizi, tra cui:

- Applicazioni Web moderne e Legacy

- Applicazioni non basate sul Web

- Servizi API Web REST e SOAP

Gestione traffico locale (LTM) consente la pubblicazione sicura dei servizi tramite la funzionalità del proxy inverso. Allo stesso tempo, un sofisticato gestore di criteri di accesso (APM) estende il BIG-IP con un set di funzionalità più completo, abilitando la Federazione e Single Sign-on (SSO).

L'integrazione si basa su una relazione di trust federativa standard tra APM e Azure AD, comuni alla maggior parte dei casi d'uso di SHA che include lo [scenario SSL-VPN](f5-aad-password-less-vpn.md). Le risorse Security Assertion Markup Language (SAML), OAuth e Open ID Connect (OIDC) non sono eccezioni, perché possono essere protette per l'accesso remoto. Potrebbero inoltre essere presenti scenari in cui un BIG-IP diventa un punto di strangolamento per zero Trust Access a tutti i servizi, incluse le app SaaS.

La capacità di un BIG-IP di integrarsi con Azure AD è ciò che consente la transizione del protocollo necessaria per proteggere i servizi legacy o non Azure AD integrati con controlli moderni, ad esempio [l'autenticazione senza password](https://www.microsoft.com/security/business/identity/passwordless) e [l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). In questo scenario, un BIG-IP continua a svolgere il proprio ruolo di proxy inverso, disattivando al contempo la pre-autenticazione e l'autorizzazione ai Azure AD, in base al servizio.

I passaggi 1-4 nel diagramma illustrano lo scambio di pre-autenticazione front-end tra un utente, un IP grande e Azure AD, in un flusso avviato dal provider di servizi. I passaggi 5-6 illustrano l'arricchimento della sessione APM successiva e l'accesso SSO ai singoli servizi back-end.

![Immagine che mostra l'architettura di alto livello](./media/f5-aad-integration/integration-flow-diagram.png)

| Passaggio | Descrizione |
|:------|:-----------|
| 1. | L'utente seleziona un'icona dell'applicazione nel portale, risolvendo l'URL con SAML SP (BIG-IP) |
| 2. | BIG-IP reindirizza l'utente a SAML IDP (Azure AD) per la pre-autenticazione|
| 3. | Azure AD elabora i criteri della CA e i [controlli di sessione](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session) per l'autorizzazione|
| 4. | Reindirizza l'utente a BIG-IP che presenta le attestazioni SAML rilasciate da Azure AD |
| 5. | BIG-IP richiede eventuali informazioni aggiuntive sulla sessione da includere in [SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) e nel [controllo degli accessi in base al ruolo (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) per il servizio pubblicato |
| 6. | BIG-IP invia la richiesta client al servizio back-end

## <a name="user-experience"></a>Esperienza utente

Per la maggior parte degli utenti che hanno già familiarità con l'esperienza di accesso di Office 365, l'accesso a servizi di grandi dimensioni tramite SHA rimane molto familiare.

Ora gli utenti trovano i servizi pubblicati BIG-IP consolidati nelle Launchpad  [app](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) o [O365](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) insieme alle funzionalità self-service per un set più ampio di servizi, indipendentemente dal tipo di dispositivo o località. Gli utenti possono anche continuare ad accedere ai servizi pubblicati direttamente tramite il portale di Webtop proprietario BIG-IPs, se preferibile. Quando si esegue la disconnessione, SHA garantisce che la sessione di un utente venga terminata a entrambe le estremità, BIG-IP e Azure AD, garantendo che i servizi rimangano completamente protetti da accessi non autorizzati.  

Le schermate fornite si trovano nel portale dell'app Azure AD che consente agli utenti di accedere in modo sicuro per trovare i servizi pubblicati su BIG-IP e per gestire le proprietà dell'account.  

![La schermata mostra la raccolta di Woodgrove Apps](media/f5-aad-integration/woodgrove-app-gallery.png)

![La schermata mostra la pagina self-service di Woodgrove accounts](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Informazioni dettagliate e analisi

Il ruolo di un IP di grandi dimensioni è fondamentale per qualsiasi azienda, quindi è necessario monitorare le istanze di BIG IP distribuite per garantire la disponibilità elevata dei servizi pubblicati, sia a livello di SHA che operativo.

Sono disponibili diverse opzioni per la registrazione di eventi in locale o in remoto tramite una soluzione di gestione di informazioni ed eventi di sicurezza (SIEM), che consente l'archiviazione e l'elaborazione dei dati di telemetria. Una soluzione estremamente efficace per il monitoraggio di Azure AD e attività specifiche di SHA consiste nell'usare [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) e [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview), insieme:

- Panoramica dettagliata dell'organizzazione, potenzialmente tra più cloud e percorsi locali, inclusa l'infrastruttura di BIG IP

- Piano di controllo singolo che fornisce una visualizzazione combinata di tutti i segnali, evitando la dipendenza da strumenti complessi e diversi

![L'immagine mostra il flusso di monitoraggio](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Prerequisiti

L'integrazione di F5 BIG-IP con Azure AD per SHA prevede i prerequisiti seguenti:

- Un'istanza BIG-IP F5 in esecuzione su una delle piattaforme seguenti:

  - Appliance fisica

  - Edizione virtuale hypervisor, ad esempio Microsoft Hyper-V, VMware ESXi, KVM Linux e hypervisor Citrix

  - Edizione virtuale cloud, ad esempio Azure, VMware, KVM, community Xen, MS Hyper-V, AWS, OpenStack e Google Cloud

    Il percorso effettivo di un'istanza di BIG-IP può essere locale o qualsiasi piattaforma cloud supportata, incluso Azure, purché disponga della connettività a Internet, delle risorse da pubblicare e di qualsiasi altro servizio necessario, ad esempio Active Directory.  

- Una licenza Active F5 BIG-IP APM, tramite una delle opzioni seguenti:

   - Bundle Best di F5 BIG-IP® Best oppure

   - F5 BIG-IP Access Policy Manager™ licenza autonoma

   - F5 la licenza del componente aggiuntivo™ (APM) di gestione dei criteri di accesso BIG IP in un Big-IP F5 BIG-IP® locale di gestione traffico™ (LTM)

   - [Licenza di valutazione](https://www.f5.com/trial/big-ip-trial.php) ™ APM (Big-IP Access Policy Manager) di 90 giorni

- Azure AD le licenze tramite una delle opzioni seguenti:

   - Una [sottoscrizione Azure ad gratuita](https://docs.microsoft.com/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) fornisce i requisiti minimi di base per l'implementazione di Sha con autenticazione senza password

   - Una [sottoscrizione Premium](https://azure.microsoft.com/pricing/details/active-directory/) fornisce tutti i valori aggiuntivi indicati nella [prefazione, tra](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)cui [l'accesso condizionale, l'](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)autenticazione a più fattori e la protezione delle [identità](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

Non è necessaria alcuna esperienza precedente o la conoscenza di F5 BIG-IP è necessaria per implementare SHA, ma è consigliabile acquisire familiarità con la terminologia di F5 BIG-IP. BigIP Rich [Knowledge base](https://www.f5.com/services/resources/glossary) è anche un ottimo punto di partenza per iniziare a creare informazioni di Big-IP.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

Le esercitazioni seguenti forniscono istruzioni dettagliate sull'implementazione di alcuni dei modelli più comuni per BIG-IP e Azure AD SHA:

- [Procedura dettagliata per la distribuzione di F5 BIG-IP in Azure](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM e Azure AD SSO alle applicazioni Kerberos](https://docs.microsoft.com/azure/active-directory/saas-apps/kerbf5-tutorial#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM e Azure AD SSO per le applicazioni basate su intestazione](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial#configure-f5-single-sign-on-for-header-based-application)

- [Protezione di F5 BIG-IP SSL-VPN con Azure AD SHA](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Fine delle password, vai alla password](https://www.microsoft.com/security/business/identity/passwordless)

- [Accesso ibrido Azure Active Directory sicuro](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Microsoft Zero Trust Framework per abilitare il lavoro remoto](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Introduzione ad Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Passaggi successivi

Prendere in considerazione l'esecuzione di un modello di verifica SHA usando l'infrastruttura BIG-IP esistente o la distribuzione di un'istanza di valutazione. [La distribuzione di una VM Big-IP Virtual Edition (VE) in Azure](f5-bigip-deployment-guide.md) richiede circa 30 minuti. a questo punto si avrà:

- Una piattaforma completamente protetta per modellare un modello di verifica SHA

- Un'istanza di pre-produzione, una piattaforma completamente protetta da usare per testare nuovi hotfix e aggiornamenti del sistema BIG-IP

Allo stesso tempo, è necessario identificare una o due applicazioni che possono essere destinate alla pubblicazione tramite il BIG-IP e la protezione con SHA.  

Si consiglia di iniziare con un'applicazione che non è ancora stata pubblicata tramite un BIG-IP, in modo da evitare potenziali rotture ai servizi di produzione. Le linee guida indicate in questo articolo consentiranno di acquisire familiarità con la procedura generale per la creazione dei vari oggetti di configurazione di BIG-IP e la configurazione di SHA. Al termine, sarà possibile eseguire la stessa operazione con altri nuovi servizi, oltre a disporre di una conoscenza sufficiente per convertire i servizi pubblicati BIG-IP esistenti in SHA con il minimo sforzo.

La guida interattiva seguente illustra in dettaglio la procedura di alto livello per l'implementazione di SHA e la visualizzazione dell'esperienza dell'utente finale.

[![L'immagine mostra la copertura interattiva della Guida](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
