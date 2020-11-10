---
title: Sicurezza di Azure Key Vault
description: Gestire le autorizzazioni di accesso per Azure Key Vault, chiavi e segreti. L'articolo riguarda il modello di autenticazione e autorizzazione per Key Vault e offre informazioni su come proteggere l'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: dc08df7390285f9b6e4701bb1ca5c4227b19f1da
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445031"
---
# <a name="azure-key-vault-security"></a>Sicurezza di Azure Key Vault

Si usa Azure Key Vault per proteggere le chiavi di crittografia e i segreti come certificati, stringhe di connessione e password nel cloud. Quando si archiviano dati sensibili e aziendali critici, è necessario eseguire le operazioni necessarie per ottimizzare la sicurezza degli insiemi di credenziali e i dati archiviati.

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure AD della sottoscrizione. Chiunque cerchi di gestire o recuperare il contenuto da un insieme di credenziali deve essere autenticato da Azure AD.

- L'autenticazione stabilisce l'identità del chiamante.
- L'autorizzazione determina le operazioni che il chiamante può eseguire. L'autorizzazione in Key Vault usa una combinazione di [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md) e criteri di accesso Azure Key Vault.

### <a name="access-model-overview"></a>Panoramica del modello di accesso

L'accesso agli insiemi di credenziali avviene tramite due interfacce o piani. Questi piani sono il piano di gestione e il piano dati.

- Il *piano di gestione* consente di gestire Key Vault ed è l'interfaccia usata per creare ed eliminare gli insiemi di credenziali. È anche possibile leggere le proprietà degli insiemi di credenziali delle chiavi e gestire i criteri di accesso.
- Il *piano dati* consente di lavorare con i dati archiviati in un insieme di credenziali delle chiavi. È possibile aggiungere, eliminare e modificare chiavi, segreti e certificati.

Per accedere a un insieme di credenziali delle chiavi in uno dei piani, tutti i chiamanti (utenti o applicazioni) devono essere autenticati e autorizzati. Entrambi i piani usano Azure Active Directory (Azure AD) per l'autenticazione. Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e il piano dati usa un criterio di accesso Key Vault.

Il modello con un singolo meccanismo di autenticazione per entrambi i piani presenta alcuni vantaggi:

- Le organizzazioni possono controllare l'accesso a tutti gli insiemi di credenziali delle chiavi in modo centralizzato.
- Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli insiemi di credenziali delle chiavi all'interno dell'organizzazione.
- Le organizzazioni possono personalizzare l'autenticazione tramite le opzioni disponibili in Azure AD, ad esempio abilitando l'autenticazione a più fattori per una maggiore sicurezza.

### <a name="managing-administrative-access-to-key-vault"></a>Gestione dell'accesso amministrativo a Key Vault

Quando si crea un insieme di credenziali delle chiavi in un gruppo di risorse, si gestisce l'accesso usando Azure AD. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. È possibile concedere l'accesso a un determinato livello di ambito assegnando i ruoli di Azure appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. I livelli di ambito seguenti possono essere assegnati a un ruolo di Azure:

