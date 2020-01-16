---
title: Crittografia dei dati a server singolo del database di Azure per PostgreSQL con chiave gestita dal cliente
description: La crittografia dei dati a server singolo di database di Azure per PostgreSQL con chiave gestita dal cliente consente di Bring Your Own Key (BYOK) per la protezione dei dati inattivi e consente alle organizzazioni di implementare la separazione dei compiti nella gestione di chiavi e dati.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 263fdda178752ee22997a03a11902a7bff4791dc
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028621"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-customer-managed-key"></a>Crittografia dei dati a server singolo del database di Azure per PostgreSQL con chiave gestita dal cliente

> [!NOTE]
> A questo punto, è necessario richiedere l'accesso per usare questa funzionalità. A tale scopo, contattare AskAzureDBforPostgreSQL@service.microsoft.com.

La crittografia dei dati a server singolo di database di Azure per PostgreSQL con chiave gestita dal cliente consente di Bring Your Own Key (BYOK) per la protezione dei dati inattivi e consente alle organizzazioni di implementare la separazione dei compiti nella gestione di chiavi e dati. Con la crittografia gestita dal cliente, l'utente è responsabile di e in un controllo completo del ciclo di vita di una chiave (creazione, caricamento, rotazione, eliminazione), autorizzazioni di utilizzo della chiave e controllo delle operazioni sulle chiavi.

Per il server singolo database di Azure per PostgreSQL, la crittografia dei dati viene impostata a livello di server. Con questa forma di crittografia dei dati, la chiave viene usata per la crittografia della chiave di crittografia del database, ovvero una chiave asimmetrica gestita dal cliente archiviata in un Azure Key Vault di proprietà del cliente e gestito dal cliente [(AKV)](../key-vault/key-Vault-secure-your-key-Vault.md), un sistema di gestione delle chiavi esterne basato sul cloud. AKV è a disponibilità elevata e fornisce un'archiviazione sicura scalabile per le chiavi crittografiche RSA, supportata facoltativamente da moduli di protezione hardware convalidati FIPS 140-2 Level 2 (HSM). Non consente l'accesso diretto a una chiave archiviata, ma fornisce servizi di crittografia/decrittografia usando la chiave per le entità autorizzate. La chiave può essere generata dal Key Vault, importato o [trasferito al Key Vault da un dispositivo HSM](../key-vault/key-Vault-hsm-protected-keys.md)locale.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui il server singolo database di Azure per PostgreSQL supporta i piani tariffari per utilizzo generico e con ottimizzazione per la memoria.

## <a name="benefits"></a>Vantaggi

La crittografia dei dati per il server singolo di database di Azure per PostgreSQL offre i vantaggi seguenti:

* Maggiore trasparenza e controllo granulare e gestione per la chiave di crittografia 
* Gestione centralizzata e organizzazione delle chiavi ospitate in Azure Key Vault. 
* Possibilità di implementare la separazione dei compiti nella gestione delle chiavi e dei dati all'interno dell'organizzazione
* Gestione delle chiavi separata da gestione dati all'interno di un'organizzazione, quindi Key Vault amministratore può revocare le autorizzazioni di accesso alla chiave per rendere inaccessibile il database crittografato 
* Maggiore attendibilità dei clienti finali, dal momento che Azure Key Vault è progettata in modo che Microsoft non possa visualizzare né estrarre le chiavi di crittografia

## <a name="terminology-and-description"></a>Terminologia e descrizione

**Chiave DEK (Data Encryption Key)** : una chiave AES256 simmetrica usata per crittografare una partizione o un blocco di dati. La crittografia di ogni blocco di dati con una chiave diversa rende più complessi gli attacchi di crittoanalisi. È necessario l'accesso alle chiavi DEK per il provider di risorse o l'istanza dell'applicazione che esegue la crittografia e la decrittografia di un blocco specifico. Quando una chiave di crittografia viene sostituita con una nuova chiave, solo i dati nel blocco associato devono essere crittografati nuovamente con la nuova chiave.

Chiave di **crittografia della chiave (KEK)** : chiave di crittografia usata per crittografare le chiavi di crittografia dei dati. L'uso di una chiave di crittografia della chiave che non lascia mai Key Vault consente di crittografare e controllare le chiavi di crittografia dei dati. L'entità che ha accesso alla chiave KEK può essere diversa dall'entità che richiede la chiave DEK. Poiché è necessaria la chiave KEK per decrittografare le chiavi DEK, la chiave KEK è di fatto un singolo punto che consente di eliminare in modo efficace le chiavi DEK eliminando la chiave KEK.

