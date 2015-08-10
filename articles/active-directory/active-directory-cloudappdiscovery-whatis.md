<properties 
	pageTitle="Come individuare app cloud non autorizzate usate nell'organizzazione" 
	description="Questo argomento fornisce informazioni sul servizio Cloud App Discovery e i vantaggi che offre." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Come individuare app cloud non autorizzate usate nell'organizzazione

Nelle aziende moderne, spesso i reparti IT non sono a conoscenza di tutte le applicazioni cloud usate dagli utenti per svolgere il proprio lavoro. L'accesso non autorizzato ai dati aziendali, le possibili perdite di dati e altri rischi di sicurezza impliciti nelle applicazioni rappresentano una preoccupazione per gli amministratori. Poiché non sono a conoscenza né del numero né del tipo di app usate, risulta difficile predisporre un piano per affrontare questi rischi in modo efficace.

Cloud App Discovery consente di risolvere questi problemi. Cloud App Discovery è una funzionalità Premium di Azure Active Directory che consente di individuare le applicazioni cloud usate dai dipendenti dell'azienda.


**Cloud App Discovery permette di:**

* Individuare le applicazioni in uso e misurarne l'utilizzo in base al numero di utenti, al volume di traffico o al numero di richieste Web all'applicazione. 
* Identificare gli utenti che usano un'applicazione. 
* Esportare i dati per ulteriori analisi offline. 
* Assegnare priorità alle applicazioni in modo che il reparto IT possa tenerle sotto controllo e integrarle facilmente per abilitare l'accesso Single Sign-On e la gestione degli utenti. 

Con Cloud App Discovery, l'attività di recupero dei dati viene svolta da agenti in esecuzione nei computer degli ambienti aziendali. Le informazioni sull'utilizzo delle app acquisite dagli agenti vengono inviate tramite un canale crittografato sicuro al servizio Cloud App Discovery. Il servizio Cloud App Discovery valuta i dati e genera report che è possibile usare per analizzare l'ambiente.


<center>![Funzionamento di Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)</center>

##Passaggi successivi


* Per altre informazioni sul funzionamento di Cloud App Discovery, vedere [Introduzione a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) 
* Per considerazioni sulla sicurezza e sulla privacy, vedere [Considerazioni sulla sicurezza e sulla privacy in Cloud App Discovery Security](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) 
* Per informazioni sulla distribuzione dell'agente di Cloud App Discovery in un ambiente aziendale con: 
 * Gestione Criteri di gruppo di Active Directory, vedere [Guida alla distribuzione di Criteri di gruppo di Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) 
 * Microsoft System Center Configuration Manager, vedere [Guida alla distribuzione di Cloud App Discovery per System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) 
 * Server proxy con porte personalizzate, vedere [Impostazioni del registro di sistema di Cloud App Discovery per server proxy con porte personalizzate](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) 





**Risorse aggiuntive**


* [Cloud App Discovery - Log modifiche dell'agente](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery - Domande frequenti](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=July15_HO5-->