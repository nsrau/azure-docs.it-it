---
title: Autenticazione di Active Directory - Database di Azure per PostgreSQL - Server singolo
description: Informazioni sui concetti di Azure Active Directory per l'autenticazione con Azure Database for PostgreSQL - Single Server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ec853657d6dd1f3b019d8a414cfa28edc1083b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769915"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Usare Azure Active Directory per l'autenticazione con PostgreSQLUse Azure Active Directory for authenticating with PostgreSQL

L'autenticazione di Microsoft Azure Active Directory (Azure AD) è un meccanismo di connessione al database di Azure per PostgreSQL usando le identità definite in Azure AD.
Con l'autenticazione di Azure AD, è possibile gestire le identità utente del database e altri servizi Microsoft in una posizione centrale, semplificando la gestione delle autorizzazioni.

> [!IMPORTANT]
> L'autenticazione di Azure AD per il database di Azure per PostgreSQL è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I vantaggi dell'uso di Azure AD includono:Benefits of using Azure AD include:

- Autenticazione degli utenti tra i servizi di Azure in modo uniforme
- Gestione dei criteri password e rotazione delle password in un'unica posizione
- Più forme di autenticazione supportate da Azure Active Directory, che possono eliminare la necessità di archiviare le passwordMultiple forms of authentication supported by Azure Active Directory, which can eliminate the need to store passwords
- I clienti possono gestire le autorizzazioni del database usando gruppi (Azure AD) esterni.
- L'autenticazione di Azure AD usa i ruoli del database PostgreSQL per autenticare le identità a livello di databaseAzure AD authentication uses PostgreSQL database roles to authenticate identities at the database level
- Supporto dell'autenticazione basata su token per le applicazioni che si connettono al database di Azure per PostgreSQLSupport of token-based authentication for applications connecting to Azure Database for PostgreSQL

Per configurare e usare l'autenticazione di Azure Active Directory, usare il processo seguente:To configure and use Azure Active Directory authentication, use the following process:

1. Creare e popolare Azure Active Directory con le identità utente in base alle esigenze.
2. Facoltativamente, associare o modificare Active Directory attualmente associato alla sottoscrizione di Azure.Optionally associate or change the Active Directory currently associated with your Azure subscription.
3. Creare un amministratore di Azure AD per il database di Azure per il server PostgreSQL.Create an Azure AD administrator for the Azure Database for PostgreSQL server.
4. Creare utenti di database nel database mappati alle identità di Azure AD.
5. Connettersi al database recuperando un token per un'identità di Azure AD e eseguendo l'accesso.

> [!NOTE]
> Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con Il database di Azure per PostgreSQL, vedere [Configurare e accedere con Azure AD per Azure Database di Azure per PostgreSQL.](howto-configure-sign-in-aad-authentication.md)

## <a name="architecture"></a>Architecture

Il diagramma di alto livello seguente riepiloga il funzionamento dell'autenticazione con l'autenticazione di Azure AD con il database di Azure per PostgreSQL.The following high-level diagram summarizes how authentication works using Azure AD authentication with Azure Database for PostgreSQL. Le frecce indicano i percorsi di comunicazione.

![flusso di autenticazione][1]

## <a name="administrator-structure"></a>Struttura dell'account amministratore

Quando si usa l'autenticazione di Azure AD, sono disponibili due account amministratore per il server PostgreSQL. l'amministratore PostgreSQL originale e l'amministratore di Azure AD. Solo l'amministratore basato su un account Azure AD può creare il primo utente di database indipendente di Azure AD in un database utente. L'account di accesso dell'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Quando l'amministratore è un account di gruppo, può essere usato da qualsiasi membro del gruppo, abilitando più amministratori di Azure AD per il server PostgreSQL.When the administrator is a group account, it can be used by any group member, enabling multiple Azure AD administrators for the PostgreSQL server. L'uso di un account di gruppo come amministratore migliora la gestibilità consentendo di aggiungere e rimuovere centralmente i membri del gruppo in Azure AD senza modificare gli utenti o le autorizzazioni nel server PostgreSQL.Using a group account as an administrator enhances ability by allowing you to centrally add and remove group members in Azure AD without changing the users or permissions in the PostgreSQL server. È possibile configurare un solo amministratore di Azure AD (utente o gruppo) alla volta.

![struttura di amministrazione][2]

## <a name="permissions"></a>Autorizzazioni

