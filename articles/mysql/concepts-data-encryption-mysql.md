---
title: Crittografia dei dati di database di Azure per MySQL con una chiave gestita dal cliente
description: La crittografia dei dati del database di Azure per MySQL con una chiave gestita dal cliente consente di Bring Your Own Key (BYOK) per la protezione dei dati inattivi. Consente inoltre alle organizzazioni di implementare la separazione dei compiti nella gestione delle chiavi e dei dati.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 552c6b5176babfc1a66f74f1bb6daa9370744b8f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210432"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Crittografia dei dati di database di Azure per MySQL con una chiave gestita dal cliente

> [!NOTE]
> A questo punto, è necessario richiedere l'accesso per usare questa funzionalità. A tale scopo, contattare AskAzureDBforMySQL@service.microsoft.com.

La crittografia dei dati con chiavi gestite dal cliente per database di Azure per MySQL ti permette di usare la tua chiave (BYOK) per la protezione dei dati inattivi. Consente inoltre alle organizzazioni di implementare la separazione dei compiti nella gestione delle chiavi e dei dati. Con la crittografia gestita dal cliente, l'utente è responsabile e, in un controllo completo, del ciclo di vita di una chiave, delle autorizzazioni di utilizzo delle chiavi e del controllo delle operazioni sulle chiavi.

La crittografia dei dati con chiavi gestite dal cliente per database di Azure per MySQL è impostata a livello di server. Per un determinato server, una chiave gestita dal cliente, denominata chiave di crittografia della chiave (KEK), viene usata per crittografare la chiave di crittografia dei dati usata dal servizio. KEK è una chiave asimmetrica archiviata in un'istanza di [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) gestita dal cliente e gestita dal cliente. La chiave di crittografia della chiave (KEK) e la chiave di crittografia dei dati sono descritte in modo più dettagliato più avanti in questo articolo.

