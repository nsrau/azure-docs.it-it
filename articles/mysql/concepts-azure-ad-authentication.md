---
title: Autenticazione di Active Directory - Database di Azure per MySQL
description: Informazioni sui concetti di Azure Active Directory per l'autenticazione con il database di Azure per MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0c56652b4e098213bae0c69d4be5ac77e1b1393c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83635301"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Usare Azure Active Directory per l'autenticazione con MySQL

L'autenticazione di Microsoft Azure Active Directory (Azure AD) è un meccanismo di connessione al database di Azure per MySQL tramite identità definite in Azure AD.
Con l'autenticazione di Azure AD è possibile gestire centralmente le identità degli utenti del database e di altri servizi Microsoft semplificando la gestione delle autorizzazioni.

I vantaggi dell'uso di Azure AD includono:

- Autenticazione degli utenti nei servizi di Azure in modo uniforme
- Gestione dei criteri delle password e della rotazione delle password in un'unica posizione
- Più forme di autenticazione supportate da Azure Active Directory eliminando la necessità di archiviare le password
- I clienti possono gestire le autorizzazioni del database usando gruppi (Azure AD) esterni.
- L'autenticazione di Azure SD usa gli utenti di un database MySQL per autenticare le identità a livello di database
- Supporto dell'autenticazione basata su token per le applicazioni che si connettono al database di Azure per MySQL

Per configurare e usare l'autenticazione di Azure Active Directory, usare la procedura seguente:

1. Creare e popolare Azure Active Directory con le identità utente in base alle esigenze.
2. Associare o modificare facoltativamente l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure.
3. Creare un amministratore di Azure AD per il server di database di Azure per MySQL.
4. Creare gli utenti di database nel database di cui è stato eseguito il mapping alle identità di Azure AD.
5. Connettersi al database recuperando un token per un'identità di Azure AD ed eseguendo l'accesso.

> [!NOTE]
> Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con il database di Azure per MySQL, vedere [Configurare e accedere con Azure AD per il database di Azure per MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Architecture

Il diagramma generale seguente riepiloga il funzionamento dell'autenticazione con l'autenticazione di Azure AD con il database di Azure per MySQL. Le frecce indicano i percorsi di comunicazione.

![flusso di autenticazione][1]

## <a name="administrator-structure"></a>Struttura dell'account amministratore

Quando si usa l'autenticazione di Azure AD, sono disponibili due account amministratore per il server MySQL, l'amministratore MySQL originale e l'amministratore di Azure AD. Solo l'amministratore basato su un account Azure AD può creare il primo utente di database indipendente di Azure AD in un database utente. L'account di accesso dell'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Quando l'amministratore è un account di gruppo, può essere usato da qualsiasi membro del gruppo, abilitando quindi più amministratori di Azure AD per il server MySQL. L'uso dell'account di gruppo come amministratore migliora la gestibilità, perché consente di aggiungere e rimuovere a livello centrale i membri del gruppo in Azure AD senza apportare modifiche a utenti o autorizzazioni nel server MySQL. È possibile configurare un solo amministratore di Azure AD (utente o gruppo) alla volta.

![struttura di amministrazione][2]

## <a name="permissions"></a>Autorizzazioni

Per creare nuovi utenti che possono eseguire l'autenticazione con Azure AD, è necessario essere l'amministratore Azure AD designato. Questo utente viene assegnato configurando l'account amministratore di Azure AD per uno specifico server di database di Azure per MySQL.

Per creare un nuovo utente del database di Azure AD, è necessario connettersi come amministratore di Azure AD. Questa operazione è illustrata in [Configurare e accedere con Azure AD per il database di Azure per MySQL](howto-configure-sign-in-azure-ad-authentication.md).

L'autenticazione di Azure AD è possibile unicamente se l'amministratore di Azure AD è stato creato per il database di Azure per MySQL. Se l'amministratore di Azure Active Directory è stato rimosso dal server, gli utenti di Azure Active Directory esistenti creati in precedenza non possono più connettersi al database con le credenziali di Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Connettersi usando le identità di Azure AD

L'autenticazione di Azure Active Directory supporta i metodi seguenti per la connessione a un database con le identità di Azure AD:

- Password di Azure Active Directory
- Autenticazione integrata di Azure Active Directory
- Autenticazione universale di Azure Active Directory con MFA
- Uso di certificati dell'applicazione o segreti client di Active Directory

Dopo avere eseguito l'autenticazione con Active Directory, è possibile recuperare un token. Questo token è la password per l'accesso.

> [!NOTE]
> Per altri dettagli su come connettersi con un token di Active Directory, vedere [Configurare e accedere con Azure AD per il database di Azure per MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Altre considerazioni

- È possibile configurare un solo amministratore di Azure AD per un server di database di Azure per MySQL in qualsiasi momento.
- Inizialmente solo un amministratore di Azure AD per MySQL può connettersi al database di Azure per MySQL con un account Azure Active Directory. L'amministratore di Active Directory può configurare gli utenti del database di Azure AD successivi.
- Se un utente viene eliminato da Azure AD, l'utente non sarà più in grado di eseguire l'autenticazione con Azure AD e pertanto non sarà più possibile acquisire un token di accesso per tale utente. In questo caso, anche se l'utente corrispondente sarà ancora nel database, non sarà possibile connettersi al server con tale utente.
> [!NOTE]
> L'account di accesso con l'utente di Azure AD eliminato può ancora essere eseguito fino alla scadenza del token (fino a 60 minuti dall'emissione del token).  Se si rimuove l'utente anche dal database di Azure per MySQL, questo accesso verrà revocato immediatamente.
- Se l'amministratore di Azure AD viene rimosso dal server, il server non sarà più associato a un tenant di Azure AD e pertanto tutti gli account di accesso di Azure AD verranno disabilitati per il server. L'aggiunta di un nuovo amministratore di Azure AD dallo stesso tenant riabilita gli account di accesso di Azure AD.
- Il database di Azure per MySQL associa i token di accesso al ruolo del database di Azure per l'utente MySQL usando l'ID utente univoco di Azure AD dell'utente invece del nome utente. Ciò significa che se un utente di Azure AD viene eliminato in Azure AD e viene creato un nuovo utente con lo stesso nome, il database di Azure per MySQL lo considera un utente diverso. Se pertanto un utente viene eliminato da Azure AD e successivamente viene aggiunto un nuovo utente con lo stesso nome, il nuovo utente non potrà connettersi con l'utente esistente.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con il database di Azure per MySQL, vedere [Configurare e accedere con Azure AD per il database di Azure per MySQL](howto-configure-sign-in-azure-ad-authentication.md).
- Per una panoramica degli account di accesso e degli utenti di database per Database di Azure per MySQL, vedere [creare utenti in Database di Azure per MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
