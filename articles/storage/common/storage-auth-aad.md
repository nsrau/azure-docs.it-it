---
title: Autenticare l'accesso a BLOB di Azure e code con Azure Active Directory | Microsoft Docs
description: Autenticare l'accesso a BLOB di Azure e code con Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/28/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e72400a759b976b1a2a6864b2fa7d7d91e16c62f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619289"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autenticare l'accesso a BLOB di Azure e code con Azure Active Directory

Archiviazione di Azure supporta l'autenticazione e l'autorizzazione con Azure Active Directory (AD) per i servizi BLOB e di accodamento. Azure AD consente di usare il controllo degli accessi in base al ruolo per concedere l'accesso a utenti, gruppi o entità servizio dell'applicazione. 

L'autenticazione degli utenti o delle applicazioni tramite le credenziali di Azure AD offre un livello superiore di sicurezza e facilità d'uso rispetto ad altri metodi di autorizzazione. Mentre con le applicazioni è possibile continuare a usare l'autorizzazione con chiave condivisa, l'uso di Azure AD consente di evitare la necessità di archiviare la chiave di accesso dell'account con il codice. È anche possibile continuare a usare le firme di accesso condiviso per concedere accesso specifico alle risorse dell'account di archiviazione, ma Azure AD offre funzionalità simili senza la necessità di gestire i token di firma di accesso condiviso o di occuparsi della revoca di una di firma di accesso condiviso compromessa. Microsoft consiglia di usare l'autenticazione di Azure AD per le applicazioni di Archiviazione di Azure, quando possibile.

L'autenticazione e autorizzazione con le credenziali di Azure AD è disponibile per tutti i per utilizzo generico v2, per utilizzo generico v1 e account di archiviazione Blob in tutte le aree pubbliche. Solo gli account di archiviazione creato con il supporto del modello di distribuzione Azure Resource Manager di autorizzazione di Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Panoramica di Azure AD per BLOB e code

Il primo passaggio per l'uso dell'integrazione di Azure AD con Archiviazione di Azure è costituito dall'assegnazione dei ruoli Controllo degli accessi in base al ruolo per i dati di archiviazione all'entità servizio (un utente, un gruppo o un'entità servizio dell'applicazione) o alle identità gestite per le risorse di Azure. I ruoli Controllo degli accessi in base al ruolo includono set comuni di autorizzazioni per contenitori e code. Per altre informazioni sull'assegnazione dei ruoli RBAC per l'archiviazione di Azure, vedere [Gestisci i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).

Per usare Azure AD per autorizzare l'accesso alle risorse di archiviazione contenute nelle applicazioni è necessario richiedere al codice un token di accesso OAuth 2.0. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste ad archiviazione di Azure, vedere [eseguire l'autenticazione con Azure AD da un'applicazione di archiviazione di Azure](storage-auth-aad-app.md). Se si usa un'identità gestita, vedere [autentica l'accesso a BLOB e code con Azure managed le identità per le risorse di Azure](storage-auth-aad-msi.md).

L'interfaccia della riga di comando di Azure e PowerShell ora supportano l'accesso con un'identità di Azure AD. Dopo l'accesso con un'identità Azure AD, la sessione viene eseguito con tale identità. Per altre informazioni, vedere [usare un'identità di Azure AD per accedere ad archiviazione di Azure con PowerShell o CLI](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Ruoli Controllo degli accessi in base al ruolo per BLOB e code

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che includono set di autorizzazioni utilizzate per accedere ai dati blob e di Accodamento comuni. È anche possibile definire ruoli personalizzati per l'accesso ai dati blob e coda.

Quando viene assegnato un ruolo RBAC a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione, o un [identità per le risorse di Azure gestito](../../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Per informazioni su come assegnare un RBAC predefiniti per le risorse di archiviazione di Azure, vedere uno degli argomenti seguenti:

- [Concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite la CLI di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite PowerShell](storage-auth-aad-rbac-powershell.md)

### <a name="access-permissions-granted-by-rbac-roles"></a>Autorizzazioni di accesso concesse dai ruoli RBAC 

Per informazioni dettagliate sulle autorizzazioni necessarie per chiamare operazioni di Archiviazione di Azure, vedere [Permissions for calling REST operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations) (Autorizzazioni per chiamare operazioni REST).

## <a name="resource-scope"></a>Ambito di risorse

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Controllo di accesso disponibile a livello generale in base al supporto di archiviazione di Azure per Azure Active Directory](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
- [Eseguire l'autenticazione con Azure Active Directory da un'applicazione per l'accesso ai BLOB e alle code](storage-auth-aad-app.md)
- [Autenticare l'accesso a BLOB e code con identità gestite per le risorse di Azure](storage-auth-aad-msi.md)
- File di Azure supporta l'autenticazione con Azure AD su SMB solo per le macchine virtuali aggiunte a un dominio (anteprima). Per informazioni sull'uso di Azure AD su SMB per File di Azure, vedere [Panoramica dell'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](../files/storage-files-active-directory-overview.md).