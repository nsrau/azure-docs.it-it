<properties 
	pageTitle="Gruppi dedicati in Azure Active Directory | Microsoft Azure" 
	description="Panoramica dei gruppi dedicati in Azure AD e come vengono creati." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/09/2015" 
	ms.author="femila"/>

# Gruppi dedicati in Azure Active Directory

In Azure Active Directory vengono creati automaticamente gruppi dedicati e appartenenze ai gruppi per tali gruppi. È possibile aggiungere o rimuovere membri nei gruppi dedicati tramite il portale di gestione di Azure, i cmdlet di Windows PowerShell oppure a livello di codice. Per abilitare i gruppi dedicati, nella scheda Configura del portale di gestione di Azure impostare l'opzione Abilita gruppi dedicati su Sì.

Nella versione di anteprima pubblica corrente, dopo aver impostato l'opzione Abilita gruppi dedicati su **Sì**, è inoltre possibile consentire alla directory di creare automaticamente il gruppo dedicato Tutti gli utenti mediante l'impostazione dell'opzione Abilita il gruppo "Tutti gli utenti" su **Sì**. È quindi possibile modificare il nome di questo gruppo dedicato digitando il nuovo nome nel campo Nome visualizzato per il gruppo “**Tutti gli utenti**”

Il gruppo dedicato Tutti gli utenti può essere utile se si desidera assegnare le stesse autorizzazioni a tutti gli utenti in una directory. Ad esempio, è possibile consentire a tutti gli utenti di una directory di accedere a un'applicazione SaaS tramite l'assegnazione dell'accesso a tale applicazione al gruppo dedicato Tutti gli utenti.

Di seguito sono elencati alcuni argomenti contenenti informazioni aggiuntive su Azure Active Directory

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Informazioni su Azure Active Directory](active-directory-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->