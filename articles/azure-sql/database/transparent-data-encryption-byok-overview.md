---
title: Transparent Data Encryption (TDE) gestita dal cliente
description: 'Supporto Bring Your Own Key (BYOK) per Transparent Data Encryption (Transparent Data Encryption) con Azure Key Vault per database SQL e Azure sinapsi Analytics. TDE con BYOK: panoramica, vantaggi, funzionamento, considerazioni ed elementi consigliati.'
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: cf0fec1f081a232abc88941e3dd785fb7617fb57
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387116"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL Transparent Data Encryption con chiave gestita dal cliente
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) con chiave gestita dal cliente abilita lo scenario Bring Your Own Key (BYOK) per la protezione dei dati inattivi e consente alle organizzazioni di separare i compiti di gestione di chiavi e dati. Con la funzionalità Transparent Data Encryption gestita dal cliente, il cliente ha il controllo completo ed è responsabile della gestione del ciclo di vita della chiave (creazione, caricamento, rotazione, eliminazione), delle autorizzazioni di utilizzo delle chiavi e del controllo delle operazioni sulle chiavi.

In questo scenario la chiave usata per la crittografia della chiave DEK (Database Encryption Key), chiamata protezione TDE, è una chiave asimmetrica gestita dal cliente archiviata in un [Azure Key Vault (AKV)](../../key-vault/general/secure-your-key-vault.md) di proprietà del cliente e gestito dal cliente, un sistema di gestione delle chiavi esterno basato sul cloud. Il Key Vault è un'archiviazione sicura a disponibilità elevata e scalabile per le chiavi crittografiche RSA, supportata facoltativamente da moduli di protezione hardware (HSM) con convalida di tipo FIPS 140-2 Livello 2. Non consente l'accesso diretto a una chiave archiviata, ma offre servizi di crittografia/decrittografia che usano la chiave nelle entità autorizzate. La chiave può essere generata dal Key Vault, importata o [trasferita nel Key Vault da un dispositivo HSM locale](../../key-vault/keys/hsm-protected-keys.md).

Per il database SQL di Azure e l'analisi delle sinapsi di Azure, la protezione Transparent Data Encryption è impostata a livello di server e viene ereditata da tutti i database crittografati associati a tale server. Per l'Istanza gestita di SQL di Azure, la protezione TDE è impostata a livello di istanza e viene ereditata da tutti i database crittografati nell'istanza. Il termine *Server* si riferisce sia a un server nel database SQL che a una sinapsi di Azure e a un'istanza gestita in SQL istanza gestita nell'intero documento, a meno che non venga specificato diversamente.

> [!IMPORTANT]
> Per gli utenti che usano Transparent Data Encryption gestita dal servizio e vogliono iniziare a usare Transparent Data Encryption gestita dal cliente, i dati rimangono crittografati durante il passaggio e non si verifica alcun tempo di inattività o riesecuzione della crittografia dei file di database. Il passaggio da una chiave gestita dal servizio a una chiave gestita dal cliente richiede la riesecuzione della crittografia della chiave DEK, che è un'operazione online rapida.

> [!NOTE]
> Per fornire ai clienti di Azure SQL due livelli di crittografia dei dati inattivi, viene implementata la crittografia dell'infrastruttura (usando l'algoritmo di crittografia AES-256) con chiavi gestite dalla piattaforma. Questo offre un livello aggiuntivo di crittografia dei componenti inattivi insieme a Transparent Data Encryption con chiavi gestite dal cliente, che è già disponibile. Al momento, i clienti devono richiedere l'accesso a questa funzionalità. Se si è interessati a questa funzionalità, contattare AzureSQLDoubleEncryptionAtRest@service.microsoft.com .

## <a name="benefits-of-the-customer-managed-tde"></a>Vantaggi di Transparent Data Encryption gestita dal cliente

Transparent Data Encryption gestita dal cliente offre al cliente i vantaggi seguenti:

- Controllo completo e granulare sull'utilizzo e sulla gestione della protezione TDE;

- Trasparenza dell'utilizzo della protezione TDE;

- Possibilità di implementare la separazione dei compiti nella gestione delle chiavi e dei dati all'interno dell'organizzazione;

- L'amministratore del Key Vault può revocare le autorizzazioni di accesso alle chiavi per rendere inaccessibile il database crittografato;

- Gestione centralizzata delle chiavi in AKV;

- Maggiore attendibilità per i clienti finali poiché AKV è progettato in modo che Microsoft non possa visualizzare né estrarre le chiavi di crittografia;