- **Sottoscrizione** : un ruolo di Azure assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e le risorse all'interno della sottoscrizione.
- **Gruppo di risorse** : un ruolo di Azure assegnato a livello di gruppo di risorse si applica a tutte le risorse nel gruppo di risorse.
- **Risorsa specifica** : un ruolo di Azure assegnato a una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere controllo degli accessi [in base al ruolo di Azure: ruoli predefiniti](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se un utente ha le autorizzazioni `Contributor` per un piano di gestione di un insieme di credenziali delle chiavi, l'utente può concedere a se stesso l'accesso al piano dati impostando criteri di accesso dell'insieme di credenziali delle chiavi. È necessario controllare attentamente chi ha accesso al ruolo `Contributor` per gli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti autorizzati possano accedere e gestire gli insiemi di credenziali delle chiavi, le chiavi, i segreti e i certificati.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controllo dell'accesso ai dati di Key Vault

I criteri di accesso di Key Vault concedono autorizzazioni separate per chiavi, segreti o certificati. È possibile concedere a un utente l'accesso solo alle chiavi e non ai segreti. Le autorizzazioni di accesso per chiavi, segreti e certificati vengono gestite a livello di insieme di credenziali.

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi non supportano le autorizzazioni granulari a livello di oggetto, ad esempio una chiave, un segreto o un certificato specifico. Quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, può eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.

È possibile impostare i criteri di accesso per un insieme di credenziali delle chiavi usando il [portale di Azure](assign-access-policy-portal.md), l'interfaccia della riga di comando di [Azure](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)o le [API REST di gestione Key Vault](/rest/api/keyvault/).

È possibile limitare l'accesso al piano dati usando gli [endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md). È possibile configurare [firewall e regole di rete virtuale](network-security.md) per un ulteriore livello di sicurezza.

## <a name="network-access"></a>Accesso alla rete

È possibile ridurre l'esposizione degli insiemi di credenziali specificando gli indirizzi IP che possono accedervi. Gli endpoint servizio di rete virtuale per Azure Key Vault consentono di limitare l'accesso a una rete virtuale specifica. Gli endpoint consentono anche di limitare l'accesso a un elenco di intervalli di indirizzi IPv4 (protocollo internet versione 4). L'accesso viene negato a tutti gli utenti che si connettono all'insieme di credenziali delle chiavi dall'esterno di tali origini.

Quando le regole del firewall vengono applicate, gli utenti possono leggere i dati da Key Vault solo quando le loro richieste hanno origine da reti virtuali o intervalli di indirizzi IPv4 consentiti. Questo vale anche per l'accesso a Key Vault dal portale di Azure. Benché gli utenti possano accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbero non essere in grado di elencare chiavi, segreti o certificati se il computer client in uso non è presente nell'elenco dei computer consentiti. Ciò influisce anche sul selettore dell'insieme di credenziali delle chiavi di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero essere in grado di visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non di elencare le chiavi.

Per altre informazioni sull'indirizzo di rete di Azure Key Vault, vedere [Endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md)

## <a name="tls-and-https"></a>TLS e HTTPS

*   Il front-end Key Vault (piano dati) è un server multi-tenant. Questo significa che gli insiemi di credenziali delle chiavi di diversi clienti possono condividere lo stesso indirizzo IP pubblico. Per ottenere l'isolamento, ogni richiesta HTTP viene autenticata e autorizzata indipendentemente da altre richieste.
*   È possibile identificare le versioni precedenti di TLS per segnalare le vulnerabilità, ma poiché l'indirizzo IP pubblico è condiviso, non è possibile per il team del servizio Key Vault disabilitare le versioni precedenti di TLS per gli insiemi di credenziali delle chiavi singoli a livello di trasporto.
*   Il protocollo HTTPS consente al client di partecipare alla negoziazione TLS. I **client possono applicare la versione più recente di TLS** e, ogni volta che un client esegue questa operazione, l'intera connessione utilizzerà la protezione del livello corrispondente. Il fatto che Key Vault supporta ancora le versioni precedenti di TLS non compromettere la sicurezza delle connessioni usando versioni di TLS più recenti.
*   Nonostante le vulnerabilità note nel protocollo TLS, non esiste alcun attacco noto che consentirebbe a un agente malintenzionato di estrarre tutte le informazioni dall'insieme di credenziali delle chiavi quando l'utente malintenzionato inizia una connessione con una versione di TLS con vulnerabilità. L'utente malintenzionato deve comunque eseguire l'autenticazione e l'autorizzazione e, a condizione che i client legittimi si connettano sempre alle versioni recenti di TLS, non è possibile che le credenziali siano state perse da vulnerabilità nelle versioni precedenti di TLS.

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

La funzionalità di registrazione di Key Vault salva le informazioni sulle attività eseguite nell'insieme di credenziali. Per informazioni dettagliate, vedere [Key Vault registrazione](logging.md).

Per consigli sulla gestione sicura degli account di archiviazione, vedere la [Guida alla sicurezza di archiviazione di Azure](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Passaggi successivi

- [Endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md)
- [Controllo degli accessi in base al ruolo Azure: ruoli predefiniti](../../role-based-access-control/built-in-roles.md)