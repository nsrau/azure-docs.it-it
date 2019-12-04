---
title: Autenticazione Active Directory-database di Azure per PostgreSQL-server singolo
description: Informazioni sui concetti di Azure Active Directory per l'autenticazione con database di Azure per PostgreSQL-server singolo
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ec853657d6dd1f3b019d8a414cfa28edc1083b29
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769915"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Usare Azure Active Directory per l'autenticazione con PostgreSQL

L'autenticazione Microsoft Azure Active Directory (Azure AD) è un meccanismo di connessione al database di Azure per PostgreSQL usando le identità definite in Azure AD.
Con Azure AD autenticazione, è possibile gestire le identità degli utenti del database e altri servizi Microsoft in una posizione centrale, semplificando la gestione delle autorizzazioni.

> [!IMPORTANT]
> L'autenticazione Azure AD per database di Azure per PostgreSQL è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I vantaggi dell'utilizzo di Azure AD includono:

- Autenticazione degli utenti tra i servizi di Azure in modo uniforme
- Gestione dei criteri password e della rotazione delle password in un'unica posizione
- Più forme di autenticazione supportate da Azure Active Directory, che possono eliminare la necessità di archiviare le password
- I clienti possono gestire le autorizzazioni del database usando gruppi (Azure AD) esterni.
- Azure AD autenticazione usa i ruoli del database PostgreSQL per autenticare le identità a livello di database
- Supporto dell'autenticazione basata su token per le applicazioni che si connettono al database di Azure per PostgreSQL

Per configurare e usare l'autenticazione Azure Active Directory, usare il processo seguente:

1. Creare e popolare Azure Active Directory con le identità utente in base alle esigenze.
2. Facoltativamente, associare o modificare il Active Directory attualmente associato alla sottoscrizione di Azure.
3. Creare un amministratore di Azure AD per il database di Azure per il server PostgreSQL.
4. Creare gli utenti del database nel database mappato alle identità Azure AD.
5. Connettersi al database recuperando un token per un'identità Azure AD ed eseguendo l'accesso.

> [!NOTE]
> Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con database di Azure per PostgreSQL, vedere [configurare e accedere con Azure ad per database di Azure per PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="architecture"></a>Architecture

Il diagramma generale seguente riepiloga il funzionamento dell'autenticazione con Azure AD l'autenticazione con database di Azure per PostgreSQL. Le frecce indicano i percorsi di comunicazione.

![flusso di autenticazione][1]

## <a name="administrator-structure"></a>Struttura dell'account amministratore

Quando si usa l'autenticazione di Azure AD, sono disponibili due account amministratore per il server PostgreSQL. l'amministratore di PostgreSQL originale e l'amministratore Azure AD. Solo l'amministratore basato su un account Azure AD può creare il primo utente di database indipendente di Azure AD in un database utente. L'account di accesso dell'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Quando l'amministratore è un account di gruppo, può essere usato da qualsiasi membro del gruppo, abilitando più Azure AD amministratori per il server PostgreSQL. L'uso di un account di gruppo come amministratore migliora la gestibilità consentendo di aggiungere e rimuovere in modo centralizzato i membri del gruppo in Azure AD senza modificare gli utenti o le autorizzazioni nel server PostgreSQL. È possibile configurare un solo amministratore di Azure AD (utente o gruppo) alla volta.

![struttura di amministrazione][2]

## <a name="permissions"></a>autorizzazioni

Per creare nuovi utenti in grado di eseguire l'autenticazione con Azure AD, è necessario disporre del ruolo `azure_ad_admin` nel database. Questo ruolo viene assegnato configurando l'account amministratore Azure AD per uno specifico server di database di Azure per PostgreSQL.

Per creare un nuovo utente Azure AD database, è necessario connettersi come amministratore Azure AD. Questa operazione è illustrata in [configurare e accedere con Azure ad per database di Azure per PostgreSQL](howto-configure-sign-in-aad-authentication.md).

Qualsiasi autenticazione Azure AD è possibile solo se l'amministratore del Azure AD è stato creato per database di Azure per PostgreSQL. Se il Azure Active Directory amministratore è stato rimosso dal server, gli utenti di Azure Active Directory esistenti creati in precedenza non possono più connettersi al database usando le credenziali di Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Connettersi usando le identità di Azure AD

L'autenticazione di Azure Active Directory supporta i metodi seguenti per la connessione a un database con le identità di Azure AD:

- Password di Azure Active Directory
- Autenticazione integrata di Azure Active Directory
- Autenticazione universale di Azure Active Directory con MFA
- Uso di Active Directory certificati dell'applicazione o dei segreti client

Una volta eseguita l'autenticazione con il Active Directory, viene recuperato un token. Questo token è la password per l'accesso.

> [!NOTE]
> Per altri dettagli su come connettersi con un token di Active Directory, vedere [configurare e accedere con Azure ad per database di Azure per PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Considerazione aggiuntive

- Per migliorare la gestibilità, è consigliabile effettuare il provisioning di un gruppo di Azure AD dedicato come amministratore.
- È possibile configurare un solo amministratore di Azure AD (utente o gruppo) per un server di database di Azure per PostgreSQL in qualsiasi momento.
- Solo un amministratore Azure AD per PostgreSQL può connettersi inizialmente al database di Azure per PostgreSQL usando un account di Azure Active Directory. L'amministratore di Active Directory può configurare gli utenti del database di Azure AD successivi.
- Se un utente viene eliminato da Azure AD, l'utente non sarà più in grado di eseguire l'autenticazione con Azure AD e pertanto non sarà più possibile acquisire un token di accesso per tale utente. In questo caso, anche se il ruolo corrispondente sarà ancora nel database, non sarà possibile connettersi al server con tale ruolo.
> [!NOTE]
> L'account di accesso con l'utente Azure AD eliminato può ancora essere eseguito fino alla scadenza del token (fino a 60 minuti dall'emissione di token).  Se si rimuove anche l'utente da database di Azure per PostgreSQL, questo accesso verrà revocato immediatamente.
- Se il Azure AD amministratore viene rimosso dal server, il server non sarà più associato a un tenant di Azure AD e pertanto tutti gli account di accesso Azure AD verranno disabilitati per il server. L'aggiunta di un nuovo amministratore di Azure AD dallo stesso tenant riabilita Azure AD account di accesso.
- Il database di Azure per PostgreSQL corrisponde ai token di accesso al ruolo database di Azure per PostgreSQL usando l'ID utente univoco Azure AD dell'utente, invece di usare il nome utente. Ciò significa che se un utente Azure AD viene eliminato in Azure AD e un nuovo utente creato con lo stesso nome, database di Azure per PostgreSQL considera un utente diverso. Se pertanto un utente viene eliminato da Azure AD e successivamente viene aggiunto un nuovo utente con lo stesso nome, il nuovo utente non sarà in grado di connettersi al ruolo esistente. A tale proposito, l'amministratore di database di Azure per PostgreSQL Azure AD necessario revocare e quindi concedere al ruolo "azure_ad_user" all'utente di aggiornare l'ID utente di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con database di Azure per PostgreSQL, vedere [configurare e accedere con Azure ad per database di Azure per PostgreSQL](howto-configure-sign-in-aad-authentication.md).
- Per una panoramica di account di accesso, utenti e ruoli del database di Azure per PostgreSQL, vedere [creare utenti in database di Azure per PostgreSQL-server singolo](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
