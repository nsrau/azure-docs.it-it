---
title: Autorizzare l'accesso a BLOB e code usando Active DirectoryAuthorize access to blobs and queues using Active Directory
titleSuffix: Azure Storage
description: Autorizzare l'accesso ai BLOB e alle code di Azure usando Azure Active Directory.Authorize access to Azure blobs and queues using Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255366"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autorizzare l'accesso a BLOB e code usando Azure Active DirectoryAuthorize access to blobs and queues using Azure Active Directory

Archiviazione di Azure supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste all'archiviazione BLOB e code. Con Azure AD è possibile usare il controllo degli accessi in base al ruolo per concedere autorizzazioni a un'entità di sicurezza, che può essere un'entità servizio utente, un gruppo o un'applicazione. L'entità di sicurezza viene autenticata da Azure AD per restituire un token OAuth 2.0.The security principal is authenticated by Azure AD to return an OAuth 2.0 token. Il token può quindi essere usato per autorizzare una richiesta sull'archiviazione BLOB o coda.

L'autorizzazione delle richieste su Archiviazione di Azure con Azure AD offre una sicurezza e un utilizzo di qualità superiore rispetto all'autorizzazione con chiave condivisa. Microsoft consiglia di usare l'autorizzazione di Azure AD con le applicazioni BLOB e coda quando possibile per ridurre al minimo le potenziali vulnerabilità di sicurezza inerenti alla chiave condivisa.

L'autorizzazione con Azure AD è disponibile per tutti gli account di archiviazione BLOB e generici in tutte le aree pubbliche e nei cloud nazionali. Solo gli account di archiviazione creati con il modello di distribuzione di Azure Resource Manager supportano l'autorizzazione di Azure AD.

L'archiviazione BLOB supporta inoltre la creazione di firme di accesso condiviso firmate con credenziali di Azure AD. Per ulteriori informazioni, consultate [Concedere un accesso limitato ai dati con firme](storage-sas-overview.md)di accesso condiviso.

File di Azure supporta l'autorizzazione con ACTIVE Directory (anteprima) o Azure AD DS (GA) tramite SMB solo per le macchine virtuali aggiunte al dominio. Per informazioni sull'uso di Active Directory (anteprima) o Di Azure AD DS (GA) su SMB per i file di Azure, vedere [Panoramica del supporto dell'autenticazione basata sull'identità di File di Azure per l'accesso SMB.](../files/storage-files-active-directory-overview.md)

L'autorizzazione con Azure AD non è supportata per l'archiviazione tabelle di Azure.Authorization with Azure AD is not supported for Azure Table storage. Usare Chiave condivisa per autorizzare le richieste all'archiviazione tabelle.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Panoramica di Azure AD per BLOB e codeOverview of Azure AD for blobs and queues

Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere a un BLOB o a una risorsa di coda, la richiesta deve essere autorizzata, a meno che non sia un BLOB disponibile per l'accesso anonimo. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2.0.First, the security principal's identity is authenticated and an OAuth 2.0 token is returned. Successivamente, il token viene passato come parte di una richiesta al servizio BLOB o coda e utilizzato dal servizio per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che un'applicazione richieda un token di accesso OAuth 2.0 in fase di esecuzione. Se un'applicazione viene eseguita dall'interno di un'entità di Azure, ad esempio una macchina virtuale di Azure, un set di scalabilità di macchine virtuali o un'app Funzioni di Azure, può usare [un'identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) per accedere a BLOB o code. Per informazioni su come autorizzare le richieste effettuate da un'identità gestita al servizio BLOB o code di Azure, vedere [Autorizzare l'accesso a BLOB e code con Azure Active Directory e le identità gestite per](storage-auth-aad-msi.md)le risorse di Azure.

Il passaggio di autorizzazione richiede l'assegnazione di uno o più ruoli RBAC all'entità di sicurezza. Archiviazione di Azure offre ruoli RBAC che includono set comuni di autorizzazioni per i dati BLOB e coda. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni di cui l'entità disticherà. Per altre informazioni sull'assegnazione di ruoli RBAC per Archiviazione di Azure, vedere Gestire i diritti di [accesso ai dati di archiviazione con il controllo degli accessi in base](storage-auth-aad-rbac.md)al ruolo.

Le applicazioni native e le applicazioni Web che effettuano richieste al servizio BLOB o coda di Azure possono anche autorizzare l'accesso con Azure AD. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste di dati BLOB o coda, vedere [Autorizzare l'accesso ad Archiviazione di Azure con Azure AD da un'applicazione di Archiviazione di Azure.To](storage-auth-aad-app.md)learn how to request an access token and use it to authorize requests for blob or queue data, see Authorize access to Azure Storage with Azure AD from an Azure Storage application.

## <a name="assign-rbac-roles-for-access-rights"></a>Assegnare ruoli RBAC per i diritti di accessoAssign RBAC roles for access rights

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che includono set comuni di autorizzazioni usate per accedere ai dati BLOB e coda. È anche possibile definire ruoli personalizzati per l'accesso ai dati BLOB e coda.

