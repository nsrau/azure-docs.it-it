<properties 
	pageTitle="Gestire gruppi in Azure AD" 
	description="Argomento che illustra come gestire i gruppi in Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Gestire gruppi in Azure AD

Un gruppo è una raccolta di utenti e gruppi che possono essere gestiti come una singola unità. Gli utenti e i gruppi che appartengono a un determinato gruppo vengono definiti membri del gruppo. L'uso dei gruppi può semplificare l'amministrazione perché si assegna un set comune di autorizzazioni e diritti a più account in una volta, invece di assegnare le autorizzazioni e i diritti a ogni singolo account.

Ora in Azure AD, è possibile creare solo gruppi di sicurezza.

È possibile usare i gruppi per assegnare facilmente agli utenti l'accesso alle applicazioni nei casi in cui è necessario assegnare più utenti alla stessa applicazione. È possibile usare i gruppi anche quando si configura la gestione dell'accesso di altri servizi online che controllano l'accesso alle risorse, ad esempio, SharePoint Online.

Se è stata configurata la sincronizzazione directory, è possibile visualizzare i gruppi sincronizzati da Windows Server Active Directory locale, la cui proprietà "Originato da" è impostata sul valore "Active Directory locale". È necessario continuare a gestire questi gruppi in Active Directory locale, perché non possono essere gestiti o eliminati nel portale di gestione di Azure.

Se si dispone di Office 365, è possibile visualizzare i gruppi di distribuzione e gruppi di protezione abilitati alla posta creati e gestiti nell'interfaccia di amministrazione di Exchange in Office 365. Questi gruppi hanno la proprietà "Originato da" impostata sul valore "Office 365" e devono continuare a essere gestiti nell'interfaccia di amministrazione di Exchange.

È anche possibile creare gruppi unificati tramite il Pannello di accesso. Nella scheda Configura, in Gestione gruppo impostare il widget **Gli utenti possono creare gruppi di O365** su **Sì**. Se sono presenti gruppi unificati di Office 365 creati nel Pannello di accesso o in Office 365, questi gruppi avranno la proprietà "Originato da" impostata su "Azure Active Directory" e possono essere gestiti dal Pannello di accesso.

Se la gestione gruppo self-service è abilitata per gli utenti (per altre informazioni, vedere Gestione dei gruppi in modalità self-service per gli utenti in Azure AD), l'amministratore del tenant può gestire anche questi gruppi dal portale di gestione di Azure. È possibile aggiungere e rimuovere i membri di un gruppo, aggiungere e rimuovere i proprietari dei gruppi, modificare le proprietà di un gruppo e visualizzare un rapporto attività cronologico dei gruppi che indica l'azione eseguita nel gruppo, chi ha eseguito l'azione e a che ora.

> [AZURE.NOTE]Per poter assegnare un gruppo a un'applicazione, è necessario usare Azure AD Premium. Se si dispone di Azure AD Premium, è inoltre possibile usare i gruppi per assegnare l'accesso alle applicazioni SaaS integrate con Azure AD. Per altre informazioni, vedere Assegnare a un gruppo l'accesso a un'applicazione SaaS in Azure AD.

## Passaggi successivi

- [Amministrazione di Azure AD](active-directory-administer.md)
- [Creare o modificare utenti in Azure AD](active-directory-create-users.md)
- [Gestire password in Azure AD](active-directory-manage-passwords.md)

<!---HONumber=58-->