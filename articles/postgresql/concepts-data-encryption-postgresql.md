---
title: Crittografia dei dati con chiave gestita dal cliente - Database di Azure per PostgreSQL - Server singoloData encryption with customer-managed key - Azure Database for PostgreSQL - Single server
description: Database di Azure per la crittografia dei dati del server singolo PostgreSQL con una chiave gestita dal cliente consente di portare la propria chiave (BYOK) per la protezione dei dati inattivi. Consente inoltre alle organizzazioni di implementare la separazione dei compiti nella gestione di chiavi e dati.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 20e01e681c382e3c9c69f76c95a90f709f409d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297023"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Database di Azure per La crittografia dei dati del server singolo con una chiave gestita dal clienteAzure Database for PostgreSQL Single server data encryption with a customer-managed key

> [!NOTE]
> A questo punto, è necessario richiedere l'accesso per utilizzare questa funzionalità. A tale scopo, contattare AskAzureDBforPostgreSQL@service.microsoft.com.

La crittografia dei dati con chiavi gestite dal cliente per il database di Azure per PostgreSQL Singolo server consente di portare la propria chiave (BYOK) per la protezione dei dati inattivi. Consente inoltre alle organizzazioni di implementare la separazione dei compiti nella gestione di chiavi e dati. Con la crittografia gestita dal cliente, l'utente è responsabile e in un controllo completo del ciclo di vita di una chiave, delle autorizzazioni di utilizzo delle chiavi e del controllo delle operazioni sulle chiavi.

La crittografia dei dati con chiavi gestite dal cliente per il database di Azure per PostgreSQL singolo server è impostata a livello di server. Per un determinato server, una chiave gestita dal cliente, denominata chiave di crittografia della chiave (KEK), viene utilizzata per crittografare la chiave di crittografia dei dati (DEK) utilizzata dal servizio. KEK è una chiave asimmetrica archiviata in un'istanza di [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) di proprietà del cliente e gestita dal cliente. La chiave di crittografia della chiave (KEK) e la chiave DEK (Data Encryption Key) sono descritte in modo più dettagliato più avanti in questo articolo.

Key Vault è un sistema di gestione delle chiavi esterno basato su cloud. È a disponibilità elevata e fornisce archiviazione scalabile e sicura per le chiavi crittografiche RSA, facoltativamente supportata da archivi hardware convalidati FIPS 140-2 Level 2. Non consente l'accesso diretto a una chiave archiviata, ma fornisce servizi di crittografia e decrittografia alle entità autorizzate. Key Vault può generare la chiave, importarla o [trasferirla da un dispositivo HSM locale.](../key-vault/key-Vault-hsm-protected-keys.md)

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui il database di Azure per PostgreSQL Singolo server supporta i livelli dei prezzi "Scopo generale" e "Ottimizzazione memoria".

## <a name="benefits"></a>Vantaggi

La crittografia dei dati per il database di Azure per PostgreSQL Singolo server offre i vantaggi seguenti:Data encryption for Azure Database for PostgreSQL Single server provides the following benefits:

* L'accesso ai dati è completamente controllato dall'utente grazie alla possibilità di rimuovere la chiave e rendere il database inaccessibile 
*    Controllo completo del ciclo di vita delle chiavi, inclusa la rotazione della chiave per allinearsi ai criteri aziendali
*    Gestione e organizzazione centralizzate delle chiavi in Archiviazione delle chiavi di AzureCentral management and organization of keys in Azure Key Vault
*    Possibilità di implementare la separazione dei compiti tra i responsabili della sicurezza e DBA e gli amministratori di sistema

## <a name="terminology-and-description"></a>Terminologia e descrizione

Chiave di **crittografia dei dati (DEK):** chiave AES256 simmetrica utilizzata per crittografare una partizione o un blocco di dati. La crittografia di ogni blocco di dati con una chiave diversa rende più complessi gli attacchi di crittoanalisi. È necessario l'accesso alle chiavi DEK per il provider di risorse o l'istanza dell'applicazione che esegue la crittografia e la decrittografia di un blocco specifico. Quando si sostituisce una chiave DEK con una nuova chiave, solo i dati nel blocco associato devono essere nuovamente crittografati con la nuova chiave.

Chiave di **crittografia chiave (KEK):** chiave di crittografia utilizzata per crittografare i codici DEK. Una chiave che non esce mai da Key Vault consente agli stessi DEK di essere crittografati e controllati. L'entità che ha accesso alla chiave KEK potrebbe essere diversa dall'entità che richiede la chiave DEK. Poiché è necessaria la chiave KEK per decrittografare le chiavi DEK, la chiave KEK è di fatto un singolo punto che consente di eliminare in modo efficace le chiavi DEK eliminando la chiave KEK.

I file DEK, crittografati con le KS, vengono archiviati separatamente. Solo un'entità con accesso a KEK può decrittografare questi DEK. Per ulteriori informazioni, vedere [Sicurezza nella crittografia inattivi](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Funzionamento della crittografia dei dati con una chiave gestita dal cliente

