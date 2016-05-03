<properties
	pageTitle="Panoramica concettuale dei nomi di dominio personalizzati in Azure Active Directory | Microsoft Azure"
	description="Illustra il framework concettuale per l'uso di nomi di dominio personalizzati in Azure Active Directory, compresa la federazione per Single Sign-On"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Panoramica concettuale dei nomi di dominio personalizzati in Azure Active Directory

Un nome di dominio è una parte importante dell'identificatore per numerose risorse directory: è parte di un nome utente o di un indirizzo di posta elettronica per un utente, parte dell'indirizzo per un gruppo e può essere parte dell'URI dell'ID app per un'applicazione. Una risorsa in Azure Active Directory (Azure AD) può includere un nome di dominio già verificato come di proprietà della directory contenente il servizio. Solo un amministratore globale può eseguire attività di gestione del dominio in Azure AD.

I nomi di dominio in Azure AD sono univoci. Un nome di dominio può essere usato da una singola directory di Azure AD. Se una directory di Azure AD ha verificato un nome di dominio, nessun'altra directory di Azure AD può verificare o usare lo stesso nome di dominio.

## Nomi di dominio iniziali e personalizzati

Ogni nome di dominio in Azure AD è un nome di dominio iniziale o un nome di dominio personalizzato.

Ogni directory di AD Azure ha un nome di dominio iniziale nel formato contoso.onmicrosoft.com. Il terzo livello del nome di dominio, in questo esempio "contoso.com", è stato stabilito al momento della creazione della directory, in genere dall'amministratore che l'ha creata. Il nome di dominio iniziale per una directory non può essere modificato o eliminato. Il nome di dominio iniziale, anche se completamente funzionante, è destinato principalmente all'uso come meccanismo di bootstrap.

Nella maggior parte degli ambienti di produzione una directory ha almeno un dominio personalizzato verificato, ad esempio "contoso.com", che sarà il dominio personalizzato visibile agli utenti finali. Un nome di dominio personalizzato è una risorsa aggiunta da un amministratore globale di una directory. Un nome di dominio personalizzato è un dominio di proprietà dell'organizzazione, ad esempio "contoso.com". La maggior parte delle organizzazioni ha già un nome di dominio che usa per ospitare il sito Web. Questo nome di dominio è noto ai dipendenti perché fa parte del nome utente che usano per accedere alla rete aziendale o per inviare e recuperare la posta elettronica. Il nome di dominio personalizzato deve essere verificato prima di poterlo usare in una directory.

## Nomi di dominio verificati e non verificati

Il nome di dominio iniziale per una directory è valutato in modo implicito come verificato da Azure AD. Quando un amministratore aggiunge un nome di dominio personalizzato a una directory di Azure AD, è inizialmente in uno stato non verificato. Azure AD non consente alle risorse della directory di usare un dominio non verificato. Ciò garantisce che solo una directory può usare un nome di dominio personalizzato e che l'organizzazione proprietaria di Azure AD è quella che possiede effettivamente il nome di dominio.

Azure AD verifica la proprietà di un nome di dominio cercando voci specifiche nei record DNS (Domain Name Service) per il dominio. Per verificare la proprietà di un dominio, un amministratore ottiene da Azure AD le voci DNS che Azure AD cercherà e le aggiunge ai record DNS per il nome di dominio gestito dal registrar per quel dominio. I passaggi per verificare un dominio sono descritti nell'articolo per l'[aggiunta di un dominio personalizzato alla directory di Azure AD](active-directory-add-domain.md).

L'aggiunta di voci DNS ad Azure AD per verificare la proprietà del dominio non incide su altri servizi di dominio, ad esempio hosting Web o posta elettronica.

## Nomi di dominio federati e gestiti

Un nome di dominio personalizzato in Azure AD può essere configurato per fornire agli utenti un'esperienza di accesso federato tra Active Directory locale e Azure AD. La configurazione di un dominio per la federazione richiede aggiornamenti alle risorse con privilegi in Azure AD e anche ad Active Directory locale. La configurazione di un dominio federato deve essere completata da Azure AD Connect o tramite PowerShell. La federazione di un dominio personalizzato non può essere avviata dal portale di Azure classico. [Guardare questo video per informazioni sulla configurazione di AD FS per l'accesso utente con Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

I domini non federati sono talvolta detti domini gestiti. Il dominio iniziale per una directory di Azure AD è valutato in modo implicito come dominio gestito.

## Nomi di dominio primari

Il nome di dominio primario per una directory è il nome di dominio già selezionato, come valore predefinito per la parte "dominio" del nome utente, quando un amministratore crea un nuovo utente nel [portale di Azure classico](https://manage.windowsazure.com/) o un altro portale, ad esempio il portale di amministrazione di Office 365. Una directory di Azure AD può avere un solo nome di dominio primario. Un amministratore può modificare il nome di dominio primario impostando qualsiasi dominio personalizzato verificato che non sia federato o il dominio iniziale.

## Nomi di dominio in Azure AD e altri Microsoft Online Services

Un nome di dominio deve essere verificato in Azure AD prima che possa essere usato da altri Microsoft Online Services, ad esempio Exchange Online, SharePoint Online e Intune. Questi altri servizi richiedono in genere che un amministratore aggiunga una o più voci DNS specifiche per il servizio.

Un'app Web di Azure usa un proprio meccanismo per verificare la proprietà di un dominio. Un dominio deve essere verificato per l'uso con Azure AD anche se è stato già verificato per l'uso con un'app Web di Azure in una sottoscrizione che si basa su Azure AD. Un'app Web di Azure può usare un nome di dominio verificato in una directory di Azure AD diversa da quella che protegge l'app Web.

## Gestione dei nomi di dominio

Le attività di gestione del dominio possono essere completate dal portale di Azure classico e da PowerShell. Molte attività possono essere completate tramite l'API Graph di Azure AD (in anteprima pubblica).

-   [Aggiunta e verifica di un nome di dominio personalizzato](active-directory-add-domain.md)

-   [Gestione di domini nel portale di Azure classico](active-directory-add-manage-domain-names.md)

-   [Uso di PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Uso dell'API Graph di Azure AD per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

<!---HONumber=AcomDC_0420_2016-->