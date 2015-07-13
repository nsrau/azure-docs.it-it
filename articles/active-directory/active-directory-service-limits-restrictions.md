<properties 
	pageTitle="Restrizioni e limiti del servizio Azure AD" 
	description="Limitazioni d'utilizzo e altri limiti di servizio per il servizio Azure Active Directory." 
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

# Restrizioni e limiti del servizio Azure AD

Ecco le limitazioni d'utilizzo e altri limiti di servizio per il servizio Azure Active Directory. Se si sta cercando il set completo dei limiti del servizio Microsoft Azure, vedere [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](../azure-subscription-service-limits.md).

## Directory

Un singolo utente può essere associato a massimo 20 directory di Azure Active Directory. Questo limite può essere raggiunto in uno dei seguenti esempi:

- Un singolo utente crea 20 directory.
- Un singolo utente viene aggiunto a 20 directory come membro.
- Un singolo utente crea 10 directory e viene aggiunto in seguito da altri utenti a 10 directory diverse.

## Oggetti

- Non esistono limiti per i sottoscrittori di Azure Active Directory Premium o Azure Active Directory Basic, Enterprise Mobility Suite, Office 365, Microsoft Intune o qualsiasi altro servizio online Microsoft che si basa su Azure Active Directory per i servizi directory.
- Possono essere usati fino a 500.000 oggetti in una singola directory con l'edizione gratuita di Azure Active Directory.
- Un utente non amministratore può creare fino a 250 oggetti.

##Estensioni dello schema

Attualmente le entità "User", "Group", "TenantDetail", "Device", "Application" e "ServicePrincipal" possono essere estese con gli attributi a valore singolo del tipo "String" o "Binary". Tra questi sono incluse le seguenti limitazioni:

- Le estensioni di tipo stringa possono contenere massimo 256 caratteri.
- Le estensioni di tipo binario sono limitate a 256 byte.
- 100 valori di estensione (tra TUTTI i tipi e TUTTE le applicazioni) possono scritti in ogni singolo oggetto.
- Le estensioni dello schema sono disponibili solo nella versione di anteprima dell'API Graph 1.21. All'applicazione deve essere concesso l'accesso in scrittura per registrare un'estensione.

## Applicazioni

Possono essere proprietari di una singola applicazione 10 utenti al massimo.

## Gruppi 

- È possibile che siano proprietari di un singolo gruppo 10 utenti al massimo.
- Qualsiasi numero di oggetti può essere membro di un singolo gruppo in Azure Active Directory.


> [AZURE.NOTE]
> 
Esiste un limite per il numero di oggetti che è possibile sincronizzare dall'Active Directory locale ad Azure Active Directory. Se si usa DirSync il limite è 15.000 utenti. Se si usa la connessione Azure AD Connect, il limite è 50.000 utenti.

## Pannello di accesso

- Non esiste alcun limite al numero di applicazioni che è possibile visualizzare nel Pannello di accesso per l'utente finale per i sottoscrittori di Azure AD Premium o Azure AD Basic o Enterprise Mobility Suite.
- Possono essere visualizzate nel Pannello di accesso per ogni utente finale con l'edizione gratuita di Azure Active Directory un massimo di 10 app SaaS preintegrate (esempi: Box, Salesforce o Dropbox). Gli utenti finali potrebbero visualizzare più di 10 app se l'organizzazione ha sviluppato app che sono state integrate in un secondo momento con Azure Active Directory. Questo limite non si applica agli account di amministratore.

## Report

È possibile visualizzare o scaricare in qualsiasi report un massimo di 1.000 righe. Eventuali dati aggiuntivi vengono troncati.

## Passaggi successivi
- [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
- [Come vengono associate le sottoscrizioni di Azure ad Azure AD](active-directory-how-subscriptions-associated-directory.md)
- [Terminologia di Azure AD](active-directory-terminology.md)


 

<!---HONumber=62-->