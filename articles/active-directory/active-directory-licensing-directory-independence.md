<properties
   pageTitle="Aggiungere e gestire più directory di Azure Active Directory | Microsoft Azure"
   description="Istruzioni e procedure consigliate per l'aggiunta e la gestione delle directory di Azure Active Directory, che spiega le directory come risorse completamente indipendenti"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>  

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>  

# Aggiungere e gestire più directory di Azure Active Directory

In Azure Active Directory (Azure AD), ogni directory è una risorsa totalmente indipendente: un peer con funzionalità complete e logicamente indipendente dalle altre directory gestite. Non esiste alcuna relazione padre-figlio tra le directory. Questa indipendenza tra le directory include l'indipendenza delle risorse, l'indipendenza amministrativa e l'indipendenza della sincronizzazione.

##Indipendenza delle risorse.

Se si crea o si elimina una risorsa in una directory, ciò non influisce sulle risorse contenute in un'altra directory, con l'eccezione parziale degli utenti esterni, come spiegato più avanti. Se si usa un dominio personalizzato "contoso.com" con una directory, non è possibile usarlo con altre directory.

##Indipendenza amministrativa

Se un utente non amministratore della directory "Contoso" crea una directory di test denominata "Test", si verifica quanto segue:
- Per impostazione predefinita, l'utente che crea una directory viene aggiunto come utente esterno nella nuova directory e gli viene assegnato il ruolo di amministratore globale in quella directory.
- Gli amministratori della directory "Contoso" non hanno privilegi amministrativi diretti per la directory "Test" se tali privilegi non vengono loro concessi specificamente da un amministratore di "Test". Gli amministratori di "Contoso" possono controllare l'accesso alla directory "Test" se controllano l'account utente che ha creato "Test".
- Se si modifica, ovvero si aggiunge o si rimuove, un ruolo di amministratore per un utente in una directory, la modifica non influisce sul ruolo di amministratore che l'utente può avere in un'altra directory.

##Indipendenza della sincronizzazione

È possibile configurare ogni directory di Azure AD in modo indipendente per sincronizzare i dati da una singola istanza di uno degli elementi seguenti:
  - Strumento di sincronizzazione directory (DirSync), per sincronizzare i dati con una singola foresta AD
  - Connettore di Azure Active Directory per Forefront Identity Manager, per sincronizzare i dati con una o più foreste locali e/o origini dati non Azure AD.

##Aggiungere una directory di Microsoft Azure

Per aggiungere una directory di Azure AD nel portale di Azure classico, selezionare l'estensione di Azure Active Directory a sinistra e toccare **Aggiungi**.

> [AZURE.NOTE]   A differenza di altre risorse di Azure, le proprie directory non sono risorse figlio di una sottoscrizione di Azure. Se si annulla o si lascia scadere la propria sottoscrizione di Azure, sarà comunque possibile accedere ai dati delle directory tramite Azure PowerShell, l'API Graph di Azure o altre interfacce come l'interfaccia di amministrazione di Office 365. È anche possibile associare un'altra sottoscrizione alla directory.

Per un'ampia panoramica dei problemi relativi alle licenze di Microsoft Azure e le procedure consigliate, vedere[che cosa sono le licenze di Azure Active Directory?](active-directory-licensing-what-is.md).

<!---HONumber=AcomDC_0928_2016-->