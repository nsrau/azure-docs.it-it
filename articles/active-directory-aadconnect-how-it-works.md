<properties 
	pageTitle="Funzionamento di Azure AD Connect" 
	description="Informazioni sul funzionamento di Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Funzionamento di Azure AD Connect


<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/active-directory-aadconnect/" title="Che cos&apos;è" class="current">Che cos'è</a> <a href="/it-it/documentation/articles/active-directory-aadconnect-how-it-works/" title="Come funziona">Come funziona</a> <a href="/it-it/documentation/articles/active-directory-aadconnect-get-started/" title="Introduzione">Introduzione</a> <a href="/it-it/documentation/articles/active-directory-aadconnect-whats-next/" title="Novità">Novità</a> <a href="/it-it/documentation/articles/active-directory-aadconnect-learn-more/" title="Altre informazioni">Altre informazioni</a>
</div>

Azure Active Directory Connect è costituito da tre parti principali. Queste parti sono i servizi di sincronizzazione, il componente facoltativo Active Directory Federation Services e il componente di monitoraggio eseguito usando Azure AD Connect Health.


<center>![Stack di Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack.png) </center>

- Sincronizzazione: questa parte è costituita dai componenti e le funzionalità resi disponibili in precedenza come DirSync e AAD Sync. Si tratta della parte responsabile della creazione di utenti e gruppi. Questa parte garantisce anche che le informazioni su utenti e gruppi nell'ambiente locale corrispondano nel cloud.
- ADFS: questa è una parte facoltativa di Azure AD Connect e può essere usata per configurare un ambiente ibrido mediante un'infrastruttura ADFS locale. Questa parte può essere usata dalle organizzazioni per gestire distribuzioni complesse che includono, ad esempio, accesso SSO di aggiunta al dominio, applicazione di criteri di accesso di Active Directory e autenticazione a più fattori con smart card o di terze parti. Per altre informazioni sulla configurazione di SSO, vedere [DirSync con Single-Sign On](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Monitoraggio dell'integrità: per le distribuzioni complesse che usano ADFS, Azure AD Connect Health può offrire monitoraggio affidabile dei server federativi e una posizione centralizzata nel portale di Azure per visualizzare questa attività. Per altre informazioni, vedere [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).






**Risorse aggiuntive**

* [Usare l'infrastruttura di identità locale nel cloud](active-directory-aadconnect.md)
* [Introduzione ad Azure AD Connect](active-directory-aadconnect-get-started.md)
* [Novità di Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [Altre informazioni](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect in MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->