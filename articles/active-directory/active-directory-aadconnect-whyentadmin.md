<properties 
	pageTitle="Perché è necessario un account amministratore dell’organizzazione" 
	description="Descrizione delle impostazioni personalizzate." 
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

# Per quale motivo è necessario un account amministratore per la connessione ad AD DS quando si configura Azure AD Connect

Nella tabella seguente sono illustrati i motivi per cui è necessario un account amministratore dell’organizzazione per configurare Azure AD Connect.

Condizioni | Descrizione 
------------- | ------------- |
Per Impostazioni rapide e DirSync Upgrade | <li>Per Impostazioni rapide, è stato creato l’account Active Directory locale utilizzato per la sincronizzazione (altrimenti noto come account AD Connector) al quale sono state assegnate le autorizzazioni corrette per la sincronizzazione e la sincronizzazione delle password </li> <li>Per Dirsync Upgrade, è stata ripristinata la password dell’account AD Connector configurato correntemente ed è stato configurato il nuovo servizio di sincronizzazione Azure AD Connect per utilizzare questo account. </li>



**Risorse aggiuntive**


* [Ulteriori informazioni sugli account e le autorizzazioni Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect in MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO3-->