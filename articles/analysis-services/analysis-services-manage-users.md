---
title: Autenticazione e autorizzazioni utente in Azure Analysis Services | Microsoft Docs
description: Questo articolo descrive come Azure Analysis Services usa Azure Active Directory (Azure AD) per la gestione delle identità e l'autenticazione degli utenti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 551bae56565140da3754e74a23b1cc18087f1171
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487440"
---
# <a name="authentication-and-user-permissions"></a>Autenticazione e autorizzazioni utente

Azure Analysis Services usa Azure Active Directory (Azure AD) per la gestione delle identità e l'autenticazione degli utenti. Qualsiasi utente che crea, gestisce o si connette a un server Azure Analysis Services deve disporre di un'identità utente valida in un [tenant di Azure AD](../active-directory/fundamentals/active-directory-whatis.md) nella stessa sottoscrizione.

Azure Analysis Services supporta la [collaborazione B2B di Azure AD](../active-directory/external-identities/what-is-b2b.md). Con B2B, gli utenti esterni all'organizzazione possono essere invitati come utenti guest in una directory di Azure AD. Gli utenti guest possono appartenere a un'altra directory di tenant di Azure AD o a qualsiasi indirizzo e-mail valido. Dopo che l'utente è stato invitato e ha accettato l'invito inviato da Azure tramite posta elettronica, l'identità dell'utente viene aggiunta alla directory tenant. Le identità possono essere aggiunte ai gruppi di sicurezza o come membri di un ruolo del database o di amministratore del server.

