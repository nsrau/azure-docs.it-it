<properties 
	pageTitle="Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS" 
	description="Questa è la pagina su Multi-Factor Authentication di Azure in cui viene descritto come iniziare a utilizzare questa tipologia di autenticazione di Azure nel cloud." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS

Se l'organizzazione è federata con Azure Active Directory e si dispone di risorse che sono accessibili da Azure AD, è possibile utilizzare Azure Multi-Factor Authentication o Active Directory Federation Services per proteggere tali risorse. Utilizzare le procedure seguenti per proteggere le risorse di Azure Active Directory con Azure Multi-Factor Authentication o Active Directory Federation Services.

## Per la protezione delle risorse Azure AD con ADFS procedere come segue: 



1. Utilizzare la procedura descritta [attivare l’autenticazione a più fattori](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users) per far sì che gli utenti abilitino un account.
2. Utilizzare la procedura seguente per impostare una regola attestazioni:

<center>! [Cloud] (. / media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)</center>

- 	Avviare la console di gestione di ADFS.
- 	Passare alla Relying Party Trusts e fare clic destro sul Trust della Relying Party. Selezionare Modifica regole attestazione...
- 	Fare nuovamente clic su Add Rule.
- 	Selezionare Send Claims Using a Custom Rule e fare clic su Next.
- 	Immettere un nome per la regola attestazione.
- 	In Regola personalizzata: aggiungere quanto segue:


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Attestazione corrispondente:

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Fare clic su OK. Fare clic su Finish. Chiudere la console di gestione di ADFS.

Gli utenti possono quindi completare l'accesso utilizzando il metodo locale (ad esempio smart card).


 

<!---HONumber=July15_HO2-->