Per creare nuovi utenti in grado di eseguire `azure_ad_admin` l'autenticazione con Azure AD, è necessario disporre del ruolo nel database. Questo ruolo viene assegnato configurando l'account amministratore di Azure AD per un database di Azure specifico per il server PostgreSQL.This role is assigned by configuring the Azure AD Administrator account for a specific Azure Database for PostgreSQL server.

Per creare un nuovo utente del database di Azure AD, è necessario connettersi come amministratore di Azure AD. Ciò è illustrato in [Configure and Login with Azure AD for Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

Qualsiasi autenticazione di Azure AD è possibile solo se l'amministratore di Azure AD è stato creato per il database di Azure per PostgreSQL.Any Azure AD authentication is only possible if the Azure AD admin was created for Azure Database for PostgreSQL. Se l'amministratore di Azure Active Directory è stato rimosso dal server, gli utenti di Azure Active Directory creati in precedenza non possono più connettersi al database usando le credenziali di Azure Active Directory.If the Azure Active Directory admin was removed from the server, existing Azure Active Directory users created previously can no longer connect to the database using their Azure Active Directory credentials.

## <a name="connecting-using-azure-ad-identities"></a>Connettersi usando le identità di Azure AD

L'autenticazione di Azure Active Directory supporta i metodi seguenti per la connessione a un database con le identità di Azure AD:

- Password di Azure Active Directory
- Autenticazione integrata di Azure Active Directory
- Autenticazione universale di Azure Active Directory con MFA
- Utilizzo di certificati dell'applicazione Active Directory o segreti client

Dopo aver eseguito l'autenticazione in Active Directory, recuperare un token. Questo token è la password per l'accesso.

> [!NOTE]
> Per altre informazioni su come connettersi a un token di Active Directory, vedere [Configurare e accedere con Azure AD per il database di Azure per PostgreSQL.](howto-configure-sign-in-aad-authentication.md)

## <a name="additional-considerations"></a>Altre considerazioni

- Per migliorare la gestibilità, è consigliabile effettuare il provisioning di un gruppo di Azure AD dedicato come amministratore.
- È possibile configurare un solo amministratore di Azure AD (un utente o un gruppo) per un database di Azure per il server PostgreSQL in qualsiasi momento.
- Solo un amministratore di Azure AD per PostgreSQL può inizialmente connettersi al database di Azure per PostgreSQL usando un account Azure Active Directory.Only an Azure AD administrator for PostgreSQL can initially connect to the Azure Database for PostgreSQL using an Azure Active Directory account. L'amministratore di Active Directory può configurare gli utenti del database di Azure AD successivi.
- Se un utente viene eliminato da Azure AD, tale utente non sarà più in grado di eseguire l'autenticazione con Azure AD e pertanto non sarà più possibile acquisire un token di accesso per tale utente. In questo caso, anche se il ruolo corrispondente sarà ancora nel database, non sarà possibile connettersi al server con tale ruolo.
> [!NOTE]
> L'accesso con l'utente di Azure AD eliminato può comunque essere eseguito fino alla scadenza del token (fino a 60 minuti dall'emissione di token).  Se si rimuove anche l'utente dal database di Azure per PostgreSQL, questo accesso verrà revocato immediatamente.
- Se l'amministratore di Azure AD viene rimosso dal server, il server non sarà più associato a un tenant di Azure AD e pertanto tutti gli account di accesso di Azure AD verranno disabilitati per il server. L'aggiunta di un nuovo amministratore di Azure AD dallo stesso tenant riabiliterà gli account di accesso di Azure AD.
- Il database di Azure per PostgreSQL associa i token di accesso al ruolo Database di Azure per PostgreSQL usando l'ID utente univoco di Azure AD dell'utente, anziché usare il nome utente. Ciò significa che se un utente di Azure AD viene eliminato in Azure AD e un nuovo utente creato con lo stesso nome, Database di Azure per PostgreSQL considera che un utente diverso. Pertanto, se un utente viene eliminato da Azure AD e quindi è stato aggiunto un nuovo utente con lo stesso nome, il nuovo utente non sarà in grado di connettersi al ruolo esistente. A tale condizione, l'amministratore del database di Azure per PostgreSQL Azure AD deve revocare e quindi concedere il ruolo "azure_ad_user" all'utente per aggiornare l'ID utente di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con Il database di Azure per PostgreSQL, vedere [Configurare e accedere con Azure AD per Azure Database di Azure per PostgreSQL.](howto-configure-sign-in-aad-authentication.md)
- Per una panoramica degli account di accesso, degli utenti e dei ruoli del database Database di Azure per PostgreSQL, vedere Creare utenti nel database di [Azure per PostgreSQL - Singolo server.](howto-create-users.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
