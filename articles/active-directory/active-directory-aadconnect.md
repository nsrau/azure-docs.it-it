<properties 
	pageTitle="Abilitazione della directory per la gestione ibrida con Azure AD Connect" 
	description="Articolo relativo ad Azure AD Connect che ne descrive le funzionalità e spiega perché è consigliabile usarlo." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="billmath"/>

# Abilitazione della directory per la gestione ibrida con Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="Che cos&apos;è" class="current">Che cos'è</a> <a href="../active-directory-aadconnect-how-it-works/" title="Come funziona">Come funziona</a> <a href="../active-directory-aadconnect-get-started/" title="Introduzione">Introduzione</a> <a href="../active-directory-aadconnect-whats-next/" title="Passaggi successivi">Passaggi successivi</a> <a href="../active-directory-aadconnect-learn-more/" title="Altre informazioni">Altre informazioni</a>
</div>


Oggi gli utenti vogliono poter accedere alle applicazioni sia in locale che nel cloud. Desiderano poterlo fare da qualsiasi dispositivo, ad esempio un portatile uno smartphone o un tablet. A questo scopo, è necessario che l'amministratore e l'organizzazione mettano a disposizione degli utenti un modo per accedere a queste app, ma il passaggio totale al cloud non è sempre un'opzione praticabile.

<center>![Informazioni su Azure AD Connect](./media/active-directory-aadconnect/arch.png)</center>

Con l'introduzione di Azure Active Directory Connect, fornire l'accesso a queste app e passare al cloud non sono mai state operazioni così facili. Azure AD Connect offre i vantaggi seguenti:

- Gli utenti possono accedere con un'identità comune sia nel cloud che in locale. Non devono ricordare più password o account e gli amministratori non si devono preoccupare del sovraccarico aggiuntivo che più account possono introdurre.
- Un singolo strumento che offre un'esperienza guidata per la connessione delle directory locali ad Azure Active Directory. Dopo l'installazione, la procedura guidata distribuisce e configura tutti i componenti necessari per l'integrazione delle directory, tra cui i servizi di sincronizzazione, la sincronizzazione delle password o di ADFS e i prerequisiti tra cui il modulo Azure AD PowerShell.



<center>![Informazioni su Azure AD Connect](./media/active-directory-aadconnect/azuread.png)</center>




## Perché usare Azure AD Connect 

L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi, in quanto fornisce un'identità comune per accedere alle risorse cloud e locali. Con questa integrazione utenti e organizzazioni possono sfruttare i vantaggi seguenti:
	
* Le organizzazioni possono fornire agli utenti un'identità ibrida comune per i servizi locali e basati sul cloud sfruttando Windows Server Active Directory per connettersi quindi ad Azure Active Directory. 
* Gli amministratori possono fornire l'accesso condizionale in base alla risorsa dell'applicazione, al dispositivo e all'identità utente, al percorso di rete e all'autenticazione a più fattori.
* Gli utenti possono fruttare la propria identità comune degli account in Azure AD per Office 365, Intune, app SaaS e applicazioni di terze parti.  
* Gli sviluppatori possono compilare applicazioni che sfruttano il modello di identità comune, integrando le applicazioni in Active Directory locale o in Azure per le applicazioni basate sul cloud.

Azure AD Connect facilita l'integrazione e semplifica la gestione dell'infrastruttura di identità in locale e nel cloud.



----------------------------------------------------------------------------------------------------------
## Scaricare Azure AD Connect

Per iniziare a usare Azure AD Connect, è possibile scaricare la versione più recente dalla pagina di [download di Azure AD Connect ](http://go.microsoft.com/fwlink/?LinkId=615771).

----------------------------------------------------------------------------------------------------------








 

<!---HONumber=62-->