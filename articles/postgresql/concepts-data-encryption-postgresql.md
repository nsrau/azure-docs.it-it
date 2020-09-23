---
title: Crittografia dei dati con chiave gestita dal cliente - Database di Azure per PostgreSQL - Server singolo
description: La crittografia dei dati per server singolo di Database di Azure per PostgreSQL con una chiave gestita dal cliente consente di usare il modello Bring Your Own Key (BYOK) per la protezione dei dati inattivi. Consente anche alle organizzazioni di implementare la separazione dei compiti nella gestione delle chiavi e dei dati.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 1be04c0617dc4ed235cc3f3bc29aa58f4c2cb1d2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902139"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Crittografia dei dati per server singolo di Database di Azure per PostgreSQL con una chiave gestita dal cliente

La crittografia dei dati con chiavi gestite dal cliente per server singolo di Database di Azure per PostgreSQL consente di usare il modello Bring Your Own Key (BYOK) per la protezione dei dati inattivi. Consente anche alle organizzazioni di implementare la separazione dei compiti nella gestione delle chiavi e dei dati. Con la crittografia gestita dal cliente, l'utente è responsabile e, in pieno controllo, del ciclo di vita di una chiave, delle autorizzazioni di utilizzo delle chiavi e del controllo delle operazioni sulle chiavi.

La crittografia dei dati con chiavi gestite dal cliente per server singolo di Database di Azure per PostgreSQL viene impostata a livello di server. Per un determinato server, una chiave gestita dal cliente, detta chiave KEK (Key Encryption Key), viene usata per crittografare la chiave DEK (Data Encryption Key) usata dal servizio. La chiave KEK è una chiave asimmetrica archiviata in un'istanza di [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) di proprietà del cliente e gestita dal cliente. Le chiavi KEK e DEK vengono descritte in maggior dettaglio più avanti in questo articolo.

Key Vault è un sistema esterno di gestione delle chiavi basato sul cloud. Offre disponibilità elevata e fornisce una risorsa di archiviazione scalabile e sicura per le chiavi di crittografia RSA, supportata facoltativamente da moduli di protezione hardware (HSM) con convalida di tipo FIPS 140-2 Livello 2. Non consente l'accesso diretto a una chiave archiviata, ma offre servizi di crittografia e decrittografia per le entità autorizzate. Key Vault può generare la chiave, la può importare o può fare in modo che venga [trasferita da un dispositivo HMS locale](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui il server singolo di Database di Azure per PostgreSQL supporta i piani tariffari di server per utilizzo generico e ottimizzati per la memoria. Per altre limitazioni, vedere la sezione [limitazione](concepts-data-encryption-postgresql.md#limitations) .

## <a name="benefits"></a>Vantaggi

La crittografia dei dati con chiavi gestite dal cliente per il server singolo di database di Azure per PostgreSQL offre i vantaggi seguenti:

* Accesso ai dati completamente controllato dall'utente, grazie alla possibilità di rimuovere la chiave e rendere inaccessibile il database 
*    Controllo completo del ciclo di vita della chiave, inclusa la rotazione della chiave per soddisfare i criteri aziendali
*    Gestione centralizzata e organizzazione delle chiavi in Azure Key Vault
*    Possibilità di implementare la separazione dei compiti tra i responsabili della sicurezza, gli amministratori di database e gli amministratore di sistema

## <a name="terminology-and-description"></a>Terminologia e descrizione

**Chiave DEK (Data Encryption Key)** : una chiave AES256 simmetrica usata per crittografare una partizione o un blocco di dati. La crittografia di ogni blocco di dati con una chiave diversa rende più complessi gli attacchi di crittoanalisi. È necessario l'accesso alle chiavi DEK per il provider di risorse o l'istanza dell'applicazione che esegue la crittografia e la decrittografia di un blocco specifico. Quando una chiave DEK viene sostituita con una nuova chiave, è necessario ripetere la crittografia con la nuova chiave solo per i dati nel blocco associato.

**Chiave KEK (Key Encryption Key)** : una chiave di crittografia usata per crittografare le chiavi DEK. Una chiave KEK che non lascia mai Key Vault consente la crittografia e il controllo delle chiavi DEK. L'entità che ha accesso alla chiave KEK può essere diversa da quella che richiede la chiave DEK. Poiché è necessaria la chiave KEK per decrittografare le chiavi DEK, la chiave KEK è di fatto un singolo punto che consente di eliminare in modo efficace le chiavi DEK eliminando la chiave KEK.

