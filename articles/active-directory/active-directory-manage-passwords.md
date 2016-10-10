<properties
	pageTitle="Gestione delle password in Azure Active Directory | Microsoft Azure"
	description="Come gestire password in Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="curtand"/>

# Gestire password in Azure Active Directory

Gli amministratori possono reimpostare le password degli utenti in Azure Active Directory (Azure AD) nel portale di Azure classico. Fare clic sul nome della directory, quindi nella pagina Utenti fare clic sul nome dell'utente e su **Reimposta password** nella parte inferiore del portale.

La parte rimanente di questo argomento descrive il set completo di funzionalità di gestione delle password supportato da Azure AD tra cui:

- La modifica delle **password self-service** consente agli utenti o agli amministratori di cambiare le password scadute o non scadute senza dover chiamare un amministratore o rivolgersi al Supporto tecnico.
- La reimpostazione delle **password self-service** consente agli utenti o agli amministratori di reimpostare automaticamente le password senza dover chiamare un amministratore o rivolgersi al Supporto tecnico. La reimpostazione delle password self-service è disponibile solo in Azure AD Premium o Basic. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
- La **reimpostazione delle password avviata dall'amministratore** consente a un amministratore di reimpostare la password di un utente finale o di un altro amministratore dal portale di Azure classico.
- I **report sulle attività di gestione delle password** forniscono agli amministratori informazioni dettagliate sulle attività di reimpostazione delle password e di registrazione che si verificano all'interno dell'organizzazione.
- Il **writeback delle password** permette di gestire le password locali dal cloud, in modo che tutti gli scenari descritti in precedenza possano essere eseguiti da o per conto di utenti federati o con sincronizzazione tramite password. Per il writeback delle password è necessaria una licenza di Azure AD Premium. Per altre informazioni, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)

> [AZURE.NOTE]
Azure AD Premium è disponibile per i clienti cinesi che usano l'istanza globale di Azure AD. Azure AD Premium non è attualmente supportato nel servizio Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Usare i collegamenti seguenti per passare alla documentazione rilevante:

- [Panoramica: gestione delle password in Azure AD](active-directory-passwords-how-it-works.md)
- [Reimpostazione password self-service in Azure AD: come abilitare, configurare e testare la reimpostazione delle password self-service](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
- [Reimpostazione password self-service in Azure AD: come personalizzare la reimpostazione delle password in base alle esigenze](active-directory-passwords-customize.md)
- [Reimpostazione password self-service in Azure AD: procedure consigliate per la distribuzione e la gestione](active-directory-passwords-best-practices.md)
- [Reimpostazione password self-service in Azure AD: come visualizzare le attività di gestione delle password nel tenant](active-directory-passwords-get-insights.md)
- [Writeback delle password: come configurare Azure AD per la gestione delle password locali](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
- [Risoluzione dei problemi di gestione delle password in Azure AD](active-directory-passwords-troubleshoot.md)
- [Domande frequenti sulla gestione delle password di Azure AD](active-directory-passwords-faq.md)


## Passaggi successivi

- [Amministrazione di Azure AD](active-directory-administer.md)
- [Creare o modificare utenti in Azure AD](active-directory-create-users.md)
- [Gestire gruppi in Azure AD](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0928_2016-->