Key Vault è un sistema di gestione delle chiavi esterne basato sul cloud. È a disponibilità elevata e fornisce un'archiviazione sicura e scalabile per le chiavi crittografiche RSA, supportata facoltativamente da moduli di protezione hardware convalidati FIPS 140-2 Level 2 (HSM). Non consente l'accesso diretto a una chiave archiviata, ma fornisce servizi di crittografia e decrittografia a entità autorizzate. Key Vault possibile generare la chiave, importarla o [trasferirla da un dispositivo HSM locale](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui database di Azure per MySQL supporta i piani tariffari "per utilizzo generico" e "con ottimizzazione per la memoria".

## <a name="benefits"></a>Vantaggi

La crittografia dei dati per database di Azure per MySQL offre i vantaggi seguenti:

* L'accesso ai dati è completamente controllato dalla possibilità di rimuovere la chiave e rendere inaccessibile il database 
* Controllo completo del ciclo di vita della chiave, inclusa la rotazione della chiave per l'allineamento con i criteri aziendali
* Gestione centralizzata e organizzazione delle chiavi in Azure Key Vault
* Possibilità di implementare la separazione dei compiti tra i responsabili della sicurezza e gli amministratori di database e di amministratore di sistema


## <a name="terminology-and-description"></a>Terminologia e descrizione

**Chiave di crittografia dei dati**: chiave AES256 simmetrica utilizzata per crittografare una partizione o un blocco di dati. La crittografia di ogni blocco di dati con una chiave diversa rende più complessi gli attacchi di crittoanalisi. È necessario l'accesso alle chiavi DEK per il provider di risorse o l'istanza dell'applicazione che esegue la crittografia e la decrittografia di un blocco specifico. Quando si sostituisce una chiave di crittografia con una nuova chiave, solo i dati nel blocco associato devono essere crittografati nuovamente con la nuova chiave.

Chiave di **crittografia della chiave (KEK)** : chiave di crittografia usata per crittografare chiavi DEK. Un KEK che non lascia mai Key Vault consente la crittografia e il controllo del chiavi DEK. L'entità che ha accesso alla chiave KEK potrebbe essere diversa da quella che richiede la chiave di crittografia. Poiché è necessaria la chiave KEK per decrittografare le chiavi DEK, la chiave KEK è di fatto un singolo punto che consente di eliminare in modo efficace le chiavi DEK eliminando la chiave KEK.

Il chiavi DEK, crittografato con KEKs, viene archiviato separatamente. Solo un'entità con accesso a KEK può decrittografare questi chiavi DEK. Per altre informazioni, vedere [sicurezza in crittografia](../security/fundamentals/encryption-atrest.md)dati inattivi.

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Funzionamento della crittografia dei dati con una chiave gestita dal cliente

![Diagramma che mostra una panoramica di Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Affinché un server MySQL utilizzi chiavi gestite dal cliente archiviate in Key Vault per la crittografia della chiave di crittografia, un amministratore di Key Vault concede al server i seguenti diritti di accesso:

* **Get**: per il recupero della parte pubblica e delle proprietà della chiave nell'insieme di credenziali delle chiavi.
* **wrapKey**: per poter crittografare la chiave di crittografia.
* **unwrapKey**: per poter decrittografare la chiave di crittografia.

L'amministratore dell'insieme di credenziali delle chiavi può anche [abilitare la registrazione degli eventi di controllo Key Vault](../azure-monitor/insights/azure-key-vault.md), in modo che possano essere controllati in un secondo momento.

Quando il server è configurato per l'uso della chiave gestita dal cliente archiviata nell'insieme di credenziali delle chiavi, il server invia la chiave di crittografia all'insieme di credenziali delle chiavi per le crittografie. Key Vault restituisce la chiave di crittografia crittografata, archiviata nel database utente. Analogamente, quando necessario, il server invia la chiave di crittografia protetta all'insieme di credenziali delle chiavi per la decrittografia. I revisori possono usare monitoraggio di Azure per esaminare i registri eventi di controllo Key Vault, se la registrazione è abilitata.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Requisiti per la configurazione della crittografia dei dati per database di Azure per MySQL

Di seguito sono riportati i requisiti per la configurazione di Key Vault:

* Key Vault e database di Azure per MySQL devono appartenere allo stesso tenant di Azure Active Directory (Azure AD). Non sono supportate le interazioni tra Key Vault e server tra tenant. Per lo stato di trasferimento delle risorse è quindi necessario riconfigurare la crittografia dei dati.
* È necessario abilitare la funzionalità di eliminazione temporanea nell'insieme di credenziali delle chiavi, in modo da evitare la perdita di dati in caso di eliminazione accidentale di chiavi (o Key Vault). Le risorse eliminate temporaneamente vengono conservate per 90 giorni, a meno che non vengano ripristinate o eliminate nel frattempo dall'utente. Le azioni di ripristino e ripulitura hanno le proprie autorizzazioni associate in un criterio di accesso Key Vault. La funzionalità di eliminazione temporanea è disattivata per impostazione predefinita, ma è possibile abilitarla tramite PowerShell o l'interfaccia della riga di comando di Azure (si noti che non è possibile abilitarla tramite il portale di Azure).
* Concedere l'accesso al database di Azure per MySQL all'insieme di credenziali delle chiavi con le autorizzazioni Get, wrapKey e unwrapKey usando l'identità gestita univoca. Nel portale di Azure, l'identità univoca viene creata automaticamente quando la crittografia dei dati è abilitata in MySQL. Per istruzioni dettagliate, vedere [configurare la crittografia dei dati per MySQL](howto-data-encryption-portal.md) quando si usa il portale di Azure.

* Quando si usa un firewall con Key Vault, è necessario abilitare l'opzione **Consenti ai servizi Microsoft attendibili di ignorare il firewall**.

Di seguito sono riportati i requisiti per la configurazione della chiave gestita dal cliente:

* La chiave gestita dal cliente da utilizzare per la crittografia della chiave di crittografia può essere solo asimmetrica, RSA 2028.
* La data di attivazione della chiave (se impostata) deve essere una data e un'ora nel passato. La data di scadenza (se impostata) deve essere una data e un'ora future.
* La chiave deve essere nello stato *abilitato* .
* Se si sta importando una chiave esistente nell'insieme di credenziali delle chiavi, assicurarsi di specificarla nei formati di file supportati (`.pfx`, `.byok``.backup`).

## <a name="recommendations"></a>Consigli

Quando si usa la crittografia dei dati usando una chiave gestita dal cliente, di seguito sono riportati i consigli per la configurazione di Key Vault:

* Impostare un blocco di risorsa su Key Vault per controllare gli utenti che possono eliminare questa risorsa critica e impedire l'eliminazione accidentale o non autorizzata.
* Abilitare il controllo e la creazione di report per tutte le chiavi di crittografia. Key Vault fornisce log facili da inserire in altri strumenti di gestione degli eventi e delle informazioni di sicurezza. Il Log Analytics di monitoraggio di Azure è un esempio di servizio già integrato.

* Assicurarsi che Key Vault e database di Azure per MySQL si trovino nella stessa area, per garantire un accesso più rapido alle operazioni di wrapping e unwrap di decrittografia.

Ecco alcuni suggerimenti per la configurazione di una chiave gestita dal cliente:

* Conserva una copia della chiave gestita dal cliente in un luogo sicuro o la assegna al servizio escrow.

* Se Key Vault genera la chiave, creare un backup della chiave prima di usare la chiave per la prima volta. Il backup può essere ripristinato solo Key Vault. Per ulteriori informazioni sul comando backup, vedere [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condizione della chiave gestita dal cliente non accessibile

Quando si configura la crittografia dei dati con una chiave gestita dal cliente in Key Vault, è necessario l'accesso continuo a questa chiave affinché il server resti online. Se il server perde l'accesso alla chiave gestita dal cliente in Key Vault, il server inizia a negare tutte le connessioni entro 10 minuti. Il server genera un messaggio di errore corrispondente e lo stato del server diventa *inaccessibile*. L'unica azione consentita su un database in questo stato è eliminarla.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Revoca accidentale dell'accesso alla chiave da Key Vault

Potrebbe verificarsi che un utente con diritti di accesso sufficienti a Key Vault Disabilita accidentalmente l'accesso al server alla chiave da:

* Revoca delle autorizzazioni Get, wrapKey e unwrapKey dell'insieme di credenziali delle chiavi dal server.
* Eliminazione della chiave.
* Eliminazione dell'insieme di credenziali delle chiavi.
* Modifica delle regole del firewall del Key Vault.

* Eliminazione dell'identità gestita del server in Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorare la chiave gestita dal cliente in Key Vault

Per monitorare lo stato del database e per abilitare gli avvisi per la perdita dell'accesso trasparente alla protezione della crittografia dei dati, configurare le funzionalità di Azure seguenti:

* [Integrità risorse di Azure](../service-health/resource-health-overview.md): un database inaccessibile che ha perso l'accesso alla chiave cliente viene visualizzato come "inaccessibile" dopo che è stata negata la prima connessione al database.
* [Log attività](../service-health/alerts-activity-log-service-notifications.md): quando l'accesso alla chiave del cliente nel Key Vault gestito dal cliente ha esito negativo, le voci vengono aggiunte al log attività. È possibile ripristinare l'accesso appena possibile, se si creano avvisi per questi eventi.

* [Gruppi di azione](../azure-monitor/platform/action-groups.md): definire questi per inviare notifiche e avvisi in base alle proprie preferenze.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Eseguire il ripristino e la replica con una chiave gestita del cliente in Key Vault

Dopo che il database di Azure per MySQL è stato crittografato con una chiave gestita del cliente archiviata in Key Vault, viene crittografata anche qualsiasi copia appena creata del server. Questa nuova copia può essere eseguita tramite un'operazione di ripristino locale o geografica oppure tramite le repliche di lettura. Tuttavia, la copia può essere modificata in modo da riflettere la chiave gestita di un nuovo cliente per la crittografia. Quando la chiave gestita dal cliente viene modificata, i backup precedenti del server iniziano a usare la chiave più recente.

Per evitare problemi durante la configurazione della crittografia dei dati gestita dal cliente durante il ripristino o la creazione della replica, è importante attenersi alla procedura seguente nei server master e di ripristino/replica:

* Avviare il processo di creazione della replica di ripristino o di lettura dal database master di Azure per MySQL.
* Mantieni il server appena creato (ripristinato/replica) in uno stato inaccessibile, perché per l'identità univoca non sono state ancora concesse le autorizzazioni per Key Vault.
* Nel server ripristinato/replica, rivalidare la chiave gestita dal cliente nelle impostazioni di crittografia dei dati. In questo modo si garantisce che al server appena creato vengano assegnate le autorizzazioni di wrapping e unwrap alla chiave archiviata in Key Vault.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare la crittografia dei dati con una chiave gestita dal cliente per il database di Azure per MySQL usando il portale di Azure](howto-data-encryption-portal.md).
