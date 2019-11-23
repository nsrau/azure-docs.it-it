---
title: On-premises password protection FAQ - Azure Active Directory
description: Domande frequenti sulla protezione password di Azure AD locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ee5d6328c6a3e4ea0b4a6359d4a21494e3ae62c
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381693"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>protezione password di Azure AD locale - Domande frequenti

This section provides answers to many commonly asked questions about Azure AD Password Protection.

## <a name="general-questions"></a>Domande generali

**Q: What guidance should users be given on how to select a secure password?**

Le indicazioni correnti di Microsoft su questo argomento sono disponibili nella pagina Web a cui si accede tramite il collegamento seguente:

[Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance) (Indicazioni di Microsoft sulle password)

**Q: Is on-premises Azure AD Password Protection supported in non-public clouds?**

No, la protezione password di Azure AD locale è supportata solo nel cloud pubblico. Non è stata annunciata alcuna data per la disponibilità nei cloud non pubblici.

The Azure AD portal does allow modification of the on-premises-specific "Password protection for Windows Server Active Directory" configuration even in non-public clouds; such changes will be persisted but otherwise will never take effect. Registration of on-premises proxy agents or forests is unsupported when non-public cloud credentials are used, and any such registration attempts will always fail.

**Q: How can I apply Azure AD Password Protection benefits to a subset of my on-premises users?**

Non supportati. Dopo la distribuzione e l'abilitazione, Password di protezione di Azure AD non prevede alcuna discriminazione: tutti gli utenti ottengono uguali vantaggi per la sicurezza.

**Q: What is the difference between a password change and a password set (or reset)?**

A password change is when a user chooses a new password after proving they have knowledge of the old password. For example, a password change is what happens when a user logs into Windows and is then prompted to choose a new password.

A password set (sometimes called a password reset) is when an administrator replaces the password on an account with a new password, for example by using the Active Directory Users and Computers management tool. This operation requires a high level of privilege (usually Domain Admin), and the person performing the operation usually does not have knowledge of the old password. Help-desk scenarios often perform password sets, for instance when assisting a user who has forgotten their password. You will also see password set events when a brand new user account is being created for the first time with a password.

