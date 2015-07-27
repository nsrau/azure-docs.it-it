<properties 
	pageTitle="Connettere le directory con Azure AD Connect" 
	description="Descrizione delle impostazioni personalizzate delle directory connesse di Azure AD Connect." 
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



# Connettere le directory con Azure AD Connect

Per Impostazioni personalizzate, non è necessario un account amministratore dell’organizzazione per la connessione alle foreste di Active Directory. La procedura guidata accetterà un account utente di dominio o locale. Tuttavia, l'account viene utilizzato come account di AD Connector locale, vale a dire l'account che legge e scrive le informazioni di directory per la sincronizzazione.

Ciò significa che è necessario assegnare autorizzazioni aggiuntive per abilitare gli scenari seguenti:

Scenario |Autorizzazione
------------- | ------------- |
Sincronizzazione delle password| <li>Replica modifiche directory.</li> <li>Replica di tutte le modifiche directory.</li>
Distribuzione ibrida di Exchange|Vedere [Attributi e autorizzazioni di writeback di AAD Sync per la distribuzione ibrida di Exchange con Office 365](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange).
Writeback delle password | <li>Cambia password</li><li>Reimposta password</li>
Writeback di utenti, gruppi e dispositivi|Autorizzazioni di scrittura per gli oggetti di directory e gli attributi di cui si desidera eseguire il "writeback"
Single Sign-On e AD FS| Autorizzazioni di amministratore di dominio nel dominio in cui si trovano i server federati.





**Risorse aggiuntive**

* [Ulteriori informazioni sugli account e le autorizzazioni Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Autorizzazioni per la sincronizzazione delle password](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Autorizzazioni per Exchange ibrido](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Autorizzazioni per il writeback delle password](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect in MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=July15_HO3-->