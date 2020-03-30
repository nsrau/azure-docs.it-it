---
title: Gestire le risorse con Microsoft Graph
titleSuffix: Azure AD B2C
description: Preparare la gestione delle risorse B2C di Azure AD con Microsoft Graph registrando un'applicazione a cui sono state concesse le autorizzazioni API Graph necessarie.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184350"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Gestire Azure AD B2C con Microsoft GraphManage Azure AD B2C with Microsoft Graph

[Microsoft Graph][ms-graph] consente di gestire molte delle risorse all'interno del tenant B2C di Azure AD, inclusi gli account utente dei clienti e i criteri personalizzati. Scrivendo script o applicazioni che chiamano [l'API Microsoft Graph][ms-graph-api], è possibile automatizzare le attività di gestione tenant come:

* Eseguire la migrazione di un archivio utente esistente a un tenant B2C di Azure ADMigrate an existing user store to an Azure AD B2C tenant
* Distribuire criteri personalizzati con una pipeline di Azure in DevOps di Azure e gestire chiavi di criteri personalizzatiDeploy custom policies with an Azure Pipeline in Azure DevOps, and manage custom policy keys
* Registrazione dell'utente host nella propria pagina e creazione di account utente nella directory B2C di Azure AD
* Automatizzare la registrazione delle applicazioni
* Ottenere i registri di controlloObtain audit logs

Le sezioni seguenti consentono di prepararsi all'uso dell'API Microsoft Graph per automatizzare la gestione delle risorse nella directory B2C di Azure AD.

## <a name="microsoft-graph-api-interaction-modes"></a>Modalità di interazione dell'API Microsoft Graph

Esistono due modalità di comunicazione che è possibile usare quando si usa l'API Microsoft Graph per gestire le risorse nel tenant B2C di Azure AD:There are two modes of communication you can use when working with the Microsoft Graph API to manage resources in your Azure AD B2C tenant:

* **Interattivo:** appropriato per le attività run-once, si usa un account amministratore nel tenant B2C per eseguire le attività di gestione. Questa modalità richiede che un amministratore acceda utilizzando le proprie credenziali prima di chiamare l'API Microsoft Graph.This mode requires an administrator to sign in using their credentials before calling the Microsoft Graph API.

* **Automatizzato:** per le attività pianificate o eseguite continuamente, questo metodo utilizza un account di servizio configurato con le autorizzazioni necessarie per eseguire le attività di gestione. Per creare l'"account del servizio" in Azure AD B2C, registrare un'applicazione utilizzata dalle applicazioni e dagli script per l'autenticazione tramite *l'ID applicazione (client)* e la concessione delle credenziali client OAuth 2.0. In questo caso, l'applicazione agisce come se stessa per chiamare l'API Microsoft Graph, non l'utente amministratore come nel metodo interattivo descritto in precedenza.

Per abilitare lo scenario di interazione **automatizzata,** creare una registrazione dell'applicazione illustrata nelle sezioni seguenti.

## <a name="register-management-application"></a>Registrare l'applicazione di gestione

Prima che gli script e le applicazioni [possano][ms-graph-api] interagire con l'API Microsoft Graph per gestire le risorse di Azure AD B2C, è necessario creare una registrazione dell'applicazione nel tenant B2C di Azure AD che conceda le autorizzazioni API necessarie.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>Concedere l'accesso all'API

Successivamente, concedere all'applicazione registrata le autorizzazioni per modificare le risorse tenant tramite chiamate all'API Microsoft Graph.Next, grant the registered application permissions to manipulate tenant resources through calls to the Microsoft Graph API.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Creare un segreto clientCreate client secret

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Si dispone ora di un'applicazione che dispone dell'autorizzazione per *creare,* *leggere,* *aggiornare*ed *eliminare* utenti nel tenant B2C di Azure AD. Passare alla sezione successiva per aggiungere le autorizzazioni di *aggiornamento password.*

## <a name="enable-user-delete-and-password-update"></a>Abilitare l'eliminazione dell'utente e l'aggiornamento della passwordEnable user delete and password update

L'autorizzazione *Lettura e scrittura dati directory* **NON** include la possibilità di eliminare gli utenti o aggiornare le password degli account utente.

Se l'applicazione o lo script deve eliminare utenti o aggiornare le password, assegnare il ruolo *di amministratore utente* all'applicazione:

1. Accedere al [portale](https://portal.azure.com) di Azure e usare il filtro **Directory e sottoscrizione** per passare al tenant B2C di Azure AD.
1. Cercare e selezionare **Azure AD B2C**.
1. In **Gestisci**selezionare **Ruoli e amministratori**.
1. Selezionare il ruolo **amministratore utente.**
1. Selezionare **Aggiungi assegnazioni**.
1. Nella casella di testo **Seleziona** immettere il nome dell'applicazione registrata in precedenza, ad esempio *managementapp1*. Selezionare l'applicazione quando viene visualizzata nei risultati della ricerca.
1. Selezionare **Aggiungi**. La propagazione completa delle autorizzazioni potrebbe richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver registrato l'applicazione di gestione e sono state concesse le autorizzazioni necessarie, le applicazioni e i servizi( ad esempio, le pipeline di Azure) possono usare le credenziali e le autorizzazioni per interagire con l'API Microsoft Graph.Now that you've registered your management application and have granted it the required permissions, your applications and services (for example, Azure Pipelines) can use its credentials and permissions to interact with the Microsoft Graph API.

* [Operazioni B2C supportate da Microsoft Graph](microsoft-graph-operations.md)
* [Gestire gli account utente di Azure AD B2C con Microsoft GraphManage Azure AD B2C user accounts with Microsoft Graph](manage-user-accounts-graph-api.md)
* [Ottenere i log di controllo con l'API di creazione di report di Azure ADGet audit logs with the Azure AD reporting API](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
