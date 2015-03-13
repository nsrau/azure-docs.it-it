<properties 
	pageTitle="Informazioni su Azure Active Directory" 
	description="Usare Azure Active Directory per estendere le identità locali esistenti nel cloud per una migliore esperienza di amministrazione e degli utenti finali, mentre Microsoft mantiene Active Directory in esecuzione nel cloud con scalabilità e disponibilità elevate e il ripristino di emergenza integrato. In alternativa, sviluppare applicazioni Azure AD integrate per l'organizzazione o per altre organizzazioni." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="justinha"/>





<h1 id="whatisaad">Informazioni su Azure Active Directory</h1>

Azure Active Directory è un servizio che offre funzionalità di gestione delle identità e degli accessi nel cloud.. Analogamente ad Active Directory, un servizio reso disponibile ai clienti tramite il sistema operativo Windows Server per la gestione delle identità in locale, Azure Active Directory (Azure AD) è un servizio reso disponibile tramite Azure per la gestione delle identità basata sul cloud.  [Altre informazioni](http://msdn.microsoft.com/library/hh967611.aspx)

Poiché si tratta della directory cloud dell'organizzazione, sono i clienti stessi a decidere chi sono gli utenti, quali informazioni conservare nel cloud, chi può usare le informazioni o gestirle e a quali applicazioni o servizi è consentito l'accesso a tali informazioni. 

Quando si usa Azure AD, è responsabilità di Microsoft mantenere Active Directory in esecuzione nel cloud con caratteristiche di scalabilità elevata, disponibilità elevata e disaster recovery integrato, nel pieno rispetto dei requisiti di privacy e sicurezza delle informazioni aziendali.

<h3>Integrazione con l'implementazione locale di Active Directory</h3>

Azure AD può essere usato come directory cloud autonoma dell'organizzazione oppure integrato con l'implementazione di Active Directory locale esistente.. L'integrazione offre funzionalità, tra cui la sincronizzazione delle directory e il Single Sign-On, che estendono ulteriormente la portata delle identità locali esistenti nel cloud migliorando l'esperienza sia degli amministratori che degli utenti finali. 
 [Altre informazioni](http://msdn.microsoft.com/library/jj573653)

<h3>Integrazione con le applicazioni</h3>

Gli sviluppatori di applicazioni possono integrare le applicazioni con Azure AD per fornire funzionalità di Single Sign-On per gli utenti.. Ciò consente di ospitare le applicazioni aziendali nel cloud e di autenticare facilmente gli utenti con le credenziali aziendali. Consente inoltre ai provider di software come un servizio (SaaS) di semplificare l'autenticazione nei propri servizi degli utenti facenti parte di organizzazioni Azure AD. Gli sviluppatori possono inoltre usare l'API Graph per eseguire query sui dati della directory per la gestione di entità quali utenti o gruppi. [Altre informazioni](http://go.microsoft.com/fwlink/?LinkID=290817&clcid=0x409)

**Risorse aggiuntive**

* [Iscrizione ad Azure come organizzazione](/it-it/manage/services/identity/organizational-account/)
* [Identità di Azure](/it-it/manage/windows/fundamentals/identity/)
* [Documentazione di Azure AD su MSDN](http://go.microsoft.com/fwlink/?LinkId=293425)

<!--HONumber=46--> 
