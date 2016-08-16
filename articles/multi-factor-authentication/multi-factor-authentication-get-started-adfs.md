<properties 
	pageTitle="Introduzione a Azure Multi-Factor Authentication e Active Directory Federation Services" 
	description="Questa è la pagina di Azure Multi-Factor Authentication che descrive come iniziare a usare Azure MFA e ADFS." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>  

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" ms.topic="get-started-article" 
	ms.date="08/04/2016" 
	ms.author="billmath"/>  

# Introduzione a Azure Multi-Factor Authentication e Active Directory Federation Services



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se l'azienda ha federato la Active Directory locale con Azure Active Directory tramite ADFS, le due opzioni seguenti per l'utilizzo di Azure Multi-Factor Authentication sono disponibili.

- Proteggere le risorse cloud mediante Azure Multi-Factor Authentication o Active Directory Federation Services 
- Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication 

Nella tabella seguente è riepilogata l'esperienza di autenticazione tra le risorse di protezione con Azure Multi-Factor Authentication e ADFS

|Esperienza di autenticazione - App basate su Browser|Esperienza di autenticazione - App Non basate su Browser
:------------- | :------------- | :------------- |
Proteggere le risorse Azure AD utilizzando Azure Multi-Factor Authentication |<li>Il primo fattore di autenticazione viene eseguito in locale utilizzando ADFS.</li> <li>Il secondo fattore è un metodo basato su telefono eseguito mediante l'autenticazione cloud.</li>|Gli utenti finali possono utilizzare le password dell’app per effettuare l'accesso.
Proteggere le risorse di Azure AD con Active Directory Federation Services |<li>Il primo fattore di autenticazione viene eseguito in locale utilizzando ADFS.</li><li>Il secondo fattore viene eseguito in locale rispettando l'attestazione.</li>|Gli utenti finali possono utilizzare le password dell’app per effettuare l'accesso.

Avvertenze per le password dell’app rivolte agli utenti federati:

- La Password dell’App viene verificata mediante l'autenticazione cloud e di conseguenza ignora le federazioni. La federazione viene usata attivamente solo durante l'impostazione della Password dell'app.
- Le impostazioni locali di Controllo dell'accesso Client non vengono rispettate dalla password dell'app.
- Si perde la Funzionalità di registrazione dell'autenticazione locale per la Password dell’App.
- La disabilitazione/eliminazione dell’account può richiedere fino a 3 ore per dirsync e per ritardare la disabilitazione o l'eliminazione della password dell’app nell'identità cloud.

Per informazioni sull'impostazione di Azure Multi-Factor Authentication o del Server Azure Multi-Factor Authentication con ADFS vedere quanto segue:

- [Proteggere le risorse cloud mediante Azure Multi-Factor Authentication e ADFS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con ADFS Server Windows 2012 R2](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Proteggere le risorse del cloud e locali mediante Server Azure Multi-Factor Authentication con AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)







 

<!---HONumber=AcomDC_0810_2016-->