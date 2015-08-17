<properties
	pageTitle="Gestire password in Azure AD | Microsoft Azure"
	description="Argomento che illustra come gestire le password in Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="swadwha"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="curtand"/>

# Gestire password in Azure AD

Gli amministratori possono reimpostare le password degli utenti nel portale di Azure classico. Fare clic sul nome della directory, quindi nella pagina Utenti fare clic sul nome dell'utente e su **Reimposta password** nella parte inferiore del portale.

La parte rimanente di questo argomento descrive il set di funzionalità di gestione delle password completo supportato da Azure Active Directory:

- La modifica delle **password self-service** consente agli utenti o agli amministratori di cambiare le password scadute o non scadute senza dover chiamare un amministratore o rivolgersi al Supporto tecnico.
- La reimpostazione delle **password self-service** consente agli utenti o agli amministratori di reimpostare automaticamente le password senza dover chiamare un amministratore o rivolgersi al Supporto tecnico. La reimpostazione delle password self-service è disponibile solo in Azure AD Premium o Basic. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
- La **reimpostazione delle password avviata dall'amministratore** consente a un amministratore di reimpostare la password di un utente o di un altro amministratore dal portale di gestione di Azure.
- I **report sulle attività di gestione delle password** forniscono agli amministratori informazioni dettagliate sulle attività di reimpostazione delle password e di registrazione che si verificano all'interno dell'organizzazione.
- Il **writeback delle password** permette di gestire le password locali dal cloud, in modo che tutti gli scenari descritti in precedenza possano essere eseguiti da o per conto di utenti federati o con sincronizzazione tramite password. Per il writeback delle password è necessaria una licenza di Azure AD Premium. Per altre informazioni, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)

> [AZURE.NOTE]Azure AD Premium è disponibile per i clienti cinesi che usano l'istanza globale di Azure AD. Azure AD Premium non è attualmente supportato nel servizio Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).

Usare i collegamenti seguenti per passare alla documentazione rilevante:

- [Panoramica: gestione delle password in Azure AD](https://msdn.microsoft.com/library/azure/dn683880.aspx)
- [Reimpostazione password self-service in Azure AD: come abilitare, configurare e testare la reimpostazione delle password self-service](https://msdn.microsoft.com/library/azure/dn683881.aspx)
- [Reimpostazione password self-service in Azure AD: come personalizzare la reimpostazione delle password in base alle esigenze](https://msdn.microsoft.com/library/azure/dn688249.aspx)
- [Reimpostazione password self-service in Azure AD: procedure consigliate per la distribuzione e la gestione](https://msdn.microsoft.com/library/azure/dn903643.aspx)
- [Reimpostazione password self-service in Azure AD: come visualizzare le attività di gestione delle password nel tenant](https://msdn.microsoft.com/library/azure/dn903641.aspx)
- [Writeback delle password: come configurare Azure AD per la gestione delle password locali](https://msdn.microsoft.com/library/azure/dn903642.aspx)
- [Domande frequenti e risoluzione dei problemi per la gestione delle password in Azure AD](https://msdn.microsoft.com/library/azure/dn683878.aspx)

## Passaggi successivi

- [Amministrazione di Azure AD](active-directory-administer.md)
- [Creare o modificare utenti in Azure AD](active-directory-create-users.md)
- [Gestire gruppi in Azure AD](active-directory-manage-groups.md)

<!---HONumber=August15_HO6-->