Le chiavi DEK, crittografate con chiavi KEK, vengono archiviate separatamente. Solo un'entità con accesso alla chiave KEK può decrittografare queste chiavi DEK. Per altre informazioni, vedere [Sicurezza della crittografia di dati inattivi](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Funzionamento della crittografia dei dati con una chiave gestita dal cliente

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png" alt-text="Diagramma che mostra una panoramica di Bring Your Own Key":::

Affinché un server PostgreSQL usi chiavi gestite dal cliente archiviate in Key Vault per la crittografia della chiave DEK, un amministratore di Key Vault concede al server i diritti di accesso seguenti:

* **get**: per recuperare la parte pubblica e le proprietà della chiave nell'insieme di credenziali delle chiavi.
* **wrapKey**: per poter crittografare la chiave DEK. La crittografia crittografata è archiviata nel database di Azure per PostgreSQL.
* **unwrapKey**: per essere in grado di decrittografare la chiave DEK. Per crittografare/decrittografare i dati, il database di Azure per PostgreSQL necessita della crittografia decrittografata

L'amministratore dell'insieme di credenziali delle chiavi può anche [abilitare la registrazione degli eventi di controllo di Key Vault](../azure-monitor/insights/key-vault-insights-overview.md), in modo che possano essere controllati in un secondo momento.

Se il server è configurato per l'uso della chiave gestita dal cliente archiviata nell'insieme di credenziali delle chiavi, il server invia la chiave DEK all'insieme di credenziali delle chiavi per la crittografia. Key Vault restituisce la chiave DEK crittografata, che viene archiviata nel database utente. Analogamente, se necessario, il server invia la chiave DEK protetta all'insieme di credenziali delle chiavi per la decrittografia. I revisori possono usare Monitoraggio di Azure per esaminare i log degli eventi di controllo di Key Vault, se la registrazione è abilitata.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Requisiti per la configurazione della crittografia dei dati per il server singolo di Database di Azure per PostgreSQL

Di seguito sono riportati i requisiti per la configurazione di Key Vault:

* Key Vault e il server singolo di Database di Azure per PostgreSQL devono appartenere allo stesso tenant di Azure Active Directory (Azure AD). Le interazioni di server e Key Vault tra più tenant non sono supportate. Per lo stato di trasferimento della risorsa Key Vault successivamente è necessario riconfigurare la crittografia dei dati.
* Abilitare la funzionalità di eliminazione temporanea nell'insieme di credenziali delle chiavi per evitare la perdita di dati in caso di eliminazione accidentale della chiave (o di Key Vault). Le risorse eliminate temporaneamente vengono conservate per 90 giorni, a meno che nel frattempo non vengano recuperate o rimosse definitivamente dall'utente. Alle azioni di recupero e rimozione definitiva sono associate autorizzazioni specifiche nei criteri di accesso di Key Vault. La funzionalità di eliminazione temporanea è disattivata per impostazione predefinita, ma è possibile abilitarla tramite PowerShell o con l'interfaccia della riga di comando di Azure (si noti che non è possibile abilitarla tramite il portale di Azure).
* Concedere al server singolo di Database di Azure per PostgreSQL l'accesso all'insieme di credenziali delle chiavi con le autorizzazioni get, wrapKey e unwrapKey usando l'identità gestita univoca. Nel portale di Azure, l'identità' servizio ' univoca viene creata automaticamente quando la crittografia dei dati è abilitata nel server singolo PostgreSQL. Per le istruzioni dettagliate nel caso si usi il portale di Azure, vedere [Crittografia dei dati per server singolo di Database di Azure per PostgreSQL tramite il portale di Azure](howto-data-encryption-portal.md).

Di seguito sono riportati i requisiti per la configurazione della chiave gestita dal cliente:

