---
title: Sicurezza di Azure Key Vault | Microsoft Docs
description: Gestire le autorizzazioni di accesso per Azure Key Vault, chiavi e segreti. L'articolo riguarda il modello di autenticazione e autorizzazione per Key Vault e offre informazioni su come proteggere l'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: edef64c215f96b34cd0efbff4521a7d5adb98885
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870579"
---
# <a name="azure-key-vault-security"></a>Sicurezza di Azure Key Vault

È necessario proteggere le chiavi di crittografia e i segreti, ad esempio certificati, stringhe di connessione e password nel cloud, quindi si usa Azure Key Vault. Poiché si archiviano dati sensibili e aziendali critici, è necessario eseguire le operazioni appropriate per ottimizzare la sicurezza degli insiemi di credenziali e dei dati archiviati al loro interno. Questo articolo illustra alcuni dei concetti che è opportuno considerare quando si progetta la sicurezza di Azure Key Vault.

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure AD della sottoscrizione. Chiunque cerchi di gestire o recuperare il contenuto da un insieme di credenziali deve essere autenticato da Azure AD.

- L'autenticazione stabilisce l'identità del chiamante.
- L'autorizzazione determina le operazioni che il chiamante può eseguire. L'autorizzazione in Key Vault usa una combinazione di [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) e criteri di accesso di Azure Key Vault.

### <a name="access-model-overview"></a>Panoramica del modello di accesso

L'accesso agli insiemi di credenziali avviene tramite due interfacce o piani. Questi piani sono il piano di gestione e il piano dati.

- Il *piano di gestione* consente di gestire Key Vault ed è l'interfaccia usata per creare ed eliminare gli insiemi di credenziali. È anche possibile leggere le proprietà degli insiemi di credenziali delle chiavi e gestire i criteri di accesso.
- Il *piano dati* consente di lavorare con i dati archiviati in un insieme di credenziali delle chiavi. È possibile aggiungere, eliminare e modificare chiavi, segreti e certificati.

Per accedere a un insieme di credenziali delle chiavi in uno dei piani, tutti i chiamanti (utenti o applicazioni) devono essere autenticati e autorizzati. Entrambi i piani usano Azure Active Directory (Azure AD) per l'autenticazione. Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo, mentre il piano dati usa i criteri di accesso dell'insieme di credenziali delle chiavi.

Il modello con un singolo meccanismo di autenticazione per entrambi i piani presenta alcuni vantaggi:

- Le organizzazioni possono controllare l'accesso a tutti gli insiemi di credenziali delle chiavi in modo centralizzato.
- Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli insiemi di credenziali delle chiavi all'interno dell'organizzazione.
- Le organizzazioni possono personalizzare l'autenticazione tramite le opzioni disponibili in Azure AD, ad esempio abilitando l'autenticazione a più fattori per una maggiore sicurezza.

### <a name="managing-administrative-access-to-key-vault"></a>Gestione dell'accesso amministrativo a Key Vault

Quando si crea un insieme di credenziali delle chiavi in un gruppo di risorse, si gestisce l'accesso usando Azure AD. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. È possibile concedere l'accesso a un determinato livello di ambito assegnando i ruoli di Azure appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. I livelli di ambito seguenti possono essere assegnati a un ruolo di Azure:

- **Sottoscrizione**: un ruolo di Azure assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e le risorse all'interno della sottoscrizione.
- **Gruppo di risorse**: un ruolo di Azure assegnato a livello di gruppo di risorse si applica a tutte le risorse nel gruppo di risorse.
- **Risorsa specifica**: un ruolo di Azure assegnato a una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se un utente ha le autorizzazioni `Contributor` per un piano di gestione di un insieme di credenziali delle chiavi, l'utente può concedere a se stesso l'accesso al piano dati impostando criteri di accesso dell'insieme di credenziali delle chiavi. È necessario controllare attentamente chi ha accesso al ruolo `Contributor` per gli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti autorizzati possano accedere e gestire gli insiemi di credenziali delle chiavi, le chiavi, i segreti e i certificati.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controllo dell'accesso ai dati di Key Vault

I criteri di accesso di Key Vault concedono autorizzazioni separate per chiavi, segreti o certificati. È possibile concedere a un utente l'accesso solo alle chiavi e non ai segreti. Le autorizzazioni di accesso per chiavi, segreti e certificati vengono gestite a livello di insieme di credenziali.

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi non supportano le autorizzazioni granulari a livello di oggetto, ad esempio una chiave, un segreto o un certificato specifico. Quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, può eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.

Per impostare i criteri di accesso per un insieme di credenziali delle chiavi, usare il [portale di Azure](https://portal.azure.com/), l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/) o le [API REST di gestione di Key Vault](/rest/api/keyvault/).

È possibile limitare l'accesso al piano dati usando gli [endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md). È possibile configurare [firewall e regole di rete virtuale](network-security.md) per un ulteriore livello di sicurezza.

## <a name="network-access"></a>Accesso alla rete

È possibile ridurre l'esposizione degli insiemi di credenziali specificando gli indirizzi IP che possono accedervi. Gli endpoint servizio di rete virtuale per Azure Key Vault consentono di limitare l'accesso a una rete virtuale specifica. Gli endpoint consentono anche di limitare l'accesso a un elenco di intervalli di indirizzi IPv4 (protocollo internet versione 4). L'accesso viene negato a tutti gli utenti che si connettono all'insieme di credenziali delle chiavi dall'esterno di tali origini.

Quando le regole del firewall vengono applicate, gli utenti possono leggere i dati da Key Vault solo quando le loro richieste hanno origine da reti virtuali o intervalli di indirizzi IPv4 consentiti. Questo vale anche per l'accesso a Key Vault dal portale di Azure. Benché gli utenti possano accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbero non essere in grado di elencare chiavi, segreti o certificati se il computer client in uso non è presente nell'elenco dei computer consentiti. Ciò influisce anche sul selettore dell'insieme di credenziali delle chiavi di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero essere in grado di visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non di elencare le chiavi.

Per altre informazioni sull'indirizzo di rete di Azure Key Vault, vedere [Endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitoraggio

La funzionalità di registrazione di Key Vault salva le informazioni sulle attività eseguite nell'insieme di credenziali. Log di Key Vault:

- Tutte le richieste API REST autenticate, incluse quelle con esito negativo
  - Operazioni nell'insieme di credenziali delle chiavi stesso. Queste operazioni includono creazione, eliminazione, impostazione di criteri di accesso e aggiornamento degli attributi dell'insieme di credenziali delle chiavi come i tag.
  - Operazioni su chiavi e segreti nell'insieme di credenziali delle chiavi, tra cui:
    - Creazione, modifica o eliminazione di queste chiavi o segreti.
    - Firma, verifica, crittografia, decrittografia, wrapping e annullamento del wrapping delle chiavi, recupero di segreti ed elenco di chiavi e segreti (e delle relative versioni).
- Richieste non autenticate che generano una risposta 401. Alcuni esempi sono le richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.

È possibile accedere alle informazioni di registrazione entro 10 minuti dall'operazione sull'insieme di credenziali delle chiavi. La gestione dei log nell'account di archiviazione è compito dell'utente.

- Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
- Eliminare i log che non è più necessario mantenere nell'account di archiviazione.

Per consigli sulla gestione sicura degli account di archiviazione, vedere la [Guida alla sicurezza di Archiviazione di Azure](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Passaggi successivi

- [Endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md)
- [Controllo degli accessi in base al ruolo: ruoli predefiniti](../../role-based-access-control/built-in-roles.md)

