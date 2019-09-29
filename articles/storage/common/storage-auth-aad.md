---
title: Autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory | Microsoft Docs
description: Autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: caf72d4af390956391fdab133cf0897abbee4633
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673148"
---
# <a name="authorize-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory

Archiviazione di Azure supporta l'uso di Azure Active Directory (AD) per autorizzare le richieste all'archiviazione BLOB e di Accodamento. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente, un gruppo o un'entità servizio dell'applicazione. L'entità di sicurezza viene autenticata da Azure AD per restituire un token OAuth 2,0. Il token può essere usato per autorizzare una richiesta di accesso a una risorsa nell'archivio BLOB o di Accodamento.

L'autorizzazione di utenti o applicazioni tramite un token OAuth 2,0 restituito da Azure AD offre sicurezza e facilità d'uso superiori rispetto alle firme di accesso condiviso (SAS). Con Azure AD, non è necessario archiviare la chiave di accesso dell'account con il codice e rischiare potenziali vulnerabilità della sicurezza. Mentre con le applicazioni è possibile continuare a usare l'autorizzazione con chiave condivisa, l'uso di Azure AD consente di evitare la necessità di archiviare la chiave di accesso dell'account con il codice. È anche possibile continuare a usare le firme di accesso condiviso per concedere accesso specifico alle risorse dell'account di archiviazione, ma Azure AD offre funzionalità simili senza la necessità di gestire i token di firma di accesso condiviso o di occuparsi della revoca di una di firma di accesso condiviso compromessa. Microsoft consiglia di usare Azure AD autorizzazione con le applicazioni di archiviazione di Azure, quando possibile.

L'autorizzazione con Azure AD è disponibile per tutti gli account di archiviazione BLOB e per utilizzo generico in tutte le aree pubbliche e nei cloud nazionali. Solo gli account di archiviazione creati con il modello di distribuzione Azure Resource Manager supportano Azure AD autorizzazione. L'autorizzazione con Azure AD non è supportata per l'archiviazione tabelle di Azure.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Panoramica di Azure AD per BLOB e code

Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere a una risorsa BLOB o di Accodamento, la richiesta deve essere autorizzata, a meno che non sia un BLOB disponibile per l'accesso anonimo. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2,0. Successivamente, il token viene passato come parte di una richiesta al BLOB o Servizio di accodamento e utilizzato dal servizio per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che un'applicazione chieda un token di accesso OAuth 2,0 in fase di esecuzione. Se un'applicazione viene eseguita da un'entità di Azure, ad esempio una VM di Azure, un set di scalabilità di macchine virtuali o un'app funzioni di Azure, può usare un' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) per accedere a BLOB o code. Per informazioni su come autorizzare le richieste effettuate da un'identità gestita al BLOB o Servizio di accodamento di Azure, vedere [autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md).

Il passaggio di autorizzazione richiede che uno o più ruoli RBAC siano assegnati all'entità di sicurezza. Archiviazione di Azure fornisce ruoli RBAC che includono set comuni di autorizzazioni per i dati di BLOB e di Accodamento. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni che saranno disponibili nell'entità. Per altre informazioni sull'assegnazione di ruoli RBAC per archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).

Le applicazioni native e le applicazioni Web che effettuano richieste al BLOB di Azure o a Servizio di accodamento possono anche autorizzare l'accesso con Azure AD. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste di dati BLOB o di Accodamento, vedere [autorizzare l'accesso ad archiviazione di Azure con Azure ad da un'applicazione di archiviazione di Azure](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Assegnazione di ruoli RBAC per i diritti di accesso

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che comprende i set comuni di autorizzazioni utilizzate per accedere ai dati BLOB e di Accodamento. È anche possibile definire ruoli personalizzati per l'accesso ai dati BLOB e di Accodamento.

Quando un ruolo RBAC viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un Azure AD entità di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o un' [identità gestita per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Ruoli RBAC predefiniti per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Per informazioni su come assegnare un ruolo predefinito di controllo degli accessi in base al ruolo a un'entità di sicurezza, vedere uno degli articoli seguenti:

- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo nel portale di Azure](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite l'interfaccia della riga di comando di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite PowerShell](storage-auth-aad-rbac-powershell.md)

Per altre informazioni sul modo in cui vengono definiti i ruoli predefiniti per Archiviazione di Azure, vedere [Informazioni sulle definizioni di ruolo](../../role-based-access-control/role-definitions.md#management-and-data-operations). Per informazioni sulla creazione di ruoli RBAC personalizzati, vedere [creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Autorizzazioni di accesso per le operazioni sui dati

Per informazioni dettagliate sulle autorizzazioni necessarie per chiamare operazioni BLOB o Servizio di accodamento specifiche, vedere [autorizzazioni per la chiamata di operazioni sui dati di BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Ambito delle risorse

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Accedere ai dati con un account Azure AD

L'accesso ai dati di BLOB o di accodamento tramite l'portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure può essere autorizzato utilizzando l'account Azure AD dell'utente o utilizzando le chiavi di accesso dell'account (autorizzazione della chiave condivisa).

### <a name="data-access-from-the-azure-portal"></a>Accesso ai dati dal portale di Azure

Il portale di Azure può usare l'account Azure AD o le chiavi di accesso dell'account per accedere ai dati BLOB e di Accodamento in un account di archiviazione di Azure. Lo schema di autorizzazione utilizzato dal portale di Azure dipende dai ruoli RBAC assegnati.

Quando si tenta di accedere ai dati del BLOB o della coda, il portale di Azure controlla prima di tutto se è stato assegnato un ruolo RBAC con **Microsoft. storage/storageAccounts/listkeys/Action**. Se è stato assegnato un ruolo con questa azione, il portale di Azure usa la chiave dell'account per accedere ai dati BLOB e di accodamento tramite l'autorizzazione della chiave condivisa. Se a questa azione non è stato assegnato un ruolo, il portale di Azure tenta di accedere ai dati usando l'account Azure AD.

Per accedere ai dati BLOB o della coda dalla portale di Azure usando l'account Azure AD, è necessario disporre delle autorizzazioni per accedere ai dati BLOB e di Accodamento e sono necessarie anche le autorizzazioni per spostarsi tra le risorse dell'account di archiviazione nel portale di Azure. I ruoli predefiniti forniti dall'archiviazione di Azure concedono l'accesso alle risorse BLOB e di Accodamento, ma non concedono le autorizzazioni per le risorse dell'account di archiviazione. Per questo motivo, l'accesso al portale richiede anche l'assegnazione di un ruolo Azure Resource Manager, ad esempio il ruolo [Reader](../../role-based-access-control/built-in-roles.md#reader) , limitato al livello dell'account di archiviazione o superiore. Il ruolo **Reader** concede le autorizzazioni più limitate, ma è accettabile anche un altro Azure Resource Manager ruolo che concede l'accesso alle risorse di gestione degli account di archiviazione. Per altre informazioni su come assegnare le autorizzazioni agli utenti per l'accesso ai dati nel portale di Azure con un account di Azure AD, vedere [concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nell'portale di Azure](storage-auth-aad-rbac-portal.md).

Il portale di Azure indica quale schema di autorizzazione è in uso quando si passa a un contenitore o a una coda. Per altre informazioni sull'accesso ai dati nel portale, vedere [usare la portale di Azure per accedere ai dati di BLOB o di Accodamento](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Accesso ai dati da PowerShell o dall'interfaccia della riga di comando

L'interfaccia della riga di comando di Azure e PowerShell supportano l'accesso con le credenziali Azure AD. Dopo l'accesso, la sessione viene eseguita con tali credenziali. Per altre informazioni, vedere [eseguire l'interfaccia della riga di comando di Azure o i comandi di PowerShell con Azure ad credenziali per accedere ai dati BLOB o della coda](storage-auth-aad-script.md).

## <a name="azure-ad-authorization-over-smb-for-azure-files"></a>Azure AD autorizzazione su SMB per File di Azure

File di Azure supporta l'autorizzazione con Azure AD su SMB solo per le macchine virtuali appartenenti a un dominio (anteprima). Per informazioni sull'uso di Azure AD su SMB per File di Azure, vedere [Panoramica dell'autorizzazione di Azure Active Directory su SMB per file di Azure (anteprima)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- [Autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md)
- [Autorizzare con Azure Active Directory da un'applicazione per l'accesso a BLOB e code](storage-auth-aad-app.md)
- [Supporto di archiviazione di Azure per il controllo degli accessi in base al Azure Active Directory](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
