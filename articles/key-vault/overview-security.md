---
title: Sicurezza di Azure Key Vault | Microsoft Docs
description: Gestire le autorizzazioni di accesso per Azure Key Vault, chiavi e segreti. L'articolo riguarda il modello di autenticazione e autorizzazione per Key Vault e offre informazioni su come proteggere l'insieme di credenziali delle chiavi.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 43847b53fbf84fe42be3efdbd647767904a05fb8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014279"
---
# <a name="azure-key-vault-security"></a>Sicurezza di Azure Key Vault

È necessario proteggere le chiavi di crittografia e i segreti, ad esempio i certificati, stringhe di connessione e password nel cloud in modo che si usa Azure Key Vault. Poiché si sta archiviando sensibili e dati aziendali critici, è necessario eseguire passaggi per ottimizzare la sicurezza dei dati archiviati in essi contenuti e gli insiemi di credenziali. Questo articolo illustra alcuni dei concetti che è opportuno considerare quando si progetta la sicurezza di Azure Key Vault.

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure AD della sottoscrizione. Chiunque provi a gestire o recuperare il contenuto da un insieme di credenziali deve essere autenticate da Azure AD.

- L'autenticazione stabilisce l'identità del chiamante.
- Autorizzazione determina le operazioni che il chiamante può eseguire. Autorizzazione in Azure Key Vault Usa una combinazione di [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) (RBAC) e i criteri di accesso di Azure Key Vault.

### <a name="access-model-overview"></a>Panoramica del modello di accesso

Accesso agli insiemi di credenziali viene eseguita tramite due interfacce o piani. Questi piani sono il piano di gestione e piano dati.

- Il *piano di gestione* è dove gestire Key Vault se stessa ed è l'interfaccia utilizzata per creare ed eliminare gli insiemi di credenziali. È anche possibile leggere le proprietà di insieme di credenziali delle chiavi e gestire i criteri di accesso.
- Il *piano dati* consente di lavorare con i dati archiviati in un insieme di credenziali delle chiavi. È possibile aggiungere, eliminare e modificare chiavi, segreti e certificati.

Per accedere a un insieme di credenziali delle chiavi in entrambi piano, tutti i chiamanti (utenti o applicazioni) devono essere autenticati e autorizzati. Entrambi i piani usano Azure Active Directory (Azure AD) per l'autenticazione. Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo, mentre il piano dati usa i criteri di accesso dell'insieme di credenziali delle chiavi.

Il modello con un singolo meccanismo di autenticazione per entrambi i piani presenta alcuni vantaggi:

- Le organizzazioni possono controllare l'accesso a tutti gli insiemi di credenziali delle chiavi in modo centralizzato.
- Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli insiemi di credenziali delle chiavi all'interno dell'organizzazione.
- Le organizzazioni possono personalizzare l'autenticazione con le opzioni di Azure AD, ad esempio per abilitare l'autenticazione a più fattori per maggiore sicurezza

### <a name="managing-administrative-access-to-key-vault"></a>Gestire l'accesso amministrativo a Key Vault

Quando si crea un insieme di credenziali delle chiavi in un gruppo di risorse, è gestire l'accesso con Azure AD. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. È possibile concedere l'accesso a un livello di ambito specifico assegnando i ruoli RBAC appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. A un ruolo Controllo degli accessi in base al ruolo è possibile assegnare i livelli di ambiti seguenti:

- **Sottoscrizione** Un ruolo Controllo degli accessi in base al ruolo assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e a tutte le risorse in tale sottoscrizione.
- **Gruppo di risorse**: Un ruolo Controllo degli accessi in base al ruolo assegnato a livello di gruppo di risorse si applica a tutte le risorse di tale gruppo.
- **Risorsa specifica**: Un ruolo Controllo degli accessi in base al ruolo assegnato per una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se un utente ha le autorizzazioni `Contributor` per un piano di gestione di un insieme di credenziali delle chiavi, l'utente può concedere a se stesso l'accesso al piano dati impostando criteri di accesso dell'insieme di credenziali delle chiavi. È necessario controllare attentamente chi ha accesso al ruolo `Contributor` per gli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti autorizzati possano accedere e gestire gli insiemi di credenziali delle chiavi, le chiavi, i segreti e i certificati.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controllo dell'accesso ai dati Key Vault

I criteri di accesso di Key Vault concedono autorizzazioni separatamente per le chiavi, segreti o certificati. È possibile concedere a un utente l'accesso solo alle chiavi e non ai segreti. Autorizzazioni di accesso per le chiavi, segreti e certificati vengono gestite a livello di insieme di credenziali.

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi non supportano le autorizzazioni granulari a livello di oggetto, ad esempio una chiave, un segreto o un certificato specifico. Quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, può eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.

Per impostare i criteri di accesso per un insieme di credenziali delle chiavi, usare il [portale di Azure](https://portal.azure.com/), l'[interfaccia della riga di comando di Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di gestione di Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

È possibile limitare l'accesso al piano dati usando gli [endpoint di servizio della rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). È possibile configurare [firewall e regole della rete virtuale](key-vault-network-security.md) per un ulteriore livello di sicurezza.

## <a name="network-access"></a>Accesso alla rete

È possibile ridurre l'esposizione degli insiemi di credenziali, specificando gli indirizzi IP possano accedervi. Gli endpoint del servizio di rete virtuale per Azure Key Vault consentono di limitare l'accesso a una rete virtuale specifica. Gli endpoint consentono anche di limitare l'accesso a un elenco di intervalli di indirizzi IPv4 (protocollo internet versione 4). L'accesso viene negato a tutti gli utenti che si connettono all'insieme di credenziali delle chiavi dall'esterno di tali origini.

Dopo aver firewall regole sono in effetti, gli utenti possono solo leggere i dati da Key Vault quando le richieste provengono da reti virtuali consentite o intervalli di indirizzi IPv4. Questo vale anche per l'accesso a Key Vault dal portale di Azure. Benché gli utenti possano accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbero non essere in grado di elencare chiavi, segreti o certificati se il computer client in uso non è presente nell'elenco dei computer consentiti. Ciò influisce anche sul selettore dell'insieme di credenziali delle chiavi di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero essere in grado di visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non di elencare le chiavi.

Per altre informazioni su Azure Key Vault verifica indirizzo di rete [endpoint del servizio rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitoraggio

La registrazione di Key Vault consente di salvare informazioni sulle attività eseguite nell'insieme di credenziali. Log di Key Vault:

- Tutte autenticate richieste dell'API REST, tra cui le richieste non riuscite
  - Operazioni per la chiave dell'insieme di credenziali se stesso. Tali operazioni includono la creazione, eliminazione, impostazione di criteri di accesso e aggiornamento degli attributi dell'insieme di credenziali chiave come i tag.
  - Operazioni sulle chiavi e segreti nell'insieme di credenziali chiave, tra cui:
    - Creazione, modifica o eliminazione di chiavi o segreti.
    - Firma, verifica, la crittografia, decrittografia, ritorno a capo e rimozione del wrapping delle chiavi, recupero di informazioni riservate ed elenco chiavi e segreti (e relative versioni).
- Richieste non autenticate che generano una risposta 401. Esempi sono le richieste senza un token di connessione, che sono in formato non valido o è scaduto o che hanno un token non valido.

Le informazioni di registrazione sono accessibile all'interno di 10 minuti dopo l'operazione di insieme di credenziali delle chiavi. Spetta all'utente la gestione dei log nell'account di archiviazione. 

- Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
- Eliminare i log che non è più necessario mantenere nell'account di archiviazione.

Per indicazioni su gestione dell'archiviazione in modo sicuro gli account di esaminare il [Guida alla sicurezza di archiviazione di Azure](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>Fasi successive

- [Endpoint del servizio rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [Controllo degli accessi in base al ruolo: ruoli predefiniti](../role-based-access-control/built-in-roles.md)
- [Endpoint del servizio rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
