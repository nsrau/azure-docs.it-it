<properties 
	pageTitle="Azure Multi-Factor Authentication - Introduzione" 
	description="Scegliere la soluzione di sicurezza Multi-Factor Authentication adatta alle proprie esigenze ponendosi i relativi quesiti. Scegliere quindi cloud, Server di autenticazione a più fattori o AD FS." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/04/2016" 
	ms.author="billmath"/>

#Scegliere la soluzione di sicurezza Multi-Factor più adatta

Poiché esistono diverse versioni di Azure Multi-Factor Authentication, è necessario stabilire un paio di aspetti per scoprire quale versione sia quella corretta da utilizzare. Aspetti da considerare:

-	[Cosa si sta tentando di proteggere?](#cosa-si-sta-tentando-di-proteggere)
-	[Dove si trovano gli utenti?](#dove-si-trovano-gli-utenti)

Nelle sezioni seguenti verranno fornite istruzioni su come determinare ciascun aspetto.

## Cosa si sta tentando di proteggere?

Per determinare la soluzione Multi-Factor Authentication corretta, innanzitutto è necessario stabilire ciò che si sta tentando di proteggere con un secondo metodo di autenticazione. Si tratta di un'applicazione in Azure? Oppure si tratta, ad esempio, di un sistema di accesso remoto? Durante la definizione degli elementi da proteggere, stabiliremo dove abilitare la modalità Multi-Factor Authentication.


Cosa si intende proteggere| Multi-Factor Authentication nel cloud|Server Multi-Factor Authentication 
------------- | :-------------: | :-------------: |
App prodotte direttamente da Microsoft|* |* |
App SaaS nella Raccolta app|* |* |
Le applicazioni IIS pubblicate tramite proxy app per Azure AD|* |* |
Le applicazioni IIS non pubblicate tramite proxy app per Azure AD | |* |
Accesso remoto, ad esempio VPN, RDG| |* |



## Dove si trovano gli utenti?

Successivamente, a seconda di dove si trovano gli utenti, è possibile determinare la soluzione corretta da usare, ad esempio Multi-Factor Authentication nel cloud o in locale con il server MFA.



Ubicazione degli utenti| Soluzione
------------- | :------------- | 
Azure Active Directory| Multi-Factor Authentication nel cloud|
Azure AD e AD locale usando la federazione con AD FS| Sia MFA nel cloud che il server MFA sono opzioni possibili 
Azure AD e AD locale con DirSync, Azure AD Sync, Azure AD Connect, senza sincronizzazione di password|Sia MFA nel cloud che il server MFA sono opzioni possibili 
Azure AD e AD locale con DirSync, Azure AD Sync, Azure AD Connect, con sincronizzazione di password|Multi-Factor Authentication nel cloud
Active Directory locale|Server Multi-Factor Authentication

Nella tabella seguente è riportato un confronto tra le funzionalità disponibili in una soluzione Multi-Factor Authentication nel cloud e una soluzione server Multi-Factor Authentication.

 | Multi-Factor Authentication nel cloud | Server Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Notifica dell'app per dispositivi mobili come secondo fattore | ● | ● |
Codice di verifica dell'app per dispositivi mobili come secondo fattore | ● | ●
Chiamata telefonica come secondo fattore | ● | ● 
SMS unidirezionale come secondo fattore | ● | ●
SMS bidirezionale come secondo fattore | | ● 
Token hardware come secondo fattore | | ● 
Password di app per i client che non supportano MFA | ● |  
Controllo amministrazione sui metodi di autenticazione | ● | ● 
Modalità PIN | | ●
Avviso di illecito | ● | ●
Report MFA | ● | ● 
Bypass monouso | | ● 
Messaggi di saluto personalizzati per le telefonate | ● | ● 
ID chiamante personalizzabile per le telefonate | ● | ● 
IP attendibili | ● | ● 
Memorizzazione di MFA per dispositivi attendibili | ● |  
Accesso condizionale | ● | ● 
Cache | | ● 

Dopo aver stabilito se è necessario utilizzare la modalità Multi-Factor Authentication nel cloud o il server MFA locale, sarà possibile iniziare a impostare e utilizzare Azure Multi-Factor Authentication. **Selezionare l'icona che rappresenta lo scenario.**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_0810_2016-->