## <a name="how-customer-managed-tde-works"></a>Funzionamento di Transparent Data Encryption gestita dal cliente

![Installazione e funzionamento di Transparent Data Encryption gestita dal cliente](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

Per consentire al server di usare la protezione Transparent Data Encryption archiviata in AKV per la crittografia della chiave di crittografia, l'amministratore dell'insieme di credenziali delle chiavi deve concedere al server i seguenti diritti di accesso usando l'identità univoca Azure Active Directory (Azure AD):

- **get**: per recuperare la parte pubblica e le proprietà della chiave nel Key Vault

- **wrapKey**: per proteggere (crittografare) la chiave DEK

- **unwrapKey**: per rimuovere la protezione (decrittografare) la chiave DEK

L'amministratore del Key Vault può anche [abilitare la registrazione degli eventi di controllo del Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md), in modo che possano essere controllati in un secondo momento.

Quando il server è configurato per l'utilizzo della protezione TDE da AKV, il server invia la chiave DEK di ogni database abilitato per TDE al Key Vault per la crittografia. Il Key Vault restituisce la chiave DEK crittografata che viene quindi archiviata nel database utente.

Quando necessario, il server invia la chiave DEK protetta al Key Vault per la decrittografia.

Se la registrazione è abilitata, i revisori possono usare Monitoraggio di Azure per esaminare i log AuditEvent del Key Vault.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Requisiti per la configurazione di Transparent Data Encryption gestita dal cliente

### <a name="requirements-for-configuring-akv"></a>Requisiti per la configurazione di AKV

- Il Key Vault e il database SQL o l'istanza gestita devono appartenere allo stesso tenant di Azure Active Directory. Le interazioni tra tenant di insieme di credenziali delle chiavi e server non sono supportate. Per spostare le risorse in un secondo momento, è necessario riconfigurare Transparent Data Encryption con AKV. Altre informazioni sullo [spostamento di risorse](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

- È necessario abilitare la funzionalità di [eliminazione temporanea](../../key-vault/general/soft-delete-overview.md) nel Key Vault per evitare la perdita di dati in caso di eliminazione accidentale della chiave (o Key Vault). Le risorse eliminate temporaneamente vengono conservate per 90 giorni, a meno che non vengano recuperate o rimosse definitivamente dal cliente. Alle azioni di *recupero* e *pulizia* sono associate autorizzazioni specifiche nei criteri di accesso dell'insieme di credenziali delle chiavi. La funzionalità di eliminazione temporanea è disattivata per impostazione predefinita e può essere abilitata tramite [PowerShell](../../key-vault/general/soft-delete-powershell.md#enabling-soft-delete) o [l'interfaccia della](../../key-vault/general/soft-delete-cli.md#enabling-soft-delete)riga di comando. Non può essere abilitata tramite il portale di Azure.  

- Concedere al server o all'istanza gestita l'accesso all'insieme di credenziali delle chiavi (Get, wrapKey, unwrapKey) usando la relativa identità Azure Active Directory. Quando si usa il portale di Azure, viene creata automaticamente l'identità Azure AD. Quando si usa PowerShell o l'interfaccia della riga di comando, l'identità Azure AD deve essere creata in modo esplicito e verificare il completamento. Per istruzioni dettagliate sull'uso di PowerShell, vedere Configurare Transparent Data Encryption [con BYOK](transparent-data-encryption-byok-configure.md) e configurare Transparent Data [Encryption con BYOK per SQL istanza gestita](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) .

- Quando si usa il firewall con AKV, è necessario abilitare l'opzione *Consentire ai servizi Microsoft attendibili di ignorare il firewall?*

### <a name="requirements-for-configuring-tde-protector"></a>Requisiti per la configurazione della protezione TDE

- La chiave di protezione TDE può essere solo una chiave RSA o HSM RSA asimmetrica. Le lunghezze supportate per le chiavi sono di 2048 e 3072 byte.

- La data di attivazione della chiave (se impostata) deve essere una data e un'ora nel passato. La data di scadenza (se impostata) deve essere una data e un'ora future.

- La chiave deve avere lo stato *Abilitato*.

- Se si importa una chiave esistente nel Key Vault, assicurarsi di specificarla nei formati di file supportati (pfx, byok o backup).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Suggerimenti per la configurazione di Transparent Data Encryption gestita dal cliente

### <a name="recommendations-when-configuring-akv"></a>Suggerimenti per la configurazione di AKV

- Associare al massimo 500 database di utilizzo generico o 200 database business critical in totale a un Key Vault in una singola sottoscrizione per garantire la disponibilità elevata quando il server accede alla protezione TDE in Key Vault. Queste cifre sono basate sull'esperienza e documentate nei [limiti del servizio Key Vault](../../key-vault/general/service-limits.md). L'obiettivo è evitare problemi dopo il failover del server poiché nel Key Vault viene attivato un numero di operazioni per le chiavi corrispondente ai database nel server.

- Impostare un blocco di risorsa nel Key Vault per controllare chi può eliminare questa risorsa critica e impedire l'eliminazione accidentale o non autorizzata. Altre informazioni sui [blocchi delle risorse](../../azure-resource-manager/management/lock-resources.md).

- Abilitare il controllo e la creazione di report per tutte le chiavi di crittografia: Il Key Vault offre log che possono essere facilmente inclusi in altri strumenti di gestione delle informazioni di sicurezza e di gestione degli eventi. [Log Analytics](../../azure-monitor/insights/azure-key-vault.md) di Operations Management Suite è un esempio di un servizio già integrato.

- Collegare ogni server a due Key Vault che risiedono in aree diverse e contengono lo stesso materiale della chiave per garantire la disponibilità elevata dei database crittografati. Contrassegnare solo la chiave del Key Vault nella stessa area della protezione TDE. Se si verifica un'interruzione che interessa il Key Vault nella stessa area, il sistema passa automaticamente al Key Vault nell'area remota.

### <a name="recommendations-when-configuring-tde-protector"></a>Suggerimenti per la configurazione della protezione TDE

- Mantenere una copia della chiave di protezione TDE in un luogo sicuro o depositarla nel servizio di deposito.

- Se la chiave viene generata nel Key Vault, creare un backup della chiave prima di usare la chiave in AKV per la prima volta. Il backup può essere ripristinato solo in Azure Key Vault. Altre informazioni sul comando [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Creare un nuovo backup ogni volta che vengono apportate modifiche alla chiave (ad esempio, attributi chiavi, tag, ACL).

- **Mantenere le versioni precedenti** della chiave nell'insieme di credenziali delle chiavi durante la rotazione delle chiavi in modo tale da poter ripristinare i backup del database meno recenti. Quando viene modificata la protezione TDE per un database, i backup precedenti del database **non vengono aggiornati** per l'uso della protezione TDE più recente. In fase di ripristino ogni backup richiede la protezione TDE usata per la crittografia al momento della creazione. Le rotazioni delle chiavi possono essere eseguite seguendo le istruzioni riportate in [Ruotare la protezione di Transparent Data Encryption con PowerShell](transparent-data-encryption-byok-key-rotation.md).

- Conservare tutte le chiavi usate in precedenza in AKV anche dopo il passaggio alle chiavi gestite dal servizio. In questo modo è possibile ripristinare i backup dei database con protezioni TDE archiviate in AKV.  È necessario conservare le protezioni TDE create con Azure Key Vault fino a quando tutti gli altri backup archiviati non sono stati creati con chiavi gestite dal servizio. Creare copie di backup recuperabili delle chiavi usando [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Per rimuovere una chiave potenzialmente compromessa durante un evento di sicurezza imprevisto senza il rischio di perdita di dati, seguire la procedura descritta in [Rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Protezione TDE non accessibile

Quando Transparent Data Encryption è configurata per l'uso di una chiave gestita dal cliente, è necessario l'accesso continuo alla protezione TDE affinché il database rimanga online. Se il server perde l'accesso alla protezione TDE gestita dal cliente in Azure Key Vault, dopo un massimo di 10 minuti il database inizierà a negare tutte le connessioni con il messaggio di errore corrispondente e cambierà lo stato in *Inaccessibile*. L'unica azione consentita in un database con stato Inaccessibile è l'eliminazione.

> [!NOTE]
> Se il database è inaccessibile a causa di un'interruzione di rete intermittente, non sarà necessario eseguire alcuna azione e i database torneranno online automaticamente.

Dopo il ripristino dell'accesso alla chiave, per riportare online il database sono necessari passaggi e tempo aggiuntivi, che possono variare in base al tempo trascorso senza accesso alla chiave e alle dimensioni del database:

- Se l'accesso alla chiave viene ripristinato entro 8 ore, il database verrà riparato automaticamente entro un'ora.

- Se l'accesso alla chiave viene ripristinato dopo più di 8 ore, la riparazione automatica non è possibile e la restituzione del database richiede passaggi aggiuntivi nel portale e può richiedere una quantità di tempo significativa a seconda delle dimensioni del database. Quando il database è di nuovo online, le impostazioni a livello di server configurate in precedenza, ad esempio la configurazione del [gruppo di failover](auto-failover-group-overview.md), la cronologia dei ripristini temporizzati e i tag, **andranno persi**. È pertanto consigliabile implementare un sistema di notifica che consenta di identificare e risolvere entro 8 ore i problemi di accesso alla chiave sottostanti.

### <a name="accidental-tde-protector-access-revocation"></a>Revoca accidentale dell'accesso alla protezione TDE

È possibile che un utente con diritti di accesso sufficienti al Key Vault disabiliti accidentalmente l'accesso del server alla chiave eseguendo le operazioni seguenti:

- revoca delle autorizzazioni *Get*, *wrapKey*, *unwrapKey* del Key Vault dal server

- eliminazione della chiave

- eliminazione del Key Vault

- modifica delle regole del firewall del Key Vault

- eliminazione dell'identità gestita del server in Azure Active Directory

Altre informazioni sulle [cause comuni per cui il database diventa inaccessibile](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitoraggio di Transparent Data Encryption gestita dal cliente

Per monitorare lo stato del database e abilitare gli avvisi per la perdita dell'accesso alla protezione TDE, configurare le funzionalità di Azure seguenti:

- [Integrità risorse di Azure](../../service-health/resource-health-overview.md). Un database non accessibile che ha perso l'accesso alla protezione TDE viene visualizzato come "Non disponibile" dopo che è stata negata la prima connessione al database.
- [Log attività](../../service-health/alerts-activity-log-service-notifications.md). Quando si verifica un errore durante l'accesso alla protezione TDE nel Key Vault gestito dal cliente, vengono aggiunte voci al log attività.  La creazione di avvisi per questi eventi consente di ripristinare l'accesso appena possibile.
- [Gruppi di azioni](../../azure-monitor/platform/action-groups.md). È possibile definire gruppi di azioni per inviare notifiche e avvisi in base alle preferenze, ad esempio Posta elettronica/SMS/Push/Messaggio vocale, App per la logica, Webhook, Gestione dei servizi IT o Runbook di Automazione.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Backup e ripristino di database con Transparent Data Encryption gestita dal cliente

Quando un database viene crittografato con TDE usando una chiave del Key Vault, vengono crittografati anche eventuali nuovi backup generati con la stessa protezione TDE. Quando la protezione TDE viene modificata, i backup precedenti del database **non vengono aggiornati** per l'uso della protezione TDE più recente.

Per ripristinare un backup crittografato con la protezione TDE del Key Vault, assicurarsi che il materiale della chiave sia disponibile per il server di destinazione. Per questa ragione, è consigliabile conservare tutte le versioni precedenti della protezione TDE nel Key Vault in modo che i backup dei database possano essere ripristinati.

> [!IMPORTANT]
> In qualsiasi momento potrebbe non essere presente più di una protezione TDE impostata per un server. Si tratta della chiave contrassegnata con "Imposta la chiave predefinita come protezione TDE predefinita" nel pannello del portale di Azure. È possibile tuttavia collegare più chiavi aggiuntive a un server senza contrassegnarle come protezione TDE. Queste chiavi non vengono usate per la protezione della chiave DEK, ma possono essere usate durante il ripristino da un backup, se il file di backup è crittografato con la chiave con l'identificazione personale corrispondente.

Se la chiave necessaria per il ripristino di un backup non è più disponibile per il server di destinazione, nel tentativo di ripristino viene restituito il messaggio di errore seguente: "il server di destinazione non `<Servername>` ha accesso a tutti gli URI AKV creati tra \<Timestamp #1> e \<Timestamp #2> . Ripetere l'operazione dopo il ripristino di tutti gli URI AKV".

Per attenuarlo, eseguire il cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) per il server di destinazione o [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) per l'istanza gestita di destinazione per restituire l'elenco delle chiavi disponibili e identificare quelle mancanti. Per garantire che tutti i backup possano essere ripristinati, verificare che il server di destinazione per il ripristino abbia accesso a tutte le chiavi necessarie. Le chiavi non devono essere contrassegnate come protezione TDE.

Per ulteriori informazioni sul ripristino del backup per il database SQL, vedere [ripristino di un database nel database SQL](recovery-using-backups.md). Per ulteriori informazioni sul ripristino del backup per il pool SQL, vedere [ripristino di un pool SQL](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md). Per il backup/ripristino nativo di SQL Server con SQL Istanza gestita, vedere [Guida introduttiva: ripristinare un database in sql istanza gestita](../managed-instance/restore-sample-database-quickstart.md)

Considerazione aggiuntiva per i file di log: i file di log di cui è stato eseguito il backup rimangono crittografati con la protezione TDE originale, anche se è stata ruotata e il database usa ora una nuova protezione TDE.  In fase di ripristino, per ripristinare il database saranno necessarie entrambe le chiavi.  Se il file di log usa una protezione TDE archiviata in Azure Key Vault, questa chiave sarà necessaria in fase di ripristino, anche se nel frattempo il database è stato modificato per usare TDE gestita dal servizio.

## <a name="high-availability-with-customer-managed-tde"></a>Disponibilità elevata con Transparent Data Management gestita dal cliente

Anche nei casi in cui non è stata configurata alcuna ridondanza geografica per il server, si consiglia di configurare il server per l'uso di due diversi Key Vault in due aree diverse con lo stesso materiale della chiave. Questa operazione può essere eseguita creando una protezione TDE con il Key Vault primario posizionato nella stessa area del server e clonando la chiave in un Key Vault in un'area di Azure diversa, in modo che il server abbia accesso a un secondo Key Vault nel caso in cui si verificasse un'interruzione del Key Vault primario mentre il database è attivo e in esecuzione.

Usare il cmdlet Backup-AzureKeyVaultKey per recuperare la chiave in formato crittografato dal Key Vault primario e quindi usare il cmdlet Restore-AzureKeyVaultKey e specificare un Key Vault nella seconda area per clonare la chiave. In alternativa, usare la portale di Azure per eseguire il backup e il ripristino della chiave. La chiave nell'insieme di credenziali delle chiavi secondario nell'altra area non deve essere contrassegnata come protezione Transparent Data Encryption e non è consentita.

Se si verifica un'interruzione che interessano l'insieme di credenziali delle chiavi primarie e solo successivamente, il sistema passa automaticamente all'altra chiave collegata con la stessa identificazione personale nell'insieme di credenziali delle chiavi secondario, se esistente. Si noti che il passaggio non si verifica se la protezione TDE non è accessibile a causa di diritti di accesso revocati o perché la chiave o il Key Vault è stato eliminato. Ciò potrebbe indicare che il cliente ha intenzionalmente voluto limitare l'accesso alla chiave da parte del server.

![Disponibilità elevata con server singolo](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Ripristino di emergenza geografico e Transparent Data Encryption gestita dal cliente

In entrambi gli scenari di [replica geografica attiva](active-geo-replication-overview.md) e [gruppi di failover](auto-failover-group-overview.md), ogni server implicato richiede un Key Vault separato che deve trovarsi nella stessa area di Azure del server. Il cliente è responsabile di mantenere la coerenza del materiale della chiave nei Key Vault in modo che la replica geografica secondaria sia sincronizzata e possa essere eseguita usando la stessa chiave del Key Vault locale se il database primario diventa inaccessibile a causa di un'interruzione nell'area e dell'attivazione di un failover. È possibile configurare fino a quattro repliche secondarie e il concatenamento (database secondari di database secondari) non è supportato.

Per evitare problemi durante la creazione o durante la replica geografica a causa di un materiale della chiave incompleto, è importante attenersi alle regole seguenti durante la configurazione di Transparent Data Encryption gestita dal cliente:

- Tutti i Key Vault interessati devono avere le stesse proprietà e gli stessi diritti di accesso per i rispettivi server.

- Tutti i Key Vault interessati devono contenere lo stesso materiale della chiave. Ciò si applica alla protezione TDE corrente e a tutte le protezioni TDE precedenti che possono essere state usate nei file di backup.

- Sia la configurazione iniziale che la rotazione della protezione TDE devono essere eseguite prima nel database secondario e successivamente nel database primario.

![Gruppi di failover e ripristino di emergenza geografico](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

Per testare un failover, seguire la procedura descritta in [Panoramica della replica geografica attiva](active-geo-replication-overview.md). Il failover di test deve essere eseguito a intervalli regolari per verificare che il database SQL abbia mantenuto le autorizzazioni di accesso a entrambi gli insiemi di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile controllare gli script di esempio di PowerShell seguenti per le operazioni comuni con Transparent Data Encryption gestita dal cliente:

- [Ruotare la protezione TDE per il database SQL tramite PowerShell](transparent-data-encryption-byok-key-rotation.md)

- [Rimuove una protezione TDE (Transparent Data Encryption) per il database SQL tramite PowerShell](transparent-data-encryption-byok-remove-tde-protector.md)

- [Gestire Transparent Data Encryption in SQL Istanza gestita con la propria chiave tramite PowerShell](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)