* La chiave gestita dal cliente da usare per la crittografia della chiave DEK può essere solo di tipo RSA 2048 e asimmetrica.
* La data di attivazione della chiave (se impostata) deve essere una data/ora nel passato. La data di scadenza (se impostata) deve essere una data/ora nel futuro.
* La chiave deve avere lo stato *Abilitato*.
* Se si sta [importando una chiave esistente](https://docs.microsoft.com/rest/api/keyvault/ImportKey/ImportKey) nell'insieme di credenziali delle chiavi, assicurarsi di specificarla nei formati di file supportati ( `.pfx` , `.byok` , `.backup` ).

## <a name="recommendations"></a>Consigli

Se si usa la crittografia dei dati con una chiave gestita dal cliente, ecco le raccomandazioni per la configurazione di Key Vault:

* Impostare un blocco della risorsa in Key Vault per controllare chi può eliminare questa risorsa critica e impedire l'eliminazione accidentale o non autorizzata.
* Abilitare il controllo e la creazione di report per tutte le chiavi di crittografia. Key Vault include log che possono essere facilmente inseriti in altri strumenti di informazioni di sicurezza e gestione degli eventi (SIEM, Security Information and Event Management). Log Analytics di Monitoraggio di Azure è un esempio di un servizio già integrato.
* Assicurarsi che Key Vault e il server singolo di Database di Azure per PostgreSQL risiedano nella stessa area, per garantire un accesso più rapido per le operazioni wrapping e annullamento del wrapping della chiave DEK.
* Bloccare Azure KeyVault per limitarne l'accesso solo **all'endpoint privato e alle reti selezionate** e consentire solo a servizi *Microsoft attendibili* di proteggere le risorse.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="Servizio attendibile con Azure Key Vault":::

Ecco le raccomandazioni per la configurazione di una chiave gestita dal cliente:

* Conservare una copia della chiave gestita dal cliente in un luogo sicuro o inserirla nel servizio di deposito.

* Se Key Vault genera la chiave, crearne un backup prima di usarla per la prima volta. Il backup può essere ripristinato solo in Key Vault. Per altre informazioni sul comando di backup, vedere [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condizione inaccessibile della chiave gestita dal cliente

Quando si configura la crittografia dei dati con una chiave gestita dal cliente in Key Vault, è necessario l'accesso continuo a questa chiave affinché il server resti online. Se perde l'accesso alla chiave gestita dal cliente in Key Vault, il server inizia a negare tutte le connessioni entro 10 minuti. Il server genera un messaggio di errore corrispondente e cambia il proprio stato in *Inaccessibile*. Ecco alcuni motivi per cui il server può raggiungere questo stato:

* Se si crea un server di ripristino temporizzato per il server singolo di Database di Azure per PostgreSQL, per cui è abilitata la crittografia dei dati, il server appena creato avrà lo stato *Inaccessibile*. È possibile correggere lo stato del server tramite il [portale di Azure](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) o l'[interfaccia della riga di comando](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Se si crea una replica in lettura per il server singolo di Database di Azure per PostgreSQL, per cui è abilitata la crittografia dei dati, il server di replica avrà lo stato *Inaccessibile*. È possibile correggere lo stato del server tramite il [portale di Azure](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) o l'[interfaccia della riga di comando](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Se si elimina l'istanza di KeyVault, il server singolo di Database di Azure per PostgreSQL non potrà accedere alla chiave e passerà allo stato *Inaccessibile*. Recuperare [Key Vault](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects) e ripetere la convalida della crittografia dei dati per rendere il server *Disponibile*.
* Se si elimina la chiave da KeyVault, il server singolo di Database di Azure per PostgreSQL non potrà accedere alla chiave e passerà allo stato *Inaccessibile*. Recuperare la[chiave](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects) e ripetere la convalida della crittografia dei dati per rendere il server *Disponibile*.
* Se la chiave archiviata in Azure KeyVault scade, diventa non valida e il server singolo di Database di Azure per PostgreSQL passerà allo stato *Inaccessibile*. Estendere la data di scadenza della chiave usando l'[interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-set-attributes) e ripetere la convalida della crittografia dei dati per rendere il server *Disponibile*.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Revoca accidentale dell'accesso alla chiave da Key Vault

È possibile che un utente con diritti di accesso sufficienti per Key Vault disabiliti accidentalmente l'accesso del server alla chiave eseguendo le operazioni seguenti:

* Revoca delle autorizzazioni get, wrapKey e unwrapKey dell'insieme di credenziali delle chiavi dal server.
* Eliminazione della chiave.
* Eliminazione dell'insieme di credenziali delle chiavi.
* Modifica delle regole del firewall dell'insieme di credenziali delle chiavi.

* Eliminazione dell'identità gestita del server in Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorare la chiave gestita dal cliente in Key Vault

Per monitorare lo stato del database e abilitare gli avvisi per la perdita dell'accesso alla protezione di Transparent Data Encryption, configurare le funzionalità di Azure seguenti:

* [Integrità risorse di Azure](../service-health/resource-health-overview.md): un database inaccessibile che ha perso l'accesso alla chiave del cliente viene indicato come "Inaccessibile" dopo che viene negata la prima connessione al database.
* [Log attività](../service-health/alerts-activity-log-service-notifications.md): quando l'accesso alla chiave del cliente nell'istanza di Key Vault gestita dal cliente non riesce, nel log attività vengono aggiunte voci. Se si creano avvisi per questi eventi, è possibile ripristinare l'accesso tempestivamente.

* [Gruppi di azioni](../azure-monitor/platform/action-groups.md): definire questi gruppi per l'invio di notifiche e avvisi in base alle preferenze.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Eseguire il ripristino e la replica con una chiave gestita del cliente in Key Vault

Una volta crittografato il server singolo di Database di Azure per PostgreSQL con una chiave gestita dal cliente archiviata in Key Vault, viene crittografata anche qualsiasi nuova copia creata del server. Questa nuova copia può essere creata tramite un'operazione di ripristino locale o geografico oppure tramite repliche in lettura. Tuttavia, la copia può essere modificata in modo da riflettere una nuova chiave gestita dal cliente per la crittografia. Quando la chiave gestita dal cliente cambia, i backup precedenti del server iniziano a usare la chiave più recente.

Per evitare problemi con la configurazione della crittografia dei dati gestita dal cliente durante il ripristino o la creazione della replica in lettura, è importante seguire questa procedura nei server master e di ripristino/replica:

* Avviare il processo di ripristino o di creazione della replica in lettura dal server singolo master di Database di Azure per PostgreSQL.
* Mantenere il server appena creato (ripristinato/replica) in uno stato inaccessibile, perché la relativa identità univoca non ha ancora ricevuto le autorizzazioni per Key Vault.
* Nel server ripristinato/replica ripetere la convalida della chiave gestita dal cliente nelle impostazioni di crittografia dei dati. In questo modo ci si assicura che il server appena creato riceva autorizzazioni di wrapping e annullamento del wrapping per la chiave archiviata in Key Vault.

## <a name="limitations"></a>Limitazioni

Per database di Azure per PostgreSQL, il supporto per la crittografia dei dati inattivi con la chiave gestita dei clienti (CMK) presenta alcune limitazioni:

* Il supporto per questa funzionalità è limitato ai piani tariffari **per utilizzo generico** e con ottimizzazione per la **memoria** .
* Questa funzionalità è supportata solo nelle aree e sui server che supportano l'archiviazione fino a 16 TB. Per l'elenco delle aree di Azure che supportano l'archiviazione fino a 16TB, vedere la sezione archiviazione nella documentazione [qui](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Tutti i nuovi server PostgreSQL creati nelle aree elencate in precedenza, è **disponibile**il supporto per la crittografia con chiavi di gestione clienti. Il server ripristino temporizzato (punto nel tempo ripristinato) o la replica di lettura non verrà qualificata, sebbene in teoria siano ' New '.
    > - Per verificare se il server di cui è stato effettuato il provisioning supporta fino a 16TB, è possibile passare al pannello piano tariffario nel portale e visualizzare le dimensioni massime di archiviazione supportate dal server di cui è stato effettuato il provisioning. Se è possibile spostare il dispositivo di scorrimento fino a 4 TB, il server potrebbe non supportare la crittografia con chiavi gestite dal cliente. Tuttavia, i dati vengono crittografati in qualsiasi momento utilizzando le chiavi gestite dal servizio. Per AskAzureDBforPostgreSQL@service.microsoft.com eventuali domande, contattare il.

* La crittografia è supportata solo con la chiave crittografica RSA 2048.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare la crittografia dei dati con una chiave gestita dal cliente per il server singolo di Database di Azure per PostgreSQL usando il portale di Azure](howto-data-encryption-portal.md).

