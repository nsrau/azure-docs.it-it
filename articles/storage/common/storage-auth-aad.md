---
title: Autenticare l'accesso a BLOB di Azure e code con Azure Active Directory | Microsoft Docs
description: Autenticare l'accesso a BLOB di Azure e code con Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 2a632ef79c0e9bb925689456d682e7f22504806b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707837"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autenticare l'accesso a BLOB di Azure e code con Azure Active Directory

Archiviazione di Azure supporta l'autenticazione e l'autorizzazione con Azure Active Directory (AD) per i servizi BLOB e di accodamento. Azure AD consente di usare il controllo degli accessi in base al ruolo per concedere l'accesso a utenti, gruppi o entità servizio dell'applicazione. 

L'autenticazione degli utenti o delle applicazioni tramite le credenziali di Azure AD offre un livello superiore di sicurezza e facilità d'uso rispetto ad altri metodi di autorizzazione. Mentre con le applicazioni è possibile continuare a usare l'autorizzazione con chiave condivisa, l'uso di Azure AD consente di evitare la necessità di archiviare la chiave di accesso dell'account con il codice. È anche possibile continuare a usare le firme di accesso condiviso per concedere accesso specifico alle risorse dell'account di archiviazione, ma Azure AD offre funzionalità simili senza la necessità di gestire i token di firma di accesso condiviso o di occuparsi della revoca di una di firma di accesso condiviso compromessa. Microsoft consiglia di usare l'autenticazione di Azure AD per le applicazioni di Archiviazione di Azure, quando possibile.

L'autenticazione e autorizzazione con le credenziali di Azure AD è disponibile per tutti per utilizzo generico e account di archiviazione in tutte le aree pubbliche e i cloud nazionali Blob. Solo gli account di archiviazione creato con il supporto del modello di distribuzione Azure Resource Manager di autorizzazione di Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Panoramica di Azure AD per BLOB e code

Quando un'entità di sicurezza (un utente, gruppo o applicazione) tenta di accedere a una risorsa blob o di accodamento, la richiesta deve essere autorizzata, a meno che non si tratta di un blob disponibile per l'accesso anonimo. Con Azure AD, accesso a una risorsa è un processo in due passaggi. In primo luogo, autenticazione identità dell'entità di sicurezza e viene restituito un token OAuth 2.0. Successivamente, il token viene passato come parte di una richiesta al servizio Blob o una coda e utilizzato dal servizio per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che un'applicazione richiedere un token di accesso di OAuth 2.0 in fase di esecuzione. Se un'applicazione è in esecuzione all'interno di un'entità di Azure, ad esempio una VM di Azure, un set di scalabilità di macchine virtuali o un'app per le funzioni di Azure, è possibile utilizzare un [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) per accedere ai BLOB o code. Per informazioni su come autorizzare le richieste effettuate da un'identità gestita per il servizio di accodamento o Blob di Azure, vedere [autenticare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md).

Il passaggio di autorizzazione richiede che uno o più ruoli RBAC sia assegnato all'entità di sicurezza. Archiviazione di Azure fornisce i ruoli RBAC che includono set di autorizzazioni per i dati blob e di Accodamento comuni. I ruoli assegnati a un'entità di sicurezza determinano le relative autorizzazioni all'entità. Per altre informazioni sull'assegnazione dei ruoli RBAC per l'archiviazione di Azure, vedere [Gestisci i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).

Le applicazioni native e applicazioni web che inviano richieste al servizio di accodamento o Blob di Azure possono anche eseguire l'autenticazione con Azure AD. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste per i dati di accodamento o blob, vedere [eseguire l'autenticazione con Azure AD da un'applicazione di archiviazione di Azure](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Assegnazione dei ruoli RBAC per i diritti di accesso

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che includono set di autorizzazioni utilizzate per accedere ai dati blob e di Accodamento comuni. È anche possibile definire ruoli personalizzati per l'accesso ai dati blob e coda.