![Architettura dell'autenticazione di Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Authentication

Per connettersi a un server tutti gli strumenti e le applicazioni client usano una o più [librerie client](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) di Analysis Services (AMO, MSOLAP, ADOMD). 

Queste tre librerie client supportano sia il flusso interattivo di Azure AD sia i metodi di autenticazione non interattivi. I due metodi di autenticazione integrata di Active Directory e della password di Active Directory non interattive possono essere usati nelle applicazioni che adottano AMOMD e MSOLAP. Questi due metodi non aprono mai finestre di dialogo popup.

Applicazioni client quali Excel e Power BI Desktop e strumenti come SQL Server Management Studio (SSMS) ed estensioni di progetti Analysis Services per Visual Studio installano le versioni più recenti delle librerie quando vengono aggiornate alla versione più recente. L'estensione di progetti Power BI Desktop, SSMS e Analysis Services viene aggiornata ogni mese. Excel viene [aggiornato con Microsoft 365](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Microsoft 365 gli aggiornamenti sono meno frequenti e alcune organizzazioni usano il canale posticipato, il che significa che gli aggiornamenti vengono posticipati fino a tre mesi.

A seconda dello strumento o dell'applicazione client in uso, il tipo di autenticazione e la modalità di accesso possono essere diverse. Ogni applicazione può supportare funzionalità diverse per la connessione ai servizi cloud quali Azure Analysis Services.

Power BI Desktop, Visual Studio e SSMS supportano Active Directory autenticazione universale, un metodo interattivo che supporta anche Azure AD Multi-Factor Authentication (multi-factor authentication). Azure AD autenticazione a più fattori consente di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso un processo di accesso semplice. MFA include funzionalità avanzate di autenticazione con varie opzioni di verifica (chiamata telefonica, SMS, smart card con PIN o notifica tramite app per dispositivi mobili). La convalida di MFA interattiva con Azure AD può avvenire attraverso una finestra popup. **È consigliata l'autenticazione universale**.

Se si accede ad Azure con un account di Windows e l'autenticazione universale non è selezionata o disponibile (Excel), è richiesto [Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs). Con la Federazione, gli utenti Azure AD e Microsoft 365 vengono autenticati con le credenziali locali e possono accedere alle risorse di Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

I server di Azure Analysis Services supportano connessioni da [SSMS V17.1](/sql/ssms/download-sql-server-management-studio-ssms) e versioni successive usando l'autenticazione di Windows, l'autenticazione della password di Active Directory e l'autenticazione universale di Active Directory. In generale, è consigliabile usare l'autenticazione universale di Active Directory per i motivi indicati di seguito:

*  Supporta i metodi autenticazione interattiva e non interattiva.

*  Supporta gli utenti guest di Azure B2B invitati al tenant di Azure AS. Quando si connettono a un server, gli utenti guest devono selezionare l'autenticazione universale di Active Directory.

*  Supporta l'autenticazione a più fattori (Multi-Factor Authentication, MFA). Azure AD autenticazione a più fattori consente di proteggere l'accesso ai dati e alle applicazioni con una gamma di opzioni di verifica: telefonata, SMS, smart card con pin o notifica tramite app per dispositivi mobili. La convalida di MFA interattiva con Azure AD può avvenire attraverso una finestra popup.

### <a name="visual-studio"></a>Visual Studio

Visual Studio si connette ad Azure Analysis Services tramite l'autenticazione universale di Active Directory con supporto MFA. Agli utenti viene richiesto di accedere ad Azure alla prima distribuzione. Gli utenti devono accedere ad Azure con un account che disponga delle autorizzazioni di amministratore del server per il server nel quale stanno eseguendo la distribuzione. Al primo accesso ad Azure viene loro assegnato un token. Il token è memorizzato nella cache per le connessioni future.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop si connette ad Azure Analysis Services tramite l'autenticazione universale di Active Directory con supporto MFA. Agli utenti viene richiesto di accedere ad Azure alla prima connessione. Gli utenti devono accedere ad Azure con un account incluso in ruolo del database o di amministratore del server.

### <a name="excel"></a>Excel

Gli utenti di Excel possano connettersi a un server usando un account di Windows, un ID aziendale (indirizzo e-mail) o un indirizzo e-mail esterno. Le identità di posta elettronica esterne deve essere già presenti in Azure AD come utenti guest.

## <a name="user-permissions"></a>Autorizzazioni utente

Gli **amministratori del server** sono specifici di un'istanza del server Azure Analysis Services. Si connettono usando strumenti quali il portale di Azure, SQL Server Management Studio e Visual Studio per eseguire attività quali l'aggiunta di database e la gestione dei ruoli utente. Per impostazione predefinita, l'utente che crea il server viene automaticamente aggiunto come amministratore del server di Analysis Services. È possibile aggiungere altri amministratori tramite il portale di Azure o SSMS. Gli amministratori del server devono disporre di un account nel tenant di Azure AD nella stessa sottoscrizione. Per altre informazioni, vedere [Gestire gli amministratori del server](analysis-services-server-admins.md). 

Gli **utenti del database** si connettono ai database modello tramite applicazioni client quali Excel o Power BI. Gli utenti devono essere aggiunti ai ruoli database. I ruoli database definiscono l'amministratore, il processo o le autorizzazioni di lettura per un database. È importante comprendere che gli utenti del database in un ruolo con autorizzazioni di amministratore sono diversi dagli amministratori di server. Per impostazione predefinita, tuttavia, gli amministratori del server sono anche amministratori del database. Per altre informazioni, vedere [Gestire ruoli e utenti del database](analysis-services-database-users.md).

**Proprietari delle risorse di Azure**. I proprietari delle risorse gestiscono le risorse di una sottoscrizione di Azure. Possono aggiungere le identità degli utenti di Azure AD ai ruoli di proprietario o collaboratore di una sottoscrizione usando il **controllo di accesso** nel portale di Azure o i modelli di Azure Resource Manager. 

![Controllo di accesso nel portale di Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

I ruoli di questo livello si applicano agli utenti o agli account che devono eseguire attività completabili nel portale o tramite i modelli di Azure Resource Manager. Per altre informazioni, vedere [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Ruoli del database

 I ruoli definiti per un modello tabulare sono ruoli del database. ovvero contengono membri costituiti da utenti di Azure AD e gruppi di sicurezza che dispongono di autorizzazioni specifiche che definiscono le azioni che tali membri possono eseguire su un database modello. Un ruolo del database viene creato come oggetto separato nel database e si applica solo al database in cui è stato creato.   
  
 Per impostazione predefinita, quando si crea un nuovo progetto di modello tabulare, il progetto di modello non dispone di alcun ruolo. È possibile definire i ruoli nella finestra di dialogo Gestione ruoli di Visual Studio. Se i ruoli vengono definiti durante la progettazione dei modelli di progetto, sono applicati solo ai database dell'area di lavoro del modello. Quando viene distribuito il modello, gli stessi ruoli vengono applicati al modello distribuito. Dopo la distribuzione di un modello, gli amministratori del server e del database possono gestire ruoli e membri tramite SSMS. Per altre informazioni, vedere [Gestire ruoli e utenti del database](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Passaggi successivi

[Gestire l'accesso alle risorse tramite i gruppi di Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Gestire ruoli e utenti del database](analysis-services-database-users.md)  
[Gestire gli amministratori di server](analysis-services-server-admins.md)  
[Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)