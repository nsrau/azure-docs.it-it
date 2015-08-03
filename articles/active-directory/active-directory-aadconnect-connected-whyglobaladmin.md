<properties 
	pageTitle="Per quale motivo è richiesto un account amministratore globale di Azure AD per configurare Azure AD Connect" 
	description="Descrizione del motivo per cui è necessario un account di amministratore globale." 
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
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Per quale motivo è richiesto un account amministratore globale di Azure AD per configurare Azure AD Connect

Nella tabella seguente sono illustrati i motivi per cui è richiesto un account di amministratore globale di Azure AD per la configurazione di Azure AD Connect.

Condizioni | Descrizione 
------------- | ------------- |
Per Impostazioni rapide e DirSync Upgrade | La sincronizzazione viene abilitata (se richiesta) nella directory Azure AD e viene creato l’account Azure AD che sarà utilizzato per le operazioni di sincronizzazione ricorrenti (l’account Azure AD Connector). 
Per le impostazioni personalizzate | La sincronizzazione viene abilitata nella directory Azure AD e viene creato l’account Azure AD che sarà utilizzato per le operazioni di sincronizzazione ricorrenti (l’account Azure AD Connector). Per il Single Sign-on con l'opzione di ADFS, è possibile leggere e configurare le proprietà di federazione in Azure AD.



**Risorse aggiuntive**


* [Ulteriori informazioni sugli account e le autorizzazioni Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect in MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO4-->