---
title: Autorizzare l'accesso a BLOB e code usando Active Directory
titleSuffix: Azure Storage
description: Autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3b29c5b4b1bf682e0f43a0bf9b0c8fcb40d70433
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827878"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autorizzare l'accesso a BLOB e code usando Azure Active Directory

Archiviazione di Azure supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste all'archiviazione BLOB e di Accodamento. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente, un gruppo o un'entità servizio dell'applicazione. L'entità di sicurezza viene autenticata da Azure AD per restituire un token OAuth 2,0. Il token può quindi essere usato per autorizzare una richiesta per l'archiviazione di BLOB o code.

L'autorizzazione delle richieste per l'archiviazione di Azure con Azure AD offre sicurezza e facilità d'uso superiori rispetto all'autorizzazione della chiave condivisa. Microsoft consiglia di usare Azure AD autorizzazione con le applicazioni BLOB e di Accodamento quando possibile per ridurre al minimo le potenziali vulnerabilità di sicurezza inerenti alla chiave condivisa.

L'autorizzazione con Azure AD è disponibile per tutti gli account di archiviazione BLOB e per utilizzo generico in tutte le aree pubbliche e nei cloud nazionali. Solo gli account di archiviazione creati con il modello di distribuzione Azure Resource Manager supportano Azure AD autorizzazione.

L'archiviazione BLOB supporta inoltre la creazione di firme di accesso condiviso (SAS) firmate con Azure AD credenziali. Per altre informazioni, vedere [concedere l'accesso limitato ai dati con firme di accesso condiviso](storage-sas-overview.md).

File di Azure supporta l'autorizzazione con AD (anteprima) o Azure AD DS (GA) su SMB solo per le macchine virtuali appartenenti a un dominio. Per informazioni sull'uso di AD (anteprima) o di Azure AD DS (GA) su SMB per File di Azure, vedere [Panoramica del supporto dell'autenticazione basata sull'identità file di Azure per l'accesso SMB](../files/storage-files-active-directory-overview.md).

L'autorizzazione con Azure AD non è supportata per l'archiviazione tabelle di Azure. Usare la chiave condivisa per autorizzare le richieste all'archiviazione tabelle.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Panoramica di Azure AD per BLOB e code

Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere a una risorsa BLOB o di Accodamento, la richiesta deve essere autorizzata, a meno che non sia un BLOB disponibile per l'accesso anonimo. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2,0. Successivamente, il token viene passato come parte di una richiesta al BLOB o Servizio di accodamento e utilizzato dal servizio per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che un'applicazione chieda un token di accesso OAuth 2,0 in fase di esecuzione. Se un'applicazione viene eseguita da un'entità di Azure, ad esempio una VM di Azure, un set di scalabilità di macchine virtuali o un'app funzioni di Azure, può usare un' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) per accedere a BLOB o code. Per informazioni su come autorizzare le richieste effettuate da un'identità gestita al BLOB o Servizio di accodamento di Azure, vedere [autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md).

Il passaggio di autorizzazione richiede che uno o più ruoli di Azure siano assegnati all'entità di sicurezza. Archiviazione di Azure fornisce ruoli di Azure che includono set comuni di autorizzazioni per i dati di BLOB e di Accodamento. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni che saranno disponibili nell'entità. Per altre informazioni sull'assegnazione di ruoli di Azure per archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).

Le applicazioni native e le applicazioni Web che effettuano richieste al BLOB di Azure o a Servizio di accodamento possono anche autorizzare l'accesso con Azure AD. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste di dati BLOB o di Accodamento, vedere [autorizzare l'accesso ad archiviazione di Azure con Azure ad da un'applicazione di archiviazione di Azure](storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Assegnare i ruoli di Azure per i diritti di accesso

Azure Active Directory (Azure AD) autorizza i diritti di accesso alle risorse protette tramite il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli predefiniti di Azure che comprende i set comuni di autorizzazioni utilizzate per accedere ai dati BLOB e di Accodamento. È anche possibile definire ruoli personalizzati per l'accesso ai dati BLOB e di Accodamento.

Quando un ruolo di Azure viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un Azure AD entità di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o un' [identità gestita per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>Ruoli predefiniti di Azure per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Per informazioni su come assegnare un ruolo predefinito di Azure a un'entità di sicurezza, vedere uno degli articoli seguenti:

- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo nel portale di Azure](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite l'interfaccia della riga di comando di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite PowerShell](storage-auth-aad-rbac-powershell.md)

Per altre informazioni sul modo in cui vengono definiti i ruoli predefiniti per Archiviazione di Azure, vedere [Informazioni sulle definizioni di ruolo](../../role-based-access-control/role-definitions.md#management-and-data-operations). Per informazioni sulla creazione di ruoli personalizzati di Azure, vedere [ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Autorizzazioni di accesso per le operazioni sui dati

Per informazioni dettagliate sulle autorizzazioni necessarie per chiamare operazioni BLOB o Servizio di accodamento specifiche, vedere [autorizzazioni per la chiamata di operazioni sui dati di BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Ambito risorsa

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Accedere ai dati con un account Azure AD

L'accesso ai dati di BLOB o di accodamento tramite l'portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure può essere autorizzato utilizzando l'account Azure AD dell'utente o utilizzando le chiavi di accesso dell'account (autorizzazione della chiave condivisa).

### <a name="data-access-from-the-azure-portal"></a>Accesso ai dati dal portale di Azure

Il portale di Azure può usare l'account Azure AD o le chiavi di accesso dell'account per accedere ai dati BLOB e di Accodamento in un account di archiviazione di Azure. Quale schema di autorizzazione Usa il portale di Azure dipende dai ruoli di Azure assegnati all'utente.

Quando si tenta di accedere ai dati di BLOB o di Accodamento, il portale di Azure controlla prima di tutto se è stato assegnato un ruolo di Azure con **Microsoft. storage/storageAccounts/listkeys/Action**. Se è stato assegnato un ruolo con questa azione, il portale di Azure usa la chiave dell'account per accedere ai dati BLOB e di accodamento tramite l'autorizzazione della chiave condivisa. Se a questa azione non è stato assegnato un ruolo, il portale di Azure tenta di accedere ai dati usando l'account Azure AD.

Per accedere ai dati BLOB o della coda dalla portale di Azure usando l'account Azure AD, è necessario disporre delle autorizzazioni per accedere ai dati BLOB e di Accodamento e sono necessarie anche le autorizzazioni per spostarsi tra le risorse dell'account di archiviazione nel portale di Azure. I ruoli predefiniti forniti dall'archiviazione di Azure concedono l'accesso alle risorse BLOB e di Accodamento, ma non concedono le autorizzazioni per le risorse dell'account di archiviazione. Per questo motivo, l'accesso al portale richiede anche l'assegnazione di un ruolo Azure Resource Manager, ad esempio il ruolo [Reader](../../role-based-access-control/built-in-roles.md#reader) , limitato al livello dell'account di archiviazione o superiore. Il ruolo **Reader** concede le autorizzazioni più limitate, ma è accettabile anche un altro Azure Resource Manager ruolo che concede l'accesso alle risorse di gestione degli account di archiviazione. Per altre informazioni su come assegnare le autorizzazioni agli utenti per l'accesso ai dati nel portale di Azure con un account di Azure AD, vedere [concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nell'portale di Azure](storage-auth-aad-rbac-portal.md).

Il portale di Azure indica quale schema di autorizzazione è in uso quando si passa a un contenitore o a una coda. Per altre informazioni sull'accesso ai dati nel portale, vedere [usare la portale di Azure per accedere ai dati di BLOB o di Accodamento](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Accesso ai dati da PowerShell o dall'interfaccia della riga di comando

L'interfaccia della riga di comando di Azure e PowerShell supportano l'accesso con le credenziali Azure AD. Dopo l'accesso, la sessione viene eseguita con tali credenziali. Per altre informazioni, vedere [eseguire l'interfaccia della riga di comando di Azure o i comandi di PowerShell con Azure ad credenziali per accedere ai dati BLOB o della coda](authorize-active-directory-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

- [Autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md)
- [Autorizzare con Azure Active Directory da un'applicazione per l'accesso a BLOB e code](storage-auth-aad-app.md)
- [Supporto di archiviazione di Azure per il controllo degli accessi in base al Azure Active Directory](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
