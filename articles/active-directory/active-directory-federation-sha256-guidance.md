<properties
	pageTitle="Modificare l'algoritmo hash della firma relativo al trust della relying party di Office 365 | Microsoft Azure"
	description="Questa pagina fornisce linee guida sulla modifica dell'algoritmo SHA per la relazione di trust federativa con Office 365"
    keywords="SHA1,SHA256,O365,federazione,aadconnect,adfs,ad fs,modifica algoritmo hash della firma,trust federativo,trust della relying party"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="samueld"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>  

#Modificare l'algoritmo hash della firma relativo al trust della relying party di Office 365

##Overview

AD FS esegue la firma dei propri token per Azure Active Directory per evitare che possano essere alterati. Questa firma può essere basata sull'algoritmo SHA1 o SHA256. Microsoft Azure Active Directory ora supporta i token firmati con algoritmo SHA256, consigliato per garantire il massimo livello di sicurezza. Questo articolo descrive come impostare l'algoritmo di firma del token SHA256 per ottenere la massima sicurezza.

##Modifica dell'algoritmo di firma del token

Una volta impostato l'algoritmo di firma come descritto nella procedura seguente, AD FS inizierà a firmare con l'algoritmo SHA-256 i token per il trust della relying party di Office 365. Non è necessario modificare ulteriormente la configurazione e questa modifica non influisce in alcun modo sull'accesso a Office 365 o ad altre applicazioni di Azure AD da parte degli utenti.

###Uso della console di gestione

* Aprire la console di gestione di AD FS nel server AD FS primario.
* Espandere il nodo di AD FS e fare clic sui trust della relying party.
* Fare clic con il pulsante destro del mouse sul trust della relying party di Office 365/Azure e scegliere Proprietà.
* Nella scheda Avanzate selezionare SHA256 come algoritmo hash di protezione.
* Fare clic su Ok.

![Algoritmo di firma SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)  

###Uso dei cmdlet di AD FS PowerShell

* Sprire PowerShell da qualsiasi server AD FS con privilegi di amministratore.
* Impostare l'algoritmo hash di protezione utilizzando il cmdlet Set-AdfsRelyingPartyTrust.

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

##Altri articoli

* [Ripristinare il trust di Office 365 con AAD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0810_2016-->