Quando un ruolo Controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione o [un'identità gestita per](../../active-directory/managed-identities-azure-resources/overview.md)le risorse di Azure.An Azure AD security principal may be a user, a group, an application service principal, or a managed identity for Azure resources.

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Ruoli RBAC incorporati per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Per informazioni su come assegnare un ruolo RBAC incorporato a un'entità di sicurezza, vedere uno degli articoli seguenti:To learn how to assign a built-in RBAC role to a security principal, see one of the following articles:

- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo nel portale di Azure](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite l'interfaccia della riga di comando di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite PowerShell](storage-auth-aad-rbac-powershell.md)

Per altre informazioni sul modo in cui vengono definiti i ruoli predefiniti per Archiviazione di Azure, vedere [Informazioni sulle definizioni di ruolo](../../role-based-access-control/role-definitions.md#management-and-data-operations). Per informazioni sulla creazione di ruoli RBAC personalizzati, vedere Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure.For information about creating custom RBAC roles, see [Create custom roles for Azure Role-Based Access Control](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Autorizzazioni di accesso per le operazioni sui dati

Per informazioni dettagliate sulle autorizzazioni necessarie per chiamare operazioni specifiche del servizio BLOB o di coda, vedere Autorizzazioni per la chiamata di operazioni sui [dati BLOB e coda.](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

## <a name="resource-scope"></a>Ambito della risorsa

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Accedere ai dati con un account Azure ADAccess data with an Azure AD account

L'accesso ai dati BLOB o coda tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure può essere autorizzato usando l'account Azure AD dell'utente o usando le chiavi di accesso dell'account (autorizzazione della chiave condivisa).

### <a name="data-access-from-the-azure-portal"></a>Accesso ai dati dal portale di AzureData access from the Azure portal

Il portale di Azure può usare l'account Azure AD o le chiavi di accesso all'account per accedere ai dati BLOB e coda in un account di archiviazione di Azure.The Azure portal can use either your Azure AD account or the account access keys to access blob and queue data in an Azure storage account. Lo schema di autorizzazione utilizzato dal portale di Azure dipende dai ruoli RBAC assegnati all'utente.

Quando si tenta di accedere ai dati BLOB o coda, il portale di Azure controlla innanzitutto se è stato assegnato un ruolo RBAC con **Microsoft.Storage/storageAccounts/listkeys/action**. Se è stato assegnato un ruolo con questa azione, il portale di Azure usa la chiave dell'account per accedere ai dati BLOB e coda tramite l'autorizzazione chiave condivisa. Se non è stato assegnato un ruolo con questa azione, il portale di Azure tenta di accedere ai dati usando l'account Azure AD.

Per accedere ai dati BLOB o coda dal portale di Azure usando l'account Azure AD, sono necessarie le autorizzazioni per accedere ai dati blob e coda e sono necessarie anche le autorizzazioni per spostarsi tra le risorse dell'account di archiviazione nel portale di Azure.To access blob or queue data from the Azure portal using your Azure AD account, you need permissions to access blob and queue data, and you also need permissions to navigate through the storage account resources in the Azure portal. I ruoli predefiniti forniti da Archiviazione di Azure concedono l'accesso alle risorse BLOB e queue, ma non concedono autorizzazioni alle risorse dell'account di archiviazione. Per questo motivo, l'accesso al portale richiede anche l'assegnazione di un ruolo di Azure Resource Manager, ad esempio il ruolo [Lettore,](../../role-based-access-control/built-in-roles.md#reader) con ambito al livello dell'account di archiviazione o superiore. Il ruolo **Lettore** concede le autorizzazioni più limitate, ma è accettabile anche un altro ruolo di Azure Resource Manager che concede l'accesso alle risorse di gestione degli account di archiviazione. Per altre informazioni su come assegnare autorizzazioni agli utenti per l'accesso ai dati nel portale di Azure con un account Azure AD, vedere [Concedere l'accesso ai dati BLOB e coda](storage-auth-aad-rbac-portal.md)di Azure con il controllo degli accessi in base al ruolo nel portale di Azure.To learn more about how to assign permissions to users for data access in the Azure portal with an Azure AD account, see Grant access to Azure blob and queue data with RBAC in the Azure portal.

Il portale di Azure indica lo schema di autorizzazione in uso quando si passa a un contenitore o a una coda. Per altre informazioni sull'accesso ai dati nel portale, vedere Usare il portale di [Azure per accedere ai dati BLOB o di coda.](storage-access-blobs-queues-portal.md)

### <a name="data-access-from-powershell-or-azure-cli"></a>Accesso ai dati da PowerShell o dall'interfaccia della riga di comando di AzureData access from PowerShell or Azure CLI

L'interfaccia della riga di comando di Azure e PowerShell supportano l'accesso con le credenziali di Azure AD. Dopo l'accesso, la sessione viene eseguita con tali credenziali. Per altre informazioni, vedere [Eseguire comandi dell'interfaccia della riga di comando o di PowerShell](authorize-active-directory-powershell.md)di Azure con credenziali di Azure AD per accedere ai dati BLOB o della coda.

## <a name="next-steps"></a>Passaggi successivi

- [Autorizzare l'accesso a BLOB e code con Azure Active Directory e le identità gestite per le risorse di AzureAuthorize access to blobs and queues with Azure Active Directory and managed identities for Azure Resources](storage-auth-aad-msi.md)
- [Autorizzare con Azure Active Directory da un'applicazione per l'accesso a BLOB e codeAuthorize with Azure Active Directory from an application for access to blobs and queues](storage-auth-aad-app.md)
- [Supporto di Archiviazione di Azure per il controllo degli accessi in base ad Azure Active Directory disponibile](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