Quando viene assegnato un ruolo RBAC a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione, o un [identità per le risorse di Azure gestito](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Ruoli RBAC predefiniti per il BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Per informazioni su come assegnare un ruolo incorporato e RBAC a un'entità di sicurezza, vedere uno degli articoli seguenti:

- [Concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite la CLI di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite PowerShell](storage-auth-aad-rbac-powershell.md)

Per altre informazioni sul modo in cui vengono definiti i ruoli predefiniti per Archiviazione di Azure, vedere [Informazioni sulle definizioni di ruolo](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview). Per informazioni sulla creazione di ruoli personalizzati, vedere [creare ruoli personalizzati per il controllo di accesso](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Autorizzazioni di accesso per le operazioni sui dati

Per informazioni dettagliate sulle autorizzazioni necessarie per chiamare le operazioni di servizio di accodamento o Blob specifiche, vedere [le autorizzazioni per la chiamata di operazioni sui dati blob e coda](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Ambito di risorse

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Accesso ai dati con un account Azure AD

Accesso ai dati di blob o una coda tramite il portale di Azure, PowerShell o CLI di Azure possono essere autorizzate tramite account di Azure AD dell'utente o tramite le chiavi di accesso (autenticazione chiave condivisa).

### <a name="data-access-from-the-azure-portal"></a>Accesso ai dati dal portale di Azure

Il portale di Azure è possibile usare l'account Azure AD o le chiavi di accesso per accedere ai dati di accodamento e blob in un account di archiviazione di Azure. Quali il portale di Azure Usa lo schema di autorizzazione dipende da ruoli RBAC assegnati all'utente.

Quando si tenta di accedere ai dati di accodamento o blob, il portale di Azure controlla prima se si è stato assegnato un ruolo RBAC con **Microsoft.Storage/storageAccounts/listkeys/action**. Se è stato assegnato un ruolo con questa azione, il portale di Azure Usa la chiave dell'account di accesso ai dati di accodamento e blob tramite l'autorizzazione di chiave condivisa. Se si have non è stato assegnato un ruolo con questa azione, il portale di Azure tenta di accedere ai dati con il proprio account Azure AD.

Per accedere ai blob o accodamento dei dati dal portale di Azure con il proprio account Azure AD, sono necessarie autorizzazioni per accedere ai dati blob e di accodamento e occorre anche le autorizzazioni per spostarsi tra le risorse di account di archiviazione nel portale di Azure. I ruoli predefiniti forniti da archiviazione di Azure concedono l'accesso alle risorse di accodamento e blob, ma non concedono le autorizzazioni per le risorse di account di archiviazione. Per questo motivo, l'accesso al portale richiede anche l'assegnazione di un ruolo di Azure Resource Manager, ad esempio la [lettore](../../role-based-access-control/built-in-roles.md#reader) ruolo con ambito a livello di account di archiviazione o versione successiva. Il **lettore** ruolo concede le autorizzazioni più limitate, ma è accettabile anche un altro ruolo di Azure Resource Manager che concede l'accesso alle risorse di gestione di account di archiviazione. Per altre informazioni su come assegnare le autorizzazioni agli utenti l'accesso ai dati nel portale di Azure con un account Azure AD, vedere [concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](storage-auth-aad-rbac-portal.md).

Il portale di Azure indica quale schema di autorizzazione è in uso quando si passa a una coda o un contenitore. Per altre informazioni sull'accesso ai dati nel portale, vedere [usare il portale di Azure per accedere ai dati di accodamento o blob](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Accesso ai dati da PowerShell o CLI di Azure

PowerShell e CLI di Azure supporta l'accesso con credenziali di Azure AD. Dopo l'accesso, la sessione viene eseguita con tali credenziali. Per altre informazioni, vedere [comandi di esecuzione della riga di comando di Azure o PowerShell con credenziali di Azure AD per accedere ai dati di accodamento o blob](storage-auth-aad-script.md).

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Autenticazione di Azure AD su SMB per i file di Azure

File di Azure supporta l'autenticazione con Azure AD su SMB solo per le macchine virtuali aggiunte a un dominio (anteprima). Per altre informazioni sull'uso di Azure AD su SMB per i file di Azure, vedere [autenticazione di panoramica di Azure Active Directory su SMB per file di Azure (anteprima)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- [Autenticare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md)
- [Eseguire l'autenticazione con Azure Active Directory da un'applicazione per l'accesso ai BLOB e alle code](storage-auth-aad-app.md)
- [Controllo di accesso disponibile a livello generale in base al supporto di archiviazione di Azure per Azure Active Directory](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