Le chiavi di crittografia dei dati, crittografate con le chiavi di crittografia della chiave, vengono archiviate separatamente e solo un'entità con accesso alla chiave di crittografia della chiave può decrittografare queste chiavi di crittografia dei dati. Per altre informazioni, vedere [sicurezza in crittografia](../security/fundamentals/encryption-atrest.md)dati inattivi.

## <a name="how-data-encryption-with-customer-managed-key-works"></a>Funzionamento della crittografia dei dati con la chiave gestita dal cliente

![Panoramica di Bring your own key](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

Per consentire a un server PostgreSQL di usare le chiavi gestite dal cliente archiviate in AKV per la crittografia della chiave di crittografia, un amministratore Key Vault deve concedere al server i seguenti diritti di accesso usando l'identità univoca:

* **Get** : per il recupero della parte pubblica e delle proprietà della chiave nella Key Vault
* **wrapKey** -per poter crittografare la chiave di crittografia
* **unwrapKey** -per poter decrittografare la decrittografia

Key Vault amministratore può anche [abilitare la registrazione degli eventi di controllo Key Vault](../azure-monitor/insights/azure-key-vault.md), in modo che possano essere controllati in un secondo momento.

Quando il server è configurato per l'utilizzo della chiave gestita dal cliente archiviata nel Key Vault, il server invia la chiave di crittografia al Key Vault per la crittografia. Key Vault restituisce la chiave di crittografia crittografata, archiviata nel database utente. Analogamente, quando necessario, il server invia la chiave di crittografia protetta al Key Vault per la decrittografia. Se la registrazione è abilitata, i revisori possono usare monitoraggio di Azure per esaminare Key Vault registri AuditEvent.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Requisiti per la configurazione della crittografia dei dati per il server singolo database di Azure per PostgreSQL

### <a name="requirements-for-configuring-akv"></a>Requisiti per la configurazione di AKV

* Key Vault e il server singolo database di Azure per PostgreSQL devono appartenere allo stesso tenant di Azure Active Directory (AAD). Le interazioni tra Key Vault e server tra tenant non sono supportate. Per lo stato di trasferimento delle risorse è quindi necessario riconfigurare la crittografia dei dati. Altre informazioni sullo stato di trasferimento delle risorse.
* La funzionalità di eliminazione temporanea deve essere abilitata nel Key Vault, per evitare la perdita di dati accidentali (o Key Vault). Le risorse eliminate temporaneamente vengono conservate per 90 giorni, a meno che non vengano ripristinate o eliminate dal cliente nel frattempo. Le azioni di ripristino e ripulitura hanno le proprie autorizzazioni associate in un criterio di accesso Key Vault. La funzionalità di eliminazione temporanea è disabilitata per impostazione predefinita e può essere abilitata tramite PowerShell o l'interfaccia della riga di comando. Non può essere abilitata tramite portale di Azure.
* Concedere al server singolo il database di Azure per PostgreSQL l'accesso al Key Vault con le autorizzazioni **Get, wrapKey, unwrapKey** usando la relativa identità gestita univoca. Quando si usa portale di Azure, l'identificazione univoca viene creata automaticamente quando la crittografia dei dati è abilitata nel server singolo PostgreSQL. Per istruzioni dettagliate sull'uso di portale di Azure, vedere [configurare la crittografia dei dati per il server singolo PostgreSQL](howto-data-encryption-portal.md) .

* Quando si usa il firewall con AKV, è necessario abilitare *l'opzione Consenti ai servizi Microsoft attendibili di ignorare il firewall*.

### <a name="requirements-for-configuring-customer-managed-key"></a>Requisiti per la configurazione della chiave gestita dal cliente

* La chiave gestita dal cliente da utilizzare per la crittografia della chiave di crittografia può essere solo asimmetrica, RSA 2028.
* La data di attivazione della chiave (se impostata) deve essere una data e un'ora nel passato. La data di scadenza (se impostata) deve essere una data e un'ora future.
* La chiave deve essere nello stato *abilitato* .
* Se si sta importando una chiave esistente nella Key Vault, assicurarsi di specificarla nei formati di file supportati (`.pfx`, `.byok``.backup`).

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>Suggerimenti per l'uso della crittografia dei dati tramite la chiave gestita dal cliente

### <a name="recommendation-for-configuring-akv"></a>Raccomandazione per la configurazione di AKV

* Impostare un blocco di risorsa sulla Key Vault per controllare gli utenti che possono eliminare questa risorsa critica e impedire l'eliminazione accidentale o non autorizzata. Altre informazioni sui blocchi di risorse.
* Abilitare il controllo e la creazione di report per tutte le chiavi di crittografia: Key Vault fornisce log facili da inserire in altri strumenti di gestione degli eventi e delle informazioni di sicurezza. Il Log Analytics di monitoraggio di Azure è un esempio di servizio già integrato.

* Verificare che il Key Vault e il server singolo del database di Azure per PostgreSQL si trovino nella stessa area per garantire un accesso più rapido per le operazioni di wrapping/Unwrap di decrittografia.

### <a name="recommendation-for-configuring-customer-managed-key"></a>Raccomandazione per la configurazione della chiave gestita dal cliente

* Conservazione di una copia della chiave gestita dal cliente (KEK) in una posizione sicura o di deposito al servizio di deposito vincolato.

* Se la chiave viene generata nella Key Vault, creare un backup della chiave prima di usare la chiave in AKV per la prima volta. Il backup può essere ripristinato solo in un Azure Key Vault. Altre informazioni sul comando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) .

