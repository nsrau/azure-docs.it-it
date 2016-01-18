<properties
   pageTitle="Aggiungere e gestire più directory di Azure Active Directory | Microsoft Azure"
   description="Istruzioni e procedure consigliate per l'aggiunta e la gestione delle directory di Azure Active Directory, che spiega le directory come risorse completamente indipendenti"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/05/2016"
   ms.author="curtand"/>

# Aggiungere e gestire più directory di Azure Active Directory

In Azure Active Directory (Azure AD), ogni directory è una risorsa totalmente indipendente: un peer con funzionalità complete e logicamente indipendente dalle altre directory gestite. Non esiste alcuna relazione padre-figlio tra le directory. Questa indipendenza tra le directory include l'indipendenza delle risorse, l'indipendenza amministrativa e l'indipendenza della sincronizzazione.

##Indipendenza delle risorse.

Se si crea o si elimina una risorsa in una directory, ciò non influisce sulle risorse contenute in un'altra directory, con l'eccezione parziale degli utenti esterni, come spiegato più avanti. Se si usa un dominio personalizzato "contoso.com" con una directory, non è possibile usarlo con altre directory.

##Indipendenza amministrativa

Se un utente senza privilegi di amministratore della directory 'Contoso' crea quindi una directory di test 'Test':-per impostazione predefinita, l'utente che crea una directory viene aggiunto quale utente esterno nella nuova directory e gli viene assegnato il ruolo di amministratore globale in tale directory. - Gli amministratori della directory 'Contoso' non dispongono di privilegi di amministratore diretti alla directory 'Test', a meno che un amministratore di 'Test' gli conceda precisamente questi privilegi. Gli amministratori di 'Contoso' possono controllare l'accesso alla directory 'Test' se controllano l'account utente che ha creato 'Test' - Se si modifica (aggiunge o rimuove) un ruolo di amministratore per un utente in una directory, la modifica non influisce sul ruolo di amministratore che l'utente potrebbe avere in un'altra directory.

##Indipendenza della sincronizzazione

È possibile configurare ogni directory di Azure AD in modo indipendente per ottenere dati sincronizzati da una singola istanza di uno:-strumento di sincronizzazione della Directory (DirSync), per sincronizzare i dati con una singola foresta di Active Directory. -Azure Active Directory Connector per Forefront Identity Manager, per sincronizzare i dati con uno o più insiemi di strutture e/o origini dati Active Directory non Azure.

##Aggiungere una directory di Microsoft Azure

Per aggiungere una directory di Azure AD nel portale di Azure classico, selezionare l'estensione di Azure Active Directory a sinistra e toccare **Aggiungi**.

> [AZURE.NOTE]Si noti, inoltre, che a differenza di altre risorse di Azure, le proprie directory non sono risorse figlio di una sottoscrizione di Azure. Se pertanto si annulla o si lascia scadere la propria sottoscrizione di Azure, sarà comunque possibile accedere ai dati delle directory mediante Azure AD PowerShell, l'API Graph di Azure o altre interfacce come l'interfaccia di amministrazione di Office 365. È anche possibile associare un'altra sottoscrizione alla directory.

Per un'ampia panoramica dei problemi relativi alle licenze di Microsoft Azure e le procedure consigliate, vedere[che cosa sono le licenze di Azure Active Directory?](active-directory-licensing-what-is.md).

<!---HONumber=AcomDC_0107_2016-->