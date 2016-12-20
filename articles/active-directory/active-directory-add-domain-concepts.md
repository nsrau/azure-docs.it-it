---
title: Panoramica concettuale dei nomi di dominio personalizzati in Azure Active Directory | Documentazione Microsoft
description: Illustra il framework concettuale per l&quot;uso di nomi di dominio personalizzati in Azure Active Directory, compresa la federazione per Single Sign-On
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: fd0c5def-0da2-43af-81bc-76f4cfe86afd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 12d41a77655c4892a9dc20979ef98b1bd39f94c5


---
# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Panoramica concettuale dei nomi di dominio personalizzati in Azure Active Directory
Un nome di dominio è una parte importante dell'identificatore per numerose risorse directory: è parte di un nome utente o di un indirizzo di posta elettronica per un utente, parte dell'indirizzo per un gruppo e può essere parte dell'URI dell'ID app per un'applicazione. Una risorsa in Azure Active Directory (Azure AD) può includere un nome di dominio già verificato come di proprietà della directory contenente il servizio. Solo un amministratore globale può eseguire attività di gestione del dominio in Azure AD.

I nomi di dominio in Azure AD sono univoci. Un nome di dominio può essere usato da una singola directory di Azure AD. Se una directory di Azure AD ha verificato un nome di dominio, nessun'altra directory di Azure AD può verificare o usare lo stesso nome di dominio.

## <a name="initial-and-custom-domain-names"></a>Nomi di dominio iniziali e personalizzati
Ogni nome di dominio in Azure AD è un nome di dominio iniziale o un nome di dominio personalizzato.

Ogni directory di AD Azure ha un nome di dominio iniziale nel formato contoso.onmicrosoft.com. Nell'esempio "contoso.onmicrosoft.com" il nome di dominio di terzo livello è stato stabilito al momento della creazione della directory, in genere dall'amministratore che l'ha creata. Il nome di dominio iniziale per una directory non può essere modificato o eliminato. Il nome di dominio iniziale, anche se completamente funzionante, è destinato principalmente a essere usato come meccanismo di bootstrap finché non viene verificato un nome di dominio personalizzato.

Nella maggior parte degli ambienti di produzione una directory ha almeno un dominio personalizzato verificato, ad esempio "contoso.com", che sarà il dominio personalizzato visibile agli utenti finali. Un nome di dominio personalizzato, ad esempio "contoso.com", è un nome di dominio di proprietà dall'organizzazione che lo usa ad esempio come hosting del sito Web. Questo nome di dominio è noto ai dipendenti perché fa parte del nome utente che usano per accedere alla rete aziendale o per inviare e recuperare la posta elettronica.

Il nome di dominio personalizzato deve essere aggiunto alla directory e deve essere verificato prima di poterlo usare in Azure AD.

## <a name="verified-and-unverified-domain-names"></a>Nomi di dominio verificati e non verificati
Il nome di dominio iniziale per una directory è valutato in modo implicito come verificato da Azure AD. Quando un amministratore aggiunge un nome di dominio personalizzato a una directory di Azure AD, è inizialmente in uno stato non verificato. Azure AD non consente alle risorse della directory di usare un nome di dominio non verificato. Ciò garantisce che solo una directory può usare un nome di dominio specifico e che l'organizzazione che usa il nome di dominio sia effettivamente quella che possiede il nome di dominio.

Azure AD verifica la proprietà di un nome di dominio cercando nel file di zona DNS (Domain Name Service) voci specifiche per il nome di dominio. Per verificare la proprietà di un nome di dominio, un amministratore ottiene da Azure AD la voce DNS da cercare e la aggiunge al file di zona DNS per il nome di dominio. Il file di zona DNS è aggiornato dal registrar del nome di dominio per il dominio specifico. La procedura per verificare un dominio è descritta nell'articolo relativo all' [aggiunta di un dominio personalizzato alla directory di Azure AD](active-directory-add-domain.md).

L'aggiunta di una voce DNS al file di zona per il nome di dominio non influenza gli altri servizi di dominio, ad esempio posta elettronica o hosting web.

## <a name="federated-and-managed-domain-names"></a>Nomi di dominio federati e gestiti
Un nome di dominio personalizzato in Azure AD può essere configurato per fornire agli utenti un'esperienza di accesso federato tra Active Directory locale e Azure AD. Per configurare un dominio per la federazione, è necessario l'aggiornamento alle risorse con privilegi in Azure AD e anche a Windows Server Active Directory. La configurazione di un dominio federato deve essere completata da Azure AD Connect o tramite PowerShell. La federazione di un dominio personalizzato non può essere avviata dal portale di Azure classico. [Guardare questo video per informazioni sulla configurazione di ADFS per l'accesso utente con Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

I domini non federati sono talvolta detti domini gestiti. Il dominio iniziale per una directory di Azure AD è valutato in modo implicito come dominio gestito.

## <a name="primary-domain-names"></a>Nomi di dominio primari
Il nome di dominio primario per una directory è il nome di dominio già selezionato come valore predefinito per la parte "dominio" del nome utente, quando un amministratore crea un nuovo utente nel [portale di Azure classico](https://manage.windowsazure.com/) o in un altro portale, ad esempio il portale dell'amministratore di Office 365. Una directory può avere un solo nome di dominio primario. Un amministratore può modificare il nome di dominio primario impostando qualsiasi dominio personalizzato verificato che non sia federato o il dominio iniziale.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Nomi di dominio in Azure AD e altri Microsoft Online Services
Un nome di dominio deve essere verificato in Azure AD prima che possa essere usato da altri Microsoft Online Services, ad esempio Exchange Online, SharePoint Online e Intune. Questi altri servizi richiedono in genere che un amministratore aggiunga una o più voci DNS specifiche per il servizio.

Un'app Web di Azure usa un proprio meccanismo per verificare la proprietà di un dominio. Un dominio deve essere verificato per l'uso con Azure AD anche se è stato già verificato per l'uso con un'app Web di Azure in una sottoscrizione che si basa su Azure AD. Un'app Web di Azure può usare un nome di dominio verificato in una directory diversa da quella che protegge l'app Web.

## <a name="managing-domain-names"></a>Gestione dei nomi di dominio
Le attività di gestione del dominio possono essere completate dal portale di Azure classico e da PowerShell. Molte attività possono essere completate tramite l'API Graph di Azure AD (in anteprima pubblica).

* [Aggiunta e verifica di un nome di dominio personalizzato](active-directory-add-domain.md)
* [Gestione di domini nel portale di Azure classico](active-directory-add-manage-domain-names.md)
* [Uso di PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Uso dell'API Graph di Azure AD per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)




<!--HONumber=Nov16_HO3-->


