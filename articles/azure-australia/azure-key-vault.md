---
title: Azure Key Vault in Azure Australia
description: Linee guida per la configurazione e l'uso di Azure Key Vault per la gestione delle chiavi all'interno delle aree australiane per soddisfare i requisiti specifici di criteri, normative e normativa per il governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602122"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault in Azure Australia

L'archiviazione sicura delle chiavi crittografiche e la gestione del ciclo di vita delle chiavi crittografiche sono elementi critici all'interno dei sistemi crittografici.  Il servizio che fornisce questa funzionalità in Azure è la Azure Key Vault. Key Vault è stato eseguito l'accesso alla sicurezza IRAP e ACSC certificate per la protezione.  Questo articolo descrive le considerazioni principali relative all'uso di Key Vault per conformarsi ai [controlli manuali di sicurezza delle informazioni](https://acsc.gov.au/infosec/ism/) (ASD) della direzione dei segnali australiani (ISM).

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti. Poiché questi dati sono sensibili e business critical, Key Vault consente l'accesso sicuro agli insiemi di credenziali delle chiavi, consentendo solo a utenti e applicazioni autorizzate. Esistono tre elementi principali gestiti e controllati da Key Vault:

- chiavi
- segreti
- certificati

Questo articolo è incentrato sulla gestione delle chiavi con Key Vault.

![Insieme di credenziali delle chiavi di Azure](media/azure-key-vault-overview.png)

*Diagramma 1 – Azure Key Vault*

## <a name="key-design-considerations"></a>Considerazioni di progettazione principali

### <a name="deployment-options"></a>Opzioni di distribuzione

Sono disponibili due opzioni per la creazione di insiemi di credenziali delle chiavi di Azure. Entrambe le opzioni usano la famiglia di moduli di protezione hardware nShield di Thales, sono convalidati FIPS (Federal Information Processing Standards) e sono approvati per archiviare le chiavi in ambienti protetti. Le opzioni sono:

- **Insiemi di credenziali protetti da software:** FIPS 140-2 livello 1 convalidato. Chiavi archiviate in un modulo di protezione hardware. Le operazioni di crittografia e decrittografia vengono eseguite nelle risorse di calcolo in Azure.
- **Insiemi di credenziali protetti da HSM:** FIPS 140-2 livello 2 convalidato. Chiavi archiviate in un modulo di protezione hardware. Le operazioni di crittografia e decrittografia vengono eseguite sul modulo di protezione hardware.

Key Vault supporta chiavi di crittografia a curva ellittica (RSA) e Adleman (ECC). Il valore predefinito è chiavi RSA a 2048 bit, ma è disponibile un'opzione avanzata per le chiavi RSA 3072-bit, RSA 4096-bit e ECC.  Tutte le chiavi soddisfano i controlli ISM, ma sono preferite le chiavi a curva ellittica.

### <a name="resource-operations"></a>Operazioni sulle risorse

Esistono diversi utenti di Azure Key Vault:

- **Amministratore Key Vault:** Gestisce il ciclo di vita dell'insieme di credenziali
- **Amministratore chiavi:** Gestisce il ciclo di vita delle chiavi nell'insieme di credenziali
- **Developer/Operator:** Integrare le chiavi dall'insieme di credenziali in applicazioni e servizi
- **Revisore** Monitorare l'utilizzo e l'accesso alle chiavi
- **Applicazioni:** Usare le chiavi per proteggere le informazioni

Azure Key Vault è protetta con due interfacce separate:

- **Piano di gestione:** Questo piano si occupa della gestione dell'insieme di credenziali ed è protetto da RBAC.
- **Piano dati:** Questo piano riguarda la gestione e l'accesso agli elementi nell'insieme di credenziali.  Protetto tramite criteri di accesso Key Vault.

Come richiesto da ISM, prima che un chiamante (un utente o un'applicazione) prima di poter ottenere l'accesso a Key Vault da uno dei due piani è necessario disporre di autorizzazioni e autenticazione appropriate.

Il controllo degli accessi in base al ruolo di Azure include un ruolo predefinito per Key Vault, [Key Vault collaboratore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor), per controllare la gestione degli insiemi di credenziali delle chiavi. La creazione di ruoli personalizzati allineati a ruoli più granulari per la gestione degli insiemi di credenziali delle chiavi è consigliata.

>[!WARNING]
>Quando l'accesso alle chiavi viene abilitato tramite Key Vault criteri di accesso, l'utente o l'applicazione ha l'accesso a tutte le chiavi nell'insieme di credenziali delle chiavi (ad esempio, se un utente ha l'accesso "Delete", può eliminare tutte le chiavi).  Pertanto, è necessario distribuire più insiemi di credenziali delle chiavi per allinearli ai domini o ai limiti di sicurezza.

### <a name="networking"></a>Rete

È possibile configurare Key Vault firewall e le reti virtuali per controllare l'accesso al piano dati.  È possibile consentire l'accesso a utenti o applicazioni su reti specifiche negando l'accesso a utenti o applicazioni in tutte le altre reti. I [Servizi attendibili](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services) rappresentano un'eccezione a questo controllo se è abilitato "Consenti servizi attendibili".  Il controllo rete virtuale non si applica al piano di gestione.

L'accesso agli insiemi di credenziali delle chiavi deve essere esplicitamente limitato al set minimo di reti che dispongono di utenti o applicazioni che richiedono l'accesso alle chiavi.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault supporta BYOK.  BYOK consente agli utenti di importare chiavi dalle infrastrutture chiave esistenti.  Thales fornisce un [set di strumenti australiano](https://www.microsoft.com/download/details.aspx?id=45345) per supportare il trasferimento sicuro e l'importazione di chiavi da un HSM esterno (ad esempio, chiavi generate con una workstation offline) in Key Vault.

### <a name="key-vault-auditing-and-logging"></a>Controllo e registrazione Key Vault

Il ACSC richiede che le entità Commonwealth usino i servizi di Azure appropriati per eseguire il monitoraggio e la creazione di report in tempo reale sui carichi di lavoro di Azure.

La registrazione viene abilitata abilitando l'impostazione di diagnostica **_"AuditEvent"_** per i valori di chiave.  Gli eventi di controllo verranno registrati nell'account di archiviazione specificato.  Il periodo **_"RetentionInDays"_** deve essere impostato in base ai criteri di conservazione dei dati.  Le [operazioni](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret) sul piano di gestione e sul piano dati vengono controllate e registrate. La [soluzione Azure Key Vault in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) può essere usata per esaminare Key Vault log AuditEvent.  Per elaborare e distribuire Key Vault eventi, è possibile usare diversi altri servizi di Azure.

### <a name="key-rotation"></a>Rotazione delle chiavi

L'archiviazione delle chiavi in Key Vault ha fornito un unico punto per mantenere le chiavi all'esterno delle applicazioni che consentono l'aggiornamento delle chiavi senza influire sul comportamento delle applicazioni. L'archiviazione delle chiavi in Azure Key Vault Abilita diverse strategie per supportare la rotazione delle chiavi:

- Manualmente
- A livello di codice tramite le API
- Script di automazione (ad esempio, usando PowerShell e automazione di Azure)

Queste opzioni consentono di ruotare le chiavi su base periodica per soddisfare i requisiti di conformità o ad hoc se si verificano problemi di compromissione delle chiavi.

#### <a name="key-rotation-strategies"></a>Strategie di rotazione delle chiavi

È importante sviluppare una strategia di rotazione delle chiavi appropriata per le chiavi archiviate in Key Vault.  Se si usa la chiave errata, le informazioni vengono decrittografate in modo errato e la perdita di chiavi può comportare la perdita completa dell'accesso alle informazioni.  Esempi di strategie di rotazione delle chiavi per diversi scenari includono:

- **Dati** in fase di elaborazione: le informazioni volatili vengono trasmesse tra due parti.  Quando una chiave viene ruotata, entrambe le parti devono disporre di un meccanismo per il recupero sincrono delle chiavi aggiornate dall'insieme di credenziali delle chiavi.
- **Dati come REST:** Un'entità archivia i dati crittografati e li decrittografa in futuro da usare.  Quando una chiave viene ruotata, i dati devono essere decrittografati con la chiave precedente e quindi crittografati con la nuova chiave ruotata.  Sono disponibili approcci che consentono di ridurre al minimo l'effetto del processo di decrittografia/crittografia mediante chiavi di crittografia chiave (vedere l'esempio).  Microsoft gestisce la maggior parte del processo correlato alla rotazione della chiave per archiviazione di Azure (vedere...)
- **Chiavi di accesso:** un numero di servizi di Azure dispone di chiavi di accesso che possono essere archiviate in Key Vault (ad esempio, CosmosDB).  I servizi di Azure hanno chiavi di accesso primarie e secondarie.  È importante che entrambe le chiavi non siano ruotate nello stesso momento.  Pertanto, una chiave deve essere ruotata dopo un periodo e l'operazione della chiave è stata verificata, quindi la seconda chiave può essere ruotata.

### <a name="high-availability"></a>Disponibilità elevata

ISM dispone di diversi controlli correlati alla continuità aziendale.
Azure Key Vault dispone di più livelli di ridondanza con contenuto replicato all'interno dell'area e nell' [area associata](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)secondaria.

Quando l'insieme di credenziali delle chiavi si trova in uno stato di failover, è in modalità di sola lettura e tornerà alla modalità di lettura/scrittura il servizio primario viene ripristinato.

ISM dispone di diversi controlli correlati al backup.  È importante sviluppare ed eseguire piani di backup/ripristino appropriati per gli insiemi di credenziali e le relative chiavi.

## <a name="key-lifecycle"></a>Ciclo di vita delle chiavi

### <a name="key-operations"></a>Operazioni relative alle chiavi

Key Vault supportano le operazioni seguenti su una chiave:

- **creare** consente a un client di creare una chiave in Key Vault. Il valore della chiave viene generato da Key Vault e archiviato e non viene rilasciato al cliente. In Key Vault è possibile creare chiavi asimmetriche.
- **importare** consente a un client di importare una chiave esistente in Key Vault. Le chiavi asimmetriche possono essere importate in Key Vault usando una serie di metodi di creazione di pacchetti diversi all'interno di un costrutto JWK.
- **aggiornamento** consente a un client con autorizzazioni sufficienti di modificare i metadati (attributi chiave) associati a una chiave archiviata in precedenza in Key Vault.
- **eliminare** consente a un client con autorizzazioni sufficienti di eliminare una chiave da Key Vault.
- **elenco** consente a un client di elencare tutte le chiavi di un determinato Key Vault.
- **Elenca versioni:** consente a un client di elencare tutte le versioni di una chiave specifica in un determinato Key Vault.
- **Ottieni:** consente a un client di recuperare le parti pubbliche di una determinata chiave in un Key Vault.
- **backup** esporta una chiave in un formato protetto.
- **ripristinare** importa una chiave precedentemente sottoposta a backup.

È disponibile un set di autorizzazioni corrispondente che può essere concesso a utenti, entità servizio o applicazioni utilizzando Key Vault le voci di controllo di accesso per consentire loro di eseguire operazioni chiave.

Key Vault dispone di una funzionalità di eliminazione temporanea per consentire il ripristino di insiemi di credenziali e chiavi eliminati. Per impostazione predefinita, l'opzione **_"eliminazione temporanea"_** non è abilitata, ma una volta abilitata, gli oggetti vengono conservati per 90 giorni (periodo di conservazione) e vengono visualizzati per essere eliminati.  Un'ulteriore autorizzazione **_"ripulitura"_** consente l'eliminazione permanente delle chiavi se l'opzione **_"Ripulisci protezione dati"_** è disabilitata.

La creazione o l'importazione di una chiave esistente crea una nuova versione della chiave.

### <a name="cryptographic-operations"></a>Operazioni di crittografia

Key Vault supporta anche le operazioni di crittografia usando le chiavi:

- **firma e verifica:** questa operazione è "Sign hash" o "Verify hash". Key Vault non supporta l'hashing del contenuto come parte della creazione della firma.
- **crittografia/wrapping chiavi:** questa operazione viene usata per proteggere un'altra chiave.
- **crittografare e decrittografare:** la chiave archiviata viene usata per crittografare o decrittografare un singolo blocco di dati

È disponibile un set di autorizzazioni corrispondente che può essere concesso a utenti, entità servizio o applicazioni utilizzando Key Vault le voci di controllo di accesso per consentire loro di eseguire operazioni di crittografia.

Sono disponibili tre attributi chiave che possono essere impostati per controllare se una chiave è abilitata e utilizzabile per le operazioni di crittografia:

- **enabled**
- **NBF:** non prima dell'abilitazione prima della data specificata
- **Exp:** data di scadenza

## <a name="storage-and-keys"></a>Archiviazione e chiavi

Le chiavi gestite dal cliente sono più flessibili e consentono la valutazione e la gestione delle chiavi da controllare. Consentono inoltre di controllare le chiavi di crittografia usate per proteggere i dati.
Sono disponibili tre aspetti per archiviazione e chiavi archiviati in Key Vault:

- Key Vault chiavi dell'account di archiviazione gestita
- Azure crittografia del servizio di archiviazione (SSE) per i dati inattivi
- Dischi gestiti e crittografia dischi di Azure

La gestione delle chiavi dell'account di archiviazione di Azure Key Vault è un'estensione del servizio chiavi di Key Vault che supporta la sincronizzazione e la rigenerazione (rotazione) delle chiavi dell'account di archiviazione.  [Integrazione di archiviazione di Azure con Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) l'anteprima è consigliata quando viene rilasciata perché fornisce sicurezza e facilità d'uso migliori.
SSE usa due chiavi per gestire la crittografia dei dati inattivi:

- Chiavi di crittografia della chiave (KEK)
- Chiavi di crittografia dei dati

Sebbene Microsoft gestisca chiavi DEK, SSE è in grado di usare KEKs gestiti dal cliente che possono essere archiviati in Key Vault. Questo consente la rotazione delle chiavi in Azure Key Vault in base ai criteri di conformità appropriati. Quando le chiavi vengono ruotate, archiviazione di Azure crittografa nuovamente la chiave di crittografia dell'account per l'account di archiviazione. Questa operazione non comporta la ricrittografia di tutti i dati e non è necessario eseguire altre azioni.

SSE viene usato per i dischi gestiti, ma le chiavi gestite dal cliente non sono supportate.  La crittografia dei dischi gestiti può essere eseguita usando crittografia dischi di Azure con chiavi KEK gestite dal cliente in Key Vault.

## <a name="next-steps"></a>Fasi successive

Esaminare l'articolo sulla [Federazione delle identità](identity-federation.md)

Esaminare ulteriori Azure Key Vault documentazione ed esercitazioni nella [libreria di riferimento](reference-library.md)
