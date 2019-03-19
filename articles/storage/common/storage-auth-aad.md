---
title: Autenticare l'accesso a BLOB e code di Azure tramite Azure Active Directory (anteprima) | Microsoft Docs
description: Autenticare l'accesso a BLOB e code di Azure tramite Azure Active Directory (anteprima).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dcf7d237c8cfbf52a804e428d84fff0bb328c7c8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012098"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Autenticare l'accesso a BLOB e code di Azure tramite Azure Active Directory (anteprima)

Archiviazione di Azure supporta l'autenticazione e l'autorizzazione con Azure Active Directory (AD) per i servizi BLOB e di accodamento. Azure AD consente di usare il controllo degli accessi in base al ruolo per concedere l'accesso a utenti, gruppi o entità servizio dell'applicazione. 

L'autenticazione degli utenti o delle applicazioni tramite le credenziali di Azure AD offre un livello superiore di sicurezza e facilità d'uso rispetto ad altri metodi di autorizzazione. Mentre con le applicazioni è possibile continuare a usare l'autorizzazione con chiave condivisa, l'uso di Azure AD consente di evitare la necessità di archiviare la chiave di accesso dell'account con il codice. È anche possibile continuare a usare le firme di accesso condiviso per concedere accesso specifico alle risorse dell'account di archiviazione, ma Azure AD offre funzionalità simili senza la necessità di gestire i token di firma di accesso condiviso o di occuparsi della revoca di una di firma di accesso condiviso compromessa. Microsoft consiglia di usare l'autenticazione di Azure AD per le applicazioni di Archiviazione di Azure, quando possibile.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

Per la versione di anteprima, tenere presente quanto segue:

- L'integrazione di Azure AD è disponibile per i servizi BLOB e di accodamento solo nell'anteprima.
- L'integrazione di Azure AD è disponibile per gli account di archiviazione GPv1, GPv2 e BLOB in tutte le aree pubbliche. 
- Sono supportati solo gli account di archiviazione creati con il modello di distribuzione di Resource Manager. 
- A breve sarà disponibile il supporto per le informazioni sull'identità del chiamante nei log di Analisi archiviazione di Azure.
- L'autorizzazione di Azure AD dell'accesso alle risorse negli account di archiviazione standard è attualmente supportata. L'autorizzazione dell'accesso ai BLOB di pagine negli account di archiviazione Premium sarà supportata a breve.
- Archiviazione di Azure supporta ruoli Controllo degli accessi in base al ruolo predefiniti e personalizzati. È possibile assegnare ruoli nell'ambito della sottoscrizione, del gruppo di risorse, dell'account di archiviazione o di un singolo contenitore o coda.
- Le librerie client di Archiviazione di Azure che attualmente supportano l'integrazione di Azure AD includono:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [BLOB, code e file](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>Panoramica di Azure AD per l'archiviazione

Il primo passaggio per l'uso dell'integrazione di Azure AD con Archiviazione di Azure è costituito dall'assegnazione dei ruoli Controllo degli accessi in base al ruolo per i dati di archiviazione all'entità servizio (un utente, un gruppo o un'entità servizio dell'applicazione) o alle identità gestite per le risorse di Azure. I ruoli Controllo degli accessi in base al ruolo includono set comuni di autorizzazioni per contenitori e code. Per altre informazioni sull'assegnazione dei ruoli RBAC per l'archiviazione di Azure, vedere [Gestisci i diritti di accesso ai dati di archiviazione con RBAC (anteprima)](storage-auth-aad-rbac.md).

Per usare Azure AD per autorizzare l'accesso alle risorse di archiviazione contenute nelle applicazioni è necessario richiedere al codice un token di accesso OAuth 2.0. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste ad Archiviazione di Azure, vedere [Eseguire l'autenticazione con Azure AD da un'applicazione di Archiviazione di Azure (anteprima)](storage-auth-aad-app.md). Se si usa un'identità gestita, vedere [Autenticare l'accesso a BLOB e code con identità gestite di Azure per Risorse di Azure (anteprima)](storage-auth-aad-msi.md).

L'interfaccia della riga di comando di Azure e PowerShell ora supportano l'accesso con un'identità di Azure AD. Dopo l'accesso con un'identità di Azure AD, la sessione viene eseguita con questa identità. Per altre informazioni, vedere [Usare un'identità di Azure AD per accedere ad Archiviazione di Azure con l'interfaccia della riga di comando o PowerShell (anteprima)](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Ruoli Controllo degli accessi in base al ruolo per BLOB e code

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli di controllo degli accessi in base al ruolo predefiniti che includono un set comune di autorizzazioni usate per accedere a contenitori o code. 

Quando viene assegnato un ruolo RBAC a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione, o un [identità per le risorse di Azure gestito](../../active-directory/managed-identities-azure-resources/overview.md). 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Per informazioni su come assegnare un ruolo predefinito nel portale di Azure, vedere [concedere l'accesso a contenitori di Azure e code con accessi nel portale di Azure (anteprima)](storage-auth-aad-rbac.md).

### <a name="access-permissions-granted-by-rbac-roles"></a>Autorizzazioni di accesso concesse dai ruoli RBAC 

La tabella seguente riepiloga i diritti di accesso concessi per i ruoli predefiniti per diversi livelli di ambito:

|Scope|Proprietario dei dati BLOB|Collaboratore ai dati del BLOB di archiviazione|Lettore dati dei BLOB di archiviazione|Collaboratore ai dati della coda di archiviazione|Lettore dei dati della coda di archiviazione|
|---|---|---|---|---|---|
|Livello di sottoscrizione|Accesso in lettura/scrittura e gestione del controllo di accesso per tutti i contenitori e BLOB nella sottoscrizione|Accesso in lettura/scrittura a tutti i contenitori e BLOB nella sottoscrizione| Accesso in lettura a tutti i contenitori e BLOB nella sottoscrizione|Accesso in lettura/scrittura a tutte le code nella sottoscrizione|Accesso in lettura a tutte le code nella sottoscrizione|
|Livello Gruppo di risorse|Accesso in lettura/scrittura e gestione del controllo di accesso POSIX per tutti i contenitori e BLOB nel gruppo di risorse|Accesso in lettura/scrittura a tutti i contenitori e BLOB nel gruppo di risorse|Accesso in lettura a tutti i contenitori e BLOB nel gruppo di risorse|Accesso in lettura/scrittura a tutte le code nel gruppo di risorse|Accesso in lettura a tutte le code nel gruppo di risorse|
|Livello account di archiviazione|Accesso in lettura/scrittura e gestione del controllo di accesso POSIX per tutti i contenitori e BLOB nell'account di archiviazione|Accesso in lettura/scrittura a tutti i contenitori e BLOB nell'account di archiviazione|Accesso in lettura a tutti i contenitori e BLOB nell'account di archiviazione|Accesso in lettura/scrittura a tutte le code nell'account di archiviazione|Accesso in lettura a tutte le code nell'account di archiviazione|
|Livello di contenitore/coda|Accesso in lettura/scrittura e gestione del controllo di accesso POSIX per il contenitore specificato e i relativi BLOB.|Accesso in lettura/scrittura al contenitore specificato e ai relativi BLOB|Accesso in lettura al contenitore specificato e ai relativi BLOB|Accesso in lettura/scrittura alla coda specificata|Accesso in lettura alla coda specificata|

Per informazioni dettagliate sulle autorizzazioni necessarie per chiamare operazioni di Archiviazione di Azure, vedere [Permissions for calling REST operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations) (Autorizzazioni per chiamare operazioni REST).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione di Azure AD per BLOB e code di Azure, vedere il post di blog del team di Archiviazione di Azure sull'[annuncio della versione di anteprima di Autenticazione di Azure AD per Archiviazione di Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