![Diagramma che mostra una panoramica di Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

Affinché un server PostgreSQL utilizzi le chiavi gestite dal cliente archiviate nell'insieme di credenziali delle chiavi per la crittografia della chiave DEK, un amministratore di Key Vault concede i seguenti diritti di accesso al server:

* **get**: per recuperare la parte pubblica e le proprietà della chiave nell'insieme di credenziali delle chiavi.
* **wrapKey**: Per poter crittografare il file DEK.
* **unwrapKey**: Per poter decrittografare il file DEK.

L'amministratore dell'insieme di credenziali delle chiavi può inoltre [abilitare la registrazione degli eventi di controllo dell'insieme](../azure-monitor/insights/azure-key-vault.md)di credenziali delle chiavi, in modo che possano essere controllati in un secondo momento.

Quando il server è configurato per utilizzare la chiave gestita dal cliente archiviata nell'insieme di credenziali delle chiavi, il server invia la chiave DEK all'insieme di credenziali delle chiavi per le crittografie. Key Vault restituisce la chiave DEK crittografata, che viene archiviata nel database utente. Analogamente, quando necessario, il server invia la chiave DEK protetta all'insieme di credenziali delle chiavi per la decrittografia. I revisori possono usare Monitoraggio di Azure per esaminare i log degli eventi di controllo dell'insieme di credenziali delle chiavi, se la registrazione è abilitata.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Requisiti per la configurazione della crittografia dei dati per il database di Azure per PostgreSQL singolo serverRequirements for configuring data encryption for Azure Database for PostgreSQL Single server

Di seguito sono riportati i requisiti per la configurazione dell'insieme di credenziali delle chiavi:

* Key Vault e Azure Database for PostgreSQL Singolo server deve appartenere allo stesso tenant di Azure Active Directory (Azure AD). L'insieme di credenziali delle chiavi tra tenant e le interazioni con il server non sono supportati. Lo spostamento delle risorse in seguito richiede la riconfigurazione della crittografia dei dati.
* È necessario abilitare la funzionalità di eliminazione temporanea nell'insieme di credenziali delle chiavi, per proteggersi dalla perdita di dati in caso di eliminazione accidentale della chiave (o dell'insieme di credenziali delle chiavi). Le risorse eliminate temporaneamente vengono conservate per 90 giorni, a meno che l'utente non le recuperi o le elimini nel frattempo. Le azioni di ripristino ed eliminazione dispongono di autorizzazioni proprie associate in un criterio di accesso dell'insieme di credenziali delle chiavi. La funzionalità di eliminazione temporanea è disattivata per impostazione predefinita, ma è possibile abilitarla tramite PowerShell o l'interfaccia della riga di comando di Azure (si noti che non è possibile abilitarla tramite il portale di Azure).
* Concedere al database di Azure per PostgreSQL l'accesso al server singolo all'insieme di credenziali delle chiavi con le autorizzazioni get, wrapKey e unwrapKey usando l'identità gestita univoca. Nel portale di Azure l'identità univoca viene creata automaticamente quando la crittografia dei dati è abilitata nel server Singolo PostgreSQL.In the Azure portal, the unique identity is automatically created when data encryption is enabled on the PostgreSQL Single server. Vedere Crittografia dei dati per il database di Azure per PostgreSQL Singolo server usando il portale di Azure per istruzioni dettagliate e dettagliate quando si usa il portale di Azure.See Data encryption for [Azure Database for PostgreSQL Single server by using the Azure portal](howto-data-encryption-portal.md) for detailed, step-by-step instructions when you're using the Azure portal.

* Quando si utilizza un firewall con Key Vault, è necessario abilitare l'opzione **Consenti ai servizi Microsoft attendibili**di ignorare il firewall .

Di seguito sono riportati i requisiti per la configurazione della chiave gestita dal cliente:

* La chiave gestita dal cliente da utilizzare per la crittografia del codice DEK può essere solo asimmetrica, RSA 2028.
* La data di attivazione della chiave (se impostata) deve essere una data e un'ora nel passato. La data di scadenza (se impostata) deve essere una data e un'ora future.
* La chiave deve essere nello stato *Abilitato.*
* Se si importa una chiave esistente nell'insieme di credenziali delle chiavi,`.pfx` `.byok`assicurarsi di fornirla nei formati di file supportati ( , , ). `.backup`

## <a name="recommendations"></a>Consigli

Quando si usa la crittografia dei dati usando una chiave gestita dal cliente, ecco alcuni suggerimenti per la configurazione dell'insieme di credenziali delle chiavi:When you're using data encryption by using a customer-managed key, here are recommendations for configuring Key Vault:

* Impostare un blocco delle risorse sull'insieme di credenziali delle chiavi per controllare chi può eliminare questa risorsa critica e impedire l'eliminazione accidentale o non autorizzata.
* Abilitare il controllo e la creazione di report su tutte le chiavi di crittografia. Key Vault fornisce log facili da inserire in altre informazioni di sicurezza e strumenti di gestione degli eventi. Azure Monitor Log Analytics è un esempio di servizio già integrato.

* Assicurarsi che Key Vault e Azure Database per PostgreSQL Un singolo server risiedono nella stessa area, per garantire un accesso più rapido per le operazioni di wrapping e annullamento del wrapping DEK.

Di seguito sono riportati alcuni suggerimenti per la configurazione di una chiave gestita dal cliente:Here are recommendations for configuring a customer-managed key:

* Conservare una copia della chiave gestita dal cliente in un luogo sicuro o depositarla al servizio di deposito a garanzia.

* Se Key Vault genera la chiave, creare un backup della chiave prima di utilizzare la chiave per la prima volta. È possibile ripristinare il backup solo nell'insieme di credenziali delle chiavi. Per ulteriori informazioni sul comando backup, vedere [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condizioni di chiave gestite dal cliente inaccessibili

Quando si configura la crittografia dei dati con una chiave gestita dal cliente in Key Vault, è necessario l'accesso continuo a questa chiave affinché il server rimanga online. Se il server perde l'accesso alla chiave gestita dal cliente nell'insieme di credenziali delle chiavi, il server inizia a negare tutte le connessioni entro 10 minuti. Il server genera un messaggio di errore corrispondente e lo stato del server viene modificato in *Inaccessibile*. L'unica azione consentita in un database in questo stato è l'eliminazione.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Revoca accidentale dell'accesso alla chiave dall'insieme di credenziali delle chiavi

Può accadere che un utente con diritti di accesso sufficienti a Key Vault disattivi accidentalmente l'accesso del server alla chiave:

* Revocadelle autorizzazioni get, wrapKey e unwrapKey dell'insieme di credenziali delle chiavi dal server.
* Eliminazione della chiave.
* Eliminazione dell'insieme di credenziali delle chiavi.
* Modifica delle regole del firewall dell'insieme di credenziali delle chiavi.

* Eliminazione dell'identità gestita del server in Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorare la chiave gestita dal cliente in Key VaultMonitor the customer-managed key in Key Vault

Per monitorare lo stato del database e abilitare gli avvisi per la perdita dell'accesso trasparente alla protezione da crittografia dei dati, configurare le funzionalità di Azure seguenti:To monitor the database state, and to enable alerting for the loss of transparent data encryption encryptionor access, configure the following Azure features:

* [Integrità delle risorse di Azure:](../service-health/resource-health-overview.md)un database inaccessibile che ha perso l'accesso alla chiave del cliente viene visualizzato come "Inaccessibile" dopo che la prima connessione al database è stata negata.
* [Registro attività](../service-health/alerts-activity-log-service-notifications.md): quando l'accesso alla chiave del cliente nell'insieme di credenziali delle chiavi gestito dal cliente ha esito negativo, le voci vengono aggiunte al log attività. È possibile ripristinare l'accesso appena possibile, se si creano avvisi per questi eventi.

* [Gruppi di azioni](../azure-monitor/platform/action-groups.md): Definire questi per inviare notifiche e avvisi in base alle preferenze.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Ripristinare e replicare con la chiave gestita di un cliente nell'insieme di credenziali delle chiavi

Dopo che il database di Azure per PostgreSQL Singolo server viene crittografato con la chiave gestita di un cliente archiviata in Key Vault, viene crittografata anche qualsiasi copia appena creata del server. È possibile eseguire questa nuova copia tramite un'operazione di ripristino locale o geografico o tramite repliche di lettura. Tuttavia, la copia può essere modificata per riflettere la chiave gestita di un nuovo cliente per la crittografia. Quando la chiave gestita dal cliente viene modificata, i backup precedenti del server iniziano a utilizzare la chiave più recente.

Per evitare problemi durante l'impostazione della crittografia dei dati gestita dal cliente durante il ripristino o la creazione della replica di lettura, è importante eseguire la procedura seguente nei server master e ripristinati/replica:

* Avviare il processo di creazione della replica di ripristino o lettura dal database di Azure master per PostgreSQL singolo server.
* Mantenere il server appena creato (ripristinato/replica) in uno stato inaccessibile, perché la sua identità univoca non è ancora stata concessa le autorizzazioni per Key Vault.
* Nel server ripristinato/replica, riconvalidare la chiave gestita dal cliente nelle impostazioni di crittografia dei dati. In questo modo si garantisce che al server appena creato vengano concesse le autorizzazioni di wrapping e annullamento del wrapping per la chiave archiviata in Key Vault.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare la crittografia dei [dati con una chiave gestita dal cliente per il database di Azure per PostgreSQL Singolo server tramite il portale](howto-data-encryption-portal.md)di Azure.

