---
title: Guida alla sicurezza di Azure Storage Explorer | Microsoft Docs
description: Indicazioni sulla sicurezza per Azure Storage Explorer
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: e3bbe39077cf6d7781f7e11fde044cf272aa83e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714373"
---
# <a name="azure-storage-explorer-security-guide"></a>Guida alla sicurezza di Azure Storage Explorer

Microsoft Azure Storage Explorer consente di usare facilmente i dati di archiviazione di Azure in modo sicuro e sicuro in Windows, macOS e Linux. Seguendo queste linee guida, è possibile garantire che i dati rimangano protetti.

## <a name="general"></a>Generale

- **Usare sempre la versione più recente di Storage Explorer.** Le versioni di Storage Explorer possono contenere aggiornamenti della sicurezza. Rimanere sempre aggiornati consente di garantire la sicurezza generale.
- **Connettersi solo a risorse attendibili.** I dati scaricati da origini non attendibili potrebbero essere dannosi e il caricamento di dati in un'origine non attendibile può causare la perdita o il furto di dati.
- **Usare HTTPS quando possibile.** Storage Explorer utilizza HTTPS per impostazione predefinita. Alcuni scenari consentono di utilizzare HTTP, ma HTTP deve essere utilizzato solo come ultima risorsa.
- **Assicurarsi che vengano concesse solo le autorizzazioni necessarie agli utenti che ne hanno bisogno.** Evitare di essere eccessivamente permissivi quando si concede a chiunque l'accesso alle risorse.
- **Prestare attenzione durante l'esecuzione di operazioni critiche.** Alcune operazioni, ad esempio delete e overwrite, sono irreversibili e possono causare la perdita di dati. Assicurarsi di usare le risorse corrette prima di eseguire queste operazioni.

## <a name="choosing-the-right-authentication-method"></a>Scelta del metodo di autenticazione appropriato

Storage Explorer offre diversi modi per accedere alle risorse di archiviazione di Azure. Qualunque sia il metodo scelto, ecco le nostre raccomandazioni.

### <a name="azure-ad-authentication"></a>Autenticazione di Azure AD

Il modo più semplice e sicuro per accedere alle risorse di archiviazione di Azure consiste nell'accedere con l'account Azure. L'accesso USA Azure AD autenticazione, che consente di:

- Fornire l'accesso a utenti e gruppi specifici.
- Revocare l'accesso a utenti e gruppi specifici in qualsiasi momento.
- Applicare condizioni di accesso, ad esempio richiedere l'autenticazione a più fattori.

Quando possibile, è consigliabile usare l'autenticazione Azure AD.

Questa sezione descrive le due tecnologie basate su Azure AD che possono essere usate per proteggere le risorse di archiviazione.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Controllo degli accessi in base al ruolo di Azure

Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](https://docs.microsoft.com/azure/role-based-access-control/overview) ti permette di ottenere un controllo di accesso con granularità fine sulle risorse di Azure. I ruoli e le autorizzazioni di Azure possono essere gestiti dal portale di Azure.

Storage Explorer supporta l'accesso RBAC di Azure ad account di archiviazione, BLOB e code. Se è necessario accedere a condivisioni file o tabelle, sarà necessario assegnare i ruoli di Azure che concedono l'autorizzazione per elencare le chiavi dell'account di archiviazione.

#### <a name="access-control-lists-acls"></a>Elenchi di controllo di accesso (ACL)

Gli [elenchi di controllo di accesso (ACL)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) consentono di controllare l'accesso a livello di file e cartelle in ADLS Gen2 contenitori BLOB. È possibile gestire gli ACL usando Storage Explorer.

### <a name="shared-access-signatures-sas"></a>Firme di accesso condiviso

Se non è possibile usare l'autenticazione di Azure AD, è consigliabile usare le firme di accesso condiviso. Con le firme di accesso condiviso è possibile:

- Fornire accesso anonimo limitato a risorse protette.
- Revocare immediatamente una firma di accesso condiviso se generata da un criterio di accesso condiviso (SAP).

Tuttavia, con le firme di accesso condiviso, non è possibile:

- Limitare gli utenti che possono usare una firma di accesso condiviso. Una firma di accesso condiviso valida può essere usata da chiunque lo abbia.
- Revocare una firma di accesso condiviso se non generata da un criterio di accesso condiviso (SAP).

Quando si usa SAS in Storage Explorer, è consigliabile attenersi alle linee guida seguenti:

- **Limitare la distribuzione dei token e degli URI di firma di accesso condiviso.** Distribuisci solo token SAS e URI a utenti attendibili. La limitazione della distribuzione SAS riduce la probabilità di uso improprio di una firma di accesso condiviso.
- **Usare solo token di firma di accesso condiviso e URI da entità attendibili.**
- **Usare i criteri di accesso condiviso (SAP) quando si generano i token SAS e gli URI, se possibile.** Una firma di accesso condiviso basata su criteri di accesso condiviso è più sicura rispetto a una SAS nuda, perché la firma di accesso condiviso può essere revocata eliminando SAP.
- **Generare token con accesso e autorizzazioni minime alle risorse.** Le autorizzazioni minime limitano il potenziale danno che può essere eseguito se una firma di accesso condiviso viene usata in maniera impropria.
- **Generare token che sono validi solo per il tempo necessario.** Una breve durata è particolarmente importante per la firma di accesso condiviso nuda, perché non esiste alcun modo per revocarli una volta generati.

> [!IMPORTANT]
> Quando si condividono gli URI e i token SAS per la risoluzione dei problemi, ad esempio nelle richieste di servizio o nei report di bug, offuscare sempre almeno la parte della firma di accesso condiviso.

### <a name="storage-account-keys"></a>Chiavi dell'account di archiviazione

Le chiavi dell'account di archiviazione concedono l'accesso illimitato ai servizi e alle risorse in un account di archiviazione. Per questo motivo, è consigliabile limitare l'uso delle chiavi per accedere alle risorse in Storage Explorer. Usare le funzionalità RBAC di Azure o SAS per fornire l'accesso

Alcuni ruoli di Azure concedono l'autorizzazione per recuperare le chiavi dell'account di archiviazione. Gli utenti con questi ruoli possono eludere efficacemente le autorizzazioni concesse o negate da Azure RBAC. Si consiglia di non concedere questa autorizzazione, a meno che non sia necessario.

Storage Explorer tenterà di usare le chiavi dell'account di archiviazione, se disponibili, per autenticare le richieste. È possibile disabilitare questa funzionalità in impostazioni (**servizi > account di archiviazione > disabilitare l'utilizzo delle chiavi**). Alcune funzionalità non supportano RBAC di Azure, ad esempio l'uso di account di archiviazione classici. Tali funzionalità richiedono ancora chiavi e non sono interessate da questa impostazione.

Se è necessario usare le chiavi per accedere alle risorse di archiviazione, è consigliabile attenersi alle linee guida seguenti:

- **Non condividere le chiavi dell'account con altri utenti.**
- **Gestire le chiavi dell'account di archiviazione come le password.** Se è necessario rendere le chiavi accessibili, usare soluzioni di archiviazione sicure, ad esempio [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

> [!NOTE]
> Se si ritiene che una chiave dell'account di archiviazione sia stata condivisa o distribuita per errore, è possibile generare nuove chiavi per l'account di archiviazione dal portale di Azure.

### <a name="public-access-to-blob-containers"></a>Accesso pubblico ai contenitori BLOB

Storage Explorer consente di modificare il livello di accesso dei contenitori di archiviazione BLOB di Azure. I contenitori BLOB non privati consentono a chiunque di accedere in lettura anonima ai dati presenti in questi contenitori.

Quando si Abilita l'accesso pubblico per un contenitore BLOB, è consigliabile attenersi alle linee guida seguenti:

- **Non consentire l'accesso pubblico a un contenitore BLOB che può contenere dati potenzialmente sensibili.** Verificare che il contenitore BLOB sia privo di tutti i dati privati.
- **Non caricare dati potenzialmente sensibili in un contenitore BLOB con accesso BLOB o contenitore.** 

## <a name="next-steps"></a>Passaggi successivi

- [Suggerimenti per la sicurezza](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)