## <a name="inaccessible-customer-managed-key-condition"></a>Condizione della chiave gestita dal cliente non accessibile

Quando la crittografia dei dati viene configurata con la chiave gestita dal cliente nel Azure Key Vault (AKV), è necessario l'accesso continuo a questa chiave affinché il server resti online. Se il server perde l'accesso alla chiave gestita dal cliente in AKV, entro 10 minuti il server inizierà a negare tutte le connessioni con il messaggio di errore corrispondente e a impostare lo stato del server su **inaccessibile**. L'unica azione consentita su un database nello stato inaccessibile è l'eliminazione.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Revoca accidentale dell'accesso alla chiave dal Azure Key Vault (AKV)

Potrebbe verificarsi che un utente con diritti di accesso sufficienti per il Key Vault Disabilita accidentalmente l'accesso al server alla chiave da:

* revoca delle autorizzazioni Get, wrapKey, unwrapKey del Key Vault dal server
* eliminazione della chiave
* eliminazione del Key Vault
* modifica delle regole del firewall di Key Vault

* eliminazione dell'identità gestita del server in Azure Active Directory

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Monitoraggio della chiave gestita dal cliente nel Key Vault

Per monitorare lo stato del database e abilitare gli avvisi per la perdita dell'accesso con protezione Transparent Data Encryption, configurare le funzionalità di Azure seguenti:

* [Integrità risorse di Azure](../service-health/resource-health-overview.md) : un database non accessibile che ha perso l'accesso alla chiave del cliente viene visualizzato come "inaccessibile" dopo che è stata negata la prima connessione al database.
* [Log attività](../service-health/alerts-activity-log-service-notifications.md) : quando l'accesso alla chiave del cliente nel Key Vault gestito dal cliente ha esito negativo, le voci vengono aggiunte al log attività. La creazione di avvisi per questi eventi consente di ripristinare l'accesso appena possibile.

* I [gruppi di azioni](../azure-monitor/platform/action-groups.md) possono essere definiti per inviare notifiche e avvisi in base alle proprie preferenze, ad esempio posta elettronica/SMS/push/voce, app per la logica, webhook, ITSM o Runbook di automazione.

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Ripristinare e replica con la chiave gestita del cliente nel Key Vault

Una volta che un server singolo di database di Azure per PostgreSQL è crittografato con la chiave gestita del cliente archiviata nel Key Vault, qualsiasi copia appena creata del server (anche se l'operazione locale o di ripristino geografico o tramite le repliche di lettura) viene crittografata con la stessa chiave gestita del cliente. Tuttavia, possono essere modificati per riflettere la chiave gestita del nuovo cliente per la crittografia. Quando la chiave gestita dal cliente viene modificata, i backup obsoleti del server inizieranno a usare la chiave più recente.

Per evitare problemi durante la configurazione della crittografia dei dati gestita dal cliente durante il ripristino o la creazione della replica di lettura, è importante attenersi alla procedura seguente nel server master e nei ripristini/replica:

* Avviare il processo di creazione della replica di ripristino o di lettura dal database master di Azure per il server singolo PostgreSQL.
* Il server appena creato (ripristinato/replica) viene mantenuto uno stato inaccessibile perché per l'identità univoca non sono state ancora concesse le autorizzazioni per la Azure Key Vault (AKV)
* Nel server ripristinato/di replica, rivalidare la chiave gestita dal cliente nelle impostazioni di crittografia dei dati per assicurarsi che al server appena creato siano assegnate le autorizzazioni a capo/annullamento del wrapping per la chiave archiviata in AKV.

* Per assicurarsi che la crittografia dei dati venga mantenuta nel database master e nel server di replica ripristinato, è necessario eseguire entrambe le operazioni descritte in precedenza.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare la crittografia dei dati con la chiave gestita dal cliente per il server singolo del database di Azure per PostgreSQL usando il portale di Azure](howto-data-encryption-portal.md).
