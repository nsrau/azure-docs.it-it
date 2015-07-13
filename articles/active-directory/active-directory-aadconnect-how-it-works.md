<properties 
	pageTitle="Funzionamento di Azure AD Connect" 
	description="Informazioni sul funzionamento di Azure AD Connect." 
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

# Funzionamento di Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="Che cos&apos;è">Che cos'è</a> <a href="../active-directory-aadconnect-how-it-works/" title="Come funziona" class="current">Come funziona</a> <a href="../active-directory-aadconnect-get-started/" title="Introduzione">Introduzione</a> <a href="../active-directory-aadconnect-whats-next/" title="Passaggi successivi">Passaggi successivi</a> <a href="../active-directory-aadconnect-learn-more/" title="Altre informazioni">Altre informazioni</a>
</div>
Azure Active Directory Connect è costituito da tre parti principali, ovvero i servizi di sincronizzazione, il componente facoltativo Active Directory Federation Services e il componente di monitoraggio eseguito tramite [Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


<center>![Stack di Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Sincronizzazione: questa parte è costituita dai componenti e le funzionalità resi disponibili in precedenza come DirSync e AAD Sync. Si tratta della parte responsabile della creazione di utenti e gruppi. Questa parte garantisce anche che le informazioni su utenti e gruppi nell'ambiente locale corrispondano a quelle nel cloud.
- ADFS: questa è una parte facoltativa di Azure AD Connect e può essere usata per configurare un ambiente ibrido mediante un'infrastruttura ADFS locale. Può essere usata dalle organizzazioni per gestire distribuzioni complesse che includono, ad esempio, accesso SSO di aggiunta al dominio, applicazione di criteri di accesso di Active Directory e autenticazione a più fattori con smart card o di terze parti. Per altre informazioni sulla configurazione di SSO, vedere [DirSync con Single-Sign On](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Monitoraggio dell'integrità: per le distribuzioni complesse che usano ADFS, Azure AD Connect Health può offrire monitoraggio affidabile dei server federativi e una posizione centralizzata nel portale di Azure per visualizzare questa attività. Per altre informazioni, vedere [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


## Componenti di supporto di Azure AD Connect

Di seguito è riportato un elenco di prerequisiti e componenti di supporto che verranno installati da Azure AD Connect nel server su cui è stato impostato Azure AD Connect. L'elenco riguarda un'istallazione di SQL Express di base. Se si sceglie di usare una versione diversa di SQL Server nella pagina Installazione dei servizi di sincronizzazione, i componenti di SQL Server 2012 elencati di seguito non verranno installati.

- Connettore Azure AD di Azure AD Connect
- Utilità della riga di comando di Microsoft SQL Server 2012
- Client nativo di Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Modulo di Azure Active Directory per Windows PowerShell
- Assistente per l'accesso a Microsoft Online Services per professionisti IT
- Microsoft Visual C++ 2013 Redistribution Package




 

<!---HONumber=62-->