---
title: Sicurezza Azure Key Vault | Microsoft Docs
description: Gestire le autorizzazioni di accesso per Azure Key Vault, chiavi e segreti. L'articolo riguarda il modello di autenticazione e autorizzazione per Key Vault e offre informazioni su come proteggere l'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 728398aeec4715d15ebe44ae6d4e4bfa5f295df8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884780"
---
# <a name="azure-key-vault-security"></a>Sicurezza Azure Key Vault

È necessario proteggere le chiavi di crittografia e i segreti, ad esempio certificati, stringhe di connessione e password nel cloud, in modo da usare Azure Key Vault. Poiché si archiviano dati sensibili e aziendali critici, è necessario eseguire le operazioni necessarie per ottimizzare la sicurezza degli insiemi di credenziali e i dati archiviati in essi. In questo articolo verranno illustrati alcuni dei concetti che è opportuno considerare quando si progetta la sicurezza del Azure Key Vault.

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure AD della sottoscrizione. Chiunque tenti di gestire o recuperare il contenuto da un insieme di credenziali deve essere autenticato da Azure AD.

- L'autenticazione stabilisce l'identità del chiamante.
- L'autorizzazione determina le operazioni che il chiamante è in grado di eseguire. L'autorizzazione in Key Vault usa una combinazione di [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) (RBAC) e criteri di accesso Azure Key Vault.

### <a name="access-model-overview"></a>Panoramica del modello di accesso

L'accesso agli insiemi di credenziali avviene tramite due interfacce o piani. Questi piani sono il piano di gestione e il piano dati.

- Il *piano di gestione* è il punto in cui si gestiscono Key Vault stesso ed è l'interfaccia usata per creare ed eliminare insiemi di credenziali. È anche possibile leggere le proprietà dell'insieme di credenziali delle chiavi e gestire i criteri di accesso.
- Il *piano dati* consente di lavorare con i dati archiviati in un insieme di credenziali delle chiavi. È possibile aggiungere, eliminare e modificare chiavi, segreti e certificati.

Per accedere a un insieme di credenziali delle chiavi in entrambi i piani, è necessario autenticare e autorizzare tutti i chiamanti (utenti o applicazioni). Entrambi i piani usano Azure Active Directory (Azure AD) per l'autenticazione. Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo, mentre il piano dati usa i criteri di accesso dell'insieme di credenziali delle chiavi.

Il modello con un singolo meccanismo di autenticazione per entrambi i piani presenta alcuni vantaggi:

- Le organizzazioni possono controllare l'accesso a tutti gli insiemi di credenziali delle chiavi in modo centralizzato.
- Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli insiemi di credenziali delle chiavi all'interno dell'organizzazione.
- Le organizzazioni possono personalizzare l'autenticazione usando le opzioni disponibili in Azure AD, ad esempio per abilitare l'autenticazione a più fattori per una maggiore sicurezza

### <a name="managing-administrative-access-to-key-vault"></a>Gestione dell'accesso amministrativo ai Key Vault

Quando si crea un insieme di credenziali delle chiavi in un gruppo di risorse, è possibile gestire l'accesso usando Azure AD. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. È possibile concedere l'accesso a un livello di ambito specifico assegnando i ruoli RBAC appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. A un ruolo Controllo degli accessi in base al ruolo è possibile assegnare i livelli di ambiti seguenti:

- **Sottoscrizione** Un ruolo Controllo degli accessi in base al ruolo assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e a tutte le risorse in tale sottoscrizione.
- **Gruppo di risorse**: Un ruolo Controllo degli accessi in base al ruolo assegnato a livello di gruppo di risorse si applica a tutte le risorse di tale gruppo.
- **Risorsa specifica**: Un ruolo Controllo degli accessi in base al ruolo assegnato per una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se un utente ha le autorizzazioni `Contributor` per un piano di gestione di un insieme di credenziali delle chiavi, l'utente può concedere a se stesso l'accesso al piano dati impostando criteri di accesso dell'insieme di credenziali delle chiavi. È necessario controllare attentamente chi ha accesso al ruolo `Contributor` per gli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti autorizzati possano accedere e gestire gli insiemi di credenziali delle chiavi, le chiavi, i segreti e i certificati.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controllo dell'accesso ai dati di Key Vault

Key Vault criteri di accesso concedono le autorizzazioni separatamente a chiavi, segreti o certificati. È possibile concedere a un utente l'accesso solo alle chiavi e non ai segreti. Le autorizzazioni di accesso per chiavi, segreti e certificati sono gestite a livello di insieme di credenziali.

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi non supportano le autorizzazioni granulari a livello di oggetto, ad esempio una chiave, un segreto o un certificato specifico. Quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, può eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.

Per impostare i criteri di accesso per un insieme di credenziali delle chiavi, usare il [portale di Azure](https://portal.azure.com/), l'[interfaccia della riga di comando di Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di gestione di Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

È possibile limitare l'accesso al piano dati usando gli [endpoint servizio di rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). È possibile configurare [firewall e regole di rete virtuale](key-vault-network-security.md) per un ulteriore livello di sicurezza.

## <a name="network-access"></a>Accesso alla rete

È possibile ridurre l'esposizione degli insiemi di credenziali specificando quali indirizzi IP possono accedervi. Gli endpoint servizio di rete virtuale per Azure Key Vault consentono di limitare l'accesso a una rete virtuale specifica. Gli endpoint consentono anche di limitare l'accesso a un elenco di intervalli di indirizzi IPv4 (protocollo internet versione 4). L'accesso viene negato a tutti gli utenti che si connettono all'insieme di credenziali delle chiavi dall'esterno di tali origini.

Dopo aver applicato le regole del firewall, gli utenti possono leggere i dati solo da Key Vault quando le loro richieste provengono da reti virtuali consentite o da intervalli di indirizzi IPv4. Questo vale anche per l'accesso a Key Vault dal portale di Azure. Benché gli utenti possano accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbero non essere in grado di elencare chiavi, segreti o certificati se il computer client in uso non è presente nell'elenco dei computer consentiti. Ciò influisce anche sul selettore dell'insieme di credenziali delle chiavi di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero essere in grado di visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non di elencare le chiavi.

Per ulteriori informazioni sugli [endpoint del servizio rete virtuale](key-vault-overview-vnet-service-endpoints.md) Azure Key Vault verifica indirizzo di rete per Azure Key Vault

## <a name="monitoring"></a>Monitoraggio

Key Vault registrazione salva le informazioni sulle attività eseguite nell'insieme di credenziali. Log Key Vault:

- Tutte le richieste API REST autenticate, incluse le richieste non riuscite
  - Operazioni nell'insieme di credenziali delle chiavi. Queste operazioni includono la creazione, l'eliminazione, l'impostazione dei criteri di accesso e l'aggiornamento degli attributi dell'insieme di credenziali delle chiavi come i tag.
  - Operazioni su chiavi e segreti nell'insieme di credenziali delle chiavi, tra cui:
    - Creazione, modifica o eliminazione di queste chiavi o segreti.
    - Firma, verifica, crittografia, decrittografia, wrapping e annullamento del wrapping delle chiavi, recupero di segreti ed elenco di chiavi e segreti (e delle relative versioni).
- Richieste non autenticate che generano una risposta 401. Alcuni esempi sono le richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.

È possibile accedere alle informazioni di registrazione entro 10 minuti dall'operazione di Key Vault. È responsabilità dell'utente gestire i log nell'account di archiviazione. 

- Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
- Eliminare i log che non è più necessario mantenere nell'account di archiviazione.

Per consigli sulla gestione sicura degli account di archiviazione, vedere la [Guida alla sicurezza di archiviazione di Azure](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>Fasi successive

- [Endpoint del servizio rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [Controllo degli accessi in base al ruolo: ruoli predefiniti](../role-based-access-control/built-in-roles.md)
- [endpoint del servizio rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