The password validation policy behaves the same regardless of whether a password change or set is being done. The Azure AD Password Protection DC Agent service does log different events to inform you whether a password change or set operation was done.  See [Azure AD Password Protection monitoring and logging](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Q: Why are duplicated password rejection events logged when attempting to set a weak password using the Active Directory Users and Computers management snap-in?**

The Active Directory Users and Computers management snap-in will first try to set the new password using the Kerberos protocol. Upon failure, the snap-in will make a second attempt to set the password using a legacy (SAM RPC) protocol (the specific protocols used are not important). If the new password is considered weak by Azure AD Password Protection, this snap-in behavior will result in two sets of password reset rejection events being logged.

**Q: Why are Azure AD Password Protection password validation events being logged with an empty user name?**

Active Directory supports the ability to test a password to see if it passes the domain's current password complexity requirements, for example using the [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api. When a password is validated in this way, the testing also includes validation by password-filter-dll based products such as Azure AD Password Protection - but the user names passed to a given password filter dll will be empty. In this scenario, Azure AD Password Protection will still validate the password using the currently in-effect password policy and will issue an event log message to capture the outcome, however the event log message will have empty user name fields.

**Q: Is it supported to install Azure AD Password Protection side by side with other password-filter-based products?**

Sì. Il supporto di più DLL di filtro password registrate è una funzionalità di base di Windows e non è specifico della protezione password di Azure AD. Tutte le DLL di filtro password registrate devono concordare, prima che venga accettata una password.

**Q: How can I deploy and configure Azure AD Password Protection in my Active Directory environment without using Azure?**

Non supportati. Password di protezione di Azure AD è una funzionalità di Azure che supporta l'estensione in un ambiente Active Directory locale.

**Q: How can I modify the contents of the policy at the Active Directory level?**

Non supportati. The policy can only be administered using the Azure AD portal. Vedere anche la domanda precedente.

**D: perché è necessario il servizio DFSR per la replica di sysvol?**

Il servizio Replica file (la tecnologia precedente a DFSR) include molti problemi noti e non è completamente supportato nelle versioni più recenti di Windows Server Active Directory. La protezione password di Azure AD non verrà testata nei domini configurati con il servizio Replica file.

Per altre informazioni, vedere gli articoli seguenti:

[The Case for Migrating sysvol replication to DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr) (Caso della migrazione della replica di sysvol a DFSR)

[The End is Nigh for FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) (La fine è vicina per il servizio Replica file)

If your domain is not already using DFSR, you MUST migrate it to use DFSR before installing Azure AD Password Protection. For more information, see the following link:

[SYSVOL Replication Migration Guide: FRS to DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> The Azure AD Password Protection DC Agent software will currently install on domain controllers in domains that are still using FRS for sysvol replication, but the software will NOT work properly in this environment. Additional negative side-effects include individual files failing to replicate, and sysvol restore procedures appearing to succeed but silently failing to replicate all files. You should migrate your domain to use DFSR as soon as possible, both for DFSR's inherent benefits and also to unblock the deployment of Azure AD Password Protection. Future versions of the software will be automatically disabled when running in a domain that is still using FRS.

**Q: How much disk space does the feature require on the domain sysvol share?**

La quantità esatta di spazio usato dipende da fattori come il numero e la lunghezza dei token esclusi contenuti nell'elenco globale degli elementi esclusi Microsoft e nell'elenco personalizzato del singolo tenant, nonché dal sovraccarico della crittografia. Il contenuto di questi elenchi probabilmente aumenterà in futuro. Tenendo presente tale situazione, è ragionevole prevedere che per la funzionalità siano necessari almeno cinque (5) megabyte di spazio nella condivisione sysvol del dominio.

**D: perché è necessario un riavvio per installare o aggiornare il software dell'agente del controller di dominio?**

Questo requisito dipende dal comportamento di base di Windows.

**D: è disponibile un modo per configurare un agente del controller di dominio per l'uso di un server proxy specifico?**

No. Poiché il server proxy è senza stato, non ha importanza quale server proxy specifico venga usato.

**Q: Is it okay to deploy the Azure AD Password Protection Proxy service side by side with other services such as Azure AD Connect?**

Sì. Il servizio proxy di Password di protezione di Azure AD e Azure AD Connect non devono essere mai direttamente in conflitto l'uno con l'altro.

Unfortunately, an incompatibility has been found between the version of the Microsoft Azure AD Connect Agent Updater service that is installed by the Azure AD Password Protection Proxy software and the version of the service that is installed by the [Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) software. This incompatibility may result in the Agent Updater service being unable to contact Azure for software updates. It is not recommended to install Azure AD Password Protection Proxy and Azure Active Directory Application Proxy on the same machine.

**Q: In what order should the DC agents and proxies be installed and registered?**

Any ordering of Proxy agent installation, DC agent installation, forest registration, and Proxy registration  is supported.

**Q: Should I be concerned about the performance hit on my domain controllers from deploying this feature?**

Il servizio agente del controller di dominio di Password di protezione di Azure AD non dovrebbe incidere in modo significativo sulle prestazioni dei controller di dominio in una distribuzione di Active Directory esistente e integra.

Per la maggior parte delle distribuzioni di Active Directory attive, le operazioni di modifica delle password rappresentano una piccola parte del carico di lavoro complessivo in un determinato controller di dominio. Ad esempio, si supponga l'esistenza di un dominio di Active Directory con 10000 account utente e criteri MaxPasswordAge impostati su 30 giorni. In media questo dominio vedrà 10000/30=~333 operazioni di modifica delle password ogni giorno, il che rappresenta un numero trascurabile di operazioni persino per un singolo controller di dominio. Considerare un potenziale scenario di caso peggiore, ovvero supporre che queste ~333 modifiche delle password in un singolo controller di dominio siano state effettuate nel giro di un'ora. Questo scenario, ad esempio, può verificarsi quando diversi dipendenti tornano tutti al lavoro il lunedì mattina. Anche in questo caso, si verificheranno ~333/60 minuti = sei modifiche di password al minuto, il che non costituisce comunque un carico significativo.

Se però i controller di dominio correnti già operano a livelli di prestazioni limitate (ad esempio, perché sono stati superati i limiti massimi di CPU, spazio su disco, I/O su disco e così via), prima di distribuire questa funzionalità è consigliabile aggiungere altri controller di dominio o espandere lo spazio su disco disponibile. Vedere anche la domanda precedente relativa all'uso dello spazio su disco per la condivisione sysvol.

**Q: I want to test Azure AD Password Protection on just a few DCs in my domain. Is it possible to force user password changes to use those specific DCs?**

No. È il sistema operativo client Windows a controllare quale controller di dominio viene usato quando un utente modifica la password. The domain controller is selected based on factors such as Active Directory site and subnet assignments, environment-specific network configuration, etc. Azure AD Password Protection does not control these factors and cannot influence which domain controller is selected to change a user's password.

Un modo per raggiungere parzialmente questo obiettivo consiste nel distribuire Password di protezione di Azure AD in tutti i controller di dominio presenti in un determinato sito di Active Directory. Questo approccio garantirà una copertura ragionevole per i client Windows assegnati al sito e, di conseguenza, anche per gli utenti che accedono a tali client e modificano le loro password.

**Q: If I install the Azure AD Password Protection DC Agent service on just the Primary Domain Controller (PDC), will all other domain controllers in the domain also be protected?**

No. Quando la password di un utente viene modificata in un determinato controller di dominio non primario, la password non crittografata non viene mai inviata al controller di dominio primario (questa è un'idea errata molto diffusa). Dopo che una nuova password viene accettata in un controller di dominio, quest'ultimo usa tale password per crearne i vari hash specifici del protocollo di autenticazione e quindi rende permanenti gli hash nella directory. La password non crittografata non diventa permanente. Gli hash aggiornati vengono poi replicati nel controller di dominio primario. Le password utente possono in alcune circostanze essere modificate direttamente nel controller di dominio primario, anche in questo caso in base a vari fattori come la topologia di rete e la struttura del sito di Active Directory. Vedere la domanda precedente.

In sintesi, la distribuzione del servizio agente del controller di dominio di Password di protezione di Azure AD nel controller di dominio primario è necessaria per raggiungere una copertura di sicurezza della funzionalità pari al 100% all'interno del dominio. La distribuzione della funzionalità nel solo controller di dominio primario non offre i vantaggi di sicurezza di Password di protezione di Azure AD per gli altri controller di dominio presenti nel dominio.

**Q: Why is custom smart lockout not working even after the agents are installed in my on-premises Active Directory environment?**

Custom smart lockout is only supported in Azure AD. Changes to the custom smart lockout settings in the Azure AD portal have no effect on the on-premises Active Directory environment, even with the agents installed.

**Q: Is a System Center Operations Manager management pack available for Azure AD Password Protection?**

No.

**Q: Why is Azure AD still rejecting weak passwords even though I've configured the policy to be in Audit mode?**

Audit mode is only supported in the on-premises Active Directory environment. Azure AD is implicitly always in "enforce" mode when it evaluates passwords.

**Q: My users see the traditional Windows error message when a password is rejected by Azure AD Password Protection. Is it possible to customize this error message so that users know what really happened?**

No. The error message seen by users when a password is rejected by a domain controller is controlled by the client machine, not by the domain controller. This behavior happens whether a password is rejected by the default Active Directory password policies or by a password-filter-based solution such as Azure AD Password Protection.

## <a name="additional-content"></a>Contenuto aggiuntivo

Gli articoli a cui si accede tramite i collegamenti seguenti non fanno parte della documentazione di base relativa a Password di protezione di Azure AD, ma possono essere una fonte utile di informazioni aggiuntive su tale funzionalità.

[Azure AD Password Protection is now generally available!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Email Phishing Protection Guide – Part 15: Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529) (Password di protezione di Azure AD e il blocco intelligente sono ora disponibili in anteprima pubblica)

## <a name="microsoft-premierunified-support-training-available"></a>Disponibilità di training in caso contratti di supporto Microsoft Premier o Unified

Se è interessati ad approfondire la conoscenza di Password di protezione di Azure AD e la distribuzione di tale funzionalità nell'ambiente in uso, è possibile sfruttare i vantaggi di un servizio proattivo Microsoft disponibile per i clienti con un contratto di supporto Premier o Unified. The service is called Azure Active Directory: Password Protection. Per altre informazioni, contattare il technical account manager.

## <a name="next-steps"></a>Passaggi successivi

In caso di dubbi su Password di protezione di Azure AD locale a cui questo articolo non abbia dato risposta, inviare il proprio feedback tramite la sezione riservata ai commenti di seguito.

[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
