<properties 
	pageTitle="Terminologia di Azure AD" 
	description="Termini e definizioni correlati ad Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Terminologia di Azure AD

In Microsoft Azure Active Directory (Azure AD) vi è un set univoco di terminologia relativo agli scenari cloud, ibridi e locali. Nella tabella seguente vengono definiti questi termini per offrire una conoscenza di base per la modalità d'uso.

 Termine | Definizione
------------- | -------------
Verifica aggiuntiva di sicurezza | Un'impostazione di sicurezza che un amministratore globale può impostare su un account utente nella directory di un'organizzazione per richiedere che vengano usate la password di un utente e una risposta dal proprio telefono per verificare l'identità sul sistema di autenticazione di Azure Active Directory.
Azure Active Directory | Il servizio di gestione delle identità in Azure che fornisce funzionalità di gestione delle identità e di controllo di accesso tramite un'API basata su REST.
Controllo di accesso di Azure Active Directory | Il servizio Azure che fornisce l'autenticazione federata e l'autorizzazione basata su regole e sulle attestazioni per i servizi Web REST.
Sistema di autenticazione di Azure Active Directory | Il servizio di gestione delle identità di Microsoft nel cloud usato per autenticare e autorizzare gli account aziendali o degli istituti di istruzione.
Azure Active Directory Graph | Funzionalità di Azure Active Directory che consente di accedere a oggetti utente, gruppo e ruolo all'interno di un grafico aziendale del social network per visualizzare facilmente informazioni sugli utenti e i loro rapporti personali.
Modulo di Azure Active Directory per Windows PowerShell | Un gruppo di cmdlet usati per amministrare Azure Active Directory. È possibile usare questi cmdlet per gestire utenti, gruppi, domini, sottoscrizioni a servizi cloud, licenze, sincronizzazione delle directory, Single Sign-On e molto altro ancora.
Azure Active Directory Connect | La procedura guidata Azure Active Directory Connect rappresenta il solo strumento interattivo per la connessione delle directory locali con Azure Active Directory. Questa procedura guidata consente di distribuire e configurare tutti i componenti necessari per l'esecuzione dell'integrazione delle directory, tra cui i servizi di sincronizzazione, la sincronizzazione delle password, Active Directory Federation Services (ADFS) e i prerequisiti richiesti, ad esempio il modulo PowerShell di Azure AD.
Servizio di sincronizzazione di Azure Active Directory | L'applicazione che fornisce la sincronizzazione unidirezionale di oggetti directory da un servizio Active Directory locale di una società ad Azure Active Directory.
Integrazione di directory | Una funzionalità di Azure Active Directory che è possibile impostare per migliorare l'esperienza amministrativa associata alla gestione di identità nella directory locale e quella cloud. Gli scenari di integrazione directory comprendono la sincronizzazione delle directory e la sincronizzazione delle directory con Single Sign-On.
Sincronizzazione delle directory | Usato per sincronizzare gli oggetti directory locali (utenti, gruppi, contatti) con il cloud per ridurre il sovraccarico amministrativo. La sincronizzazione delle directory è detta anche sincronizzazione delle directory nel portale di Azure AD e nel portale di gestione di Azure. Dopo aver impostato la sincronizzazione delle directory, gli amministratori possono effettuare il provisioning di oggetti directory di un Active Directory locale in un'istanza di Azure AD.
Assistente per l'accesso a Microsoft Online Services | Assistente per l'accesso è un'applicazione installata su un computer client che rende possibile a un utente di accedere una sola volta a quel computer e quindi di accedere ai servizi innumerevoli volte durante la sessione di accesso. Senza Assistente per l'accesso, gli utenti finali devono specificare un nome e una password ogni volta che tentano di accedere a un servizio. Assistente per l'accesso non deve essere confuso con Single Sign-On che è una funzionalità di integrazione di directory di Azure Active Directory che può essere distribuita per usare le credenziali aziendali locali esistenti di un utente per l'accesso diretto ai servizi cloud Microsoft.
Multi-Factor Authentication (nota anche come autenticazione a due fattori o 2FA) | Multi-Factor Authentication aggiunge un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti. Quando si abilita multi-Factor Authentication per un account utente in Azure AD, l'utente dovrà usare il telefono oltre alle proprie credenziali di password standard come metodo di verifica di sicurezza aggiuntiva ogni volta che deve accedere e usare uno qualsiasi dei servizi cloud Microsoft sottoscritti dall'organizzazione.
Accesso Single Sign-On | Usato per fornire agli utenti un'esperienza di autenticazione più integrata in quanto accedono ai servizi cloud Microsoft mentre sono connessi alla rete aziendale. Per configurare l'accesso Single Sign-On, le organizzazioni devono distribuire un servizio token di sicurezza locale. Dopo aver impostato l'accesso Single Sign-On, gli utenti possono usare le credenziali aziendali di Active Directory (nome utente e password) per accedere ai servizi nel cloud e alle risorse locali esistenti.
ID utente | Un ID utente è un identificatore univoco fornito dall'utente nella pagina di accesso per accedere ai servizi cloud Microsoft che ha sottoscritto l'organizzazione.
Account aziendale o dell'istituto di istruzione | Un account utente assegnato da un'organizzazione (ufficio, istituto scolastico, ente senza scopo di lucro) a uno dei componenti (un dipendente, studente, cliente) che consente di accedere a una o più sottoscrizioni del servizio cloud Microsoft dell'organizzazione, ad esempio Office 365 o Azure. Questi account vengono archiviati nella directory di Azure AD di un'organizzazione e in genere vengono eliminati quando l'utente lascia l'organizzazione. Gli account aziendali o dell'istituto di istruzione differiscono dagli account Microsoft, in quanto vengono creati e gestiti dagli amministratori dell'organizzazione e non dall'utente. 

## Passaggi successivi
- [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
- [Come vengono associate le sottoscrizioni di Azure ad Azure AD](active-directory-how-subscriptions-associated-directory.md)
- [Restrizioni e limiti del servizio Azure AD](active-directory-service-limits-restrictions.md)

<!---HONumber=58--> 