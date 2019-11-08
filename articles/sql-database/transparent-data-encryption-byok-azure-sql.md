---
title: Transparent Data Encryption (Transparent Data Encryption) gestito dal cliente
description: 'Supporto Bring Your Own Key (BYOK) per Transparent Data Encryption (TDE) con Azure Key Vault per database SQL e Azure SQL Data Warehouse. TDE con BYOK: panoramica, vantaggi, funzionamento, considerazioni ed elementi consigliati.'
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 11/04/2019
ms.openlocfilehash: 3ae5403c2313bc1d2f271aeba9d4a99d9a0c5db7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822277"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Transparent Data Encryption SQL di Azure con chiave gestita dal cliente

Transparent Data Encryption SQL di Azure (Transparent Data Management [)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) con chiave gestita dal cliente Abilita Bring your own key (BYOK) per la protezione dei dati inattivi e consente alle organizzazioni di implementare la separazione dei compiti nella gestione di chiavi e dati. Grazie alla funzionalità Transparent Data Encryption gestita dal cliente, il cliente è responsabile di e in un controllo completo di una gestione del ciclo di vita delle chiavi (creazione, caricamento, rotazione, eliminazione), autorizzazioni di utilizzo chiave e controllo delle operazioni sulle chiavi.

In questo scenario, la chiave utilizzata per la crittografia della chiave di crittografia del database, denominata protezione Transparent Data Encryption, è una chiave asimmetrica gestita dal cliente archiviata in una Azure Key Vault di proprietà del cliente e gestita dal cliente [(AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), una gestione delle chiavi esterne basata sul cloud sistema. Key Vault è un'archiviazione sicura a disponibilità elevata e scalabile per chiavi crittografiche RSA, supportata da moduli di protezione hardware convalidati FIPS 140-2 Level 2 (HSM). Non consente l'accesso diretto a una chiave archiviata, ma fornisce servizi di crittografia/decrittografia usando la chiave per le entità autorizzate. La chiave può essere generata dall'insieme di credenziali delle chiavi, importato o trasferito nell'insieme di credenziali delle [chiavi da un dispositivo HSM](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)locale.

Per il database SQL di Azure e Azure SQL Data Warehouse, la protezione Transparent Data Encryption è impostata a livello di server logico e viene ereditata da tutti i database crittografati associati a tale server. Per Istanza gestita SQL di Azure, la protezione Transparent Data Encryption è impostata a livello di istanza e viene ereditata da tutti i database crittografati in tale istanza. Il termine *Server* si riferisce sia al server logico del database SQL che all'istanza gestita in tutto il documento, a meno che non venga specificato diversamente. 

> [!IMPORTANT]
> Per chi utilizza Transparent Data Encryption gestito dal servizio che desidera iniziare a utilizzare la crittografia transazionale gestita dal cliente, i dati rimangono crittografati durante il processo di trasferimento e non si verificano tempi di inattività e la nuova crittografia dei file di database. Il trasferimento da una chiave gestita dal servizio a una chiave gestita dal cliente richiede solo una nuova crittografia della chiave di crittografia, che è un'operazione veloce e online.

## <a name="benefits-of-the-customer-managed-tde"></a>Vantaggi della crittografia Transparent gestita dal cliente

Transparent Data Encryption gestito dal cliente offre al cliente i vantaggi seguenti:

- Controllo completo e granulare sull'utilizzo e sulla gestione della protezione Transparent Data Encryption;

- Trasparenza dell'utilizzo della protezione Transparent Data Encryption;

- Possibilità di implementare la separazione dei compiti nella gestione delle chiavi e dei dati all'interno dell'organizzazione;

- Key Vault amministratore può revocare le autorizzazioni di accesso alla chiave per rendere inaccessibile il database crittografato.

- Gestione centralizzata delle chiavi in AKV;

- Maggiore attendibilità dei clienti finali, dal momento che AKV è progettato in modo che Microsoft non possa visualizzare né estrarre le chiavi di crittografia.

## <a name="how-customer-managed-tde-works"></a>Funzionamento di Transparent Data Encryption gestito dal cliente

![Installazione e funzionamento di Transparent Data Encryption gestito dal cliente](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Per consentire al server di usare la protezione Transparent Data Encryption archiviata in AKV per la crittografia della chiave di crittografia, l'amministratore dell'insieme di credenziali delle chiavi deve concedere al server i seguenti diritti di accesso usando l'identità AAD univoca:

- **Get** : per il recupero della parte pubblica e delle proprietà della chiave nella Key Vault

- **wrapKey** -per poter proteggere (crittografare) la chiave di crittografia

- **unwrapKey** -per poter rimuovere la protezione (decrittografia)

L'amministratore di Key Vault può anche [abilitare la registrazione degli eventi di controllo dell'insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault), in modo che possano essere controllati in seguito.

Quando il server è configurato per l'uso di una protezione Transparent Data Encryption da AKV, il server invia la chiave di crittografia di ogni database abilitato per Transparent Data Encryption a Key Vault per la crittografia. Key Vault restituisce la crittografia crittografata, che viene quindi archiviata nel database utente.

Quando necessario, il server invia la chiave di crittografia protetta all'insieme di credenziali delle chiavi per la decrittografia.

Se la registrazione è abilitata, i revisori possono usare monitoraggio di Azure per esaminare i log AuditEvent di Key Vault.


## <a name="requirements-for-configuring-customer-managed-tde"></a>Requisiti per la configurazione di Data Encryption gestita dal cliente

### <a name="requirements-for-configuring-akv"></a>Requisiti per la configurazione di AKV

- L'insieme di credenziali delle chiavi e l'istanza gestita/database SQL devono appartenere allo stesso tenant di Azure Active Directory. L'insieme di credenziali delle chiavi tra tenant e le interazioni tra server non sono supportate. Per spostare le risorse in un secondo momento, è necessario riconfigurare Transparent Data Encryption con AKV. Altre informazioni sullo stato di [trasferimento delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- È necessario abilitare la funzionalità di [eliminazione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) temporanea nell'insieme di credenziali delle chiavi, in modo da evitare la perdita di dati accidentali o l'eliminazione di Key Vault. Le risorse eliminate temporaneamente vengono conservate per 90 giorni, a meno che non vengano ripristinate o eliminate dal cliente nel frattempo. Alle azioni di *recupero* ed *eliminazione definitiva* sono associate autorizzazioni specifiche nei criteri di accesso dell'insieme di credenziali delle chiavi. La funzionalità di eliminazione temporanea è disabilitata per impostazione predefinita e può essere abilitata tramite [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) o l' [interfaccia](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)della riga di comando. Non può essere abilitata tramite portale di Azure.  

- Concedere al server di database SQL o all'istanza gestita l'accesso all'insieme di credenziali delle chiavi (Get, wrapKey, unwrapKey) usando la relativa identità Azure Active Directory. Quando si usa portale di Azure, viene creata automaticamente l'identità Azure AD. Quando si usa PowerShell o l'interfaccia della riga di comando, l'identità Azure AD deve essere creata in modo esplicito e verificare il completamento. Vedere [Configurare TDE con BYOK](transparent-data-encryption-byok-azure-sql-configure.md) e [Configurare TDE con BYOK per l'istanza gestita](https://aka.ms/sqlmibyoktdepowershell) per istruzioni dettagliate relative all'uso con PowerShell.

- Quando si usa il firewall con AKV, è necessario abilitare *l'opzione Consenti ai servizi Microsoft attendibili di ignorare il firewall*.

### <a name="requirements-for-configuring-tde-protector"></a>Requisiti per la configurazione della protezione Transparent Data Encryption

- La protezione Transparent Data Encryption può essere solo asimmetrica, RSA 2048 o RSA HSM 2048 Key.

- Per la chiave non è possibile impostare la data di attivazione o di scadenza.

- La chiave deve essere nello stato abilitato nell'insieme di credenziali delle chiavi.

- Se si sta importando una chiave esistente nell'insieme di credenziali delle chiavi, assicurarsi di specificarla nei formati di file supportati, ovvero PFX, Byok o backup.

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Suggerimenti per la configurazione di Transparent Data Management

### <a name="recommendations-when-configuring-akv"></a>Suggerimenti per la configurazione di AKV

- Associare al massimo 500 per utilizzo generico o 200 business critical database in totale con un insieme di credenziali delle chiavi in una singola sottoscrizione per garantire la disponibilità elevata quando il server accede alla protezione Transparent Data Encryption nell'insieme di credenziali delle chiavi. Queste cifre sono basate sull'esperienza e documentate nei [limiti del servizio Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits). L'intenzione è di evitare problemi dopo il failover del server, in quanto attiverà il numero di operazioni chiave sull'insieme di credenziali in quanto sono presenti database in tale server. 

- Impostare un blocco di risorsa nell'insieme di credenziali delle chiavi per controllare gli utenti che possono eliminare questa risorsa critica e impedire l'eliminazione accidentale o non autorizzata. Altre informazioni sui [blocchi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Abilitare il controllo e la creazione di report per tutte le chiavi di crittografia: Key Vault fornisce log facili da inserire in altri strumenti di sicurezza e gestione degli eventi. Operations Management Suite [log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) è un esempio di servizio già integrato.

- Collegare ogni server con due insiemi di credenziali delle chiavi che si trovano in aree diverse e che contengono lo stesso materiale della chiave, per garantire la disponibilità elevata dei database crittografati. Contrassegnare solo la chiave dell'insieme di credenziali delle chiavi nella stessa area di una protezione Transparent Data Encryption. Il sistema utilizzerà

### <a name="recommendations-when-configuring-tde-protector"></a>Suggerimenti per la configurazione della protezione Transparent Data Encryption
- Mantenere una copia della protezione Transparent Data Encryption in un luogo sicuro o depositarla nel servizio escrow. 

- Se la chiave viene generata nell'insieme di credenziali delle chiavi, creare un backup della chiave prima di usare la chiave in AKV per la prima volta. Il backup può essere ripristinato solo in un Azure Key Vault. Altre informazioni sul comando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) .

- Creare un nuovo backup ogni volta che vengono apportate modifiche alla chiave (ad esempio, attributi chiave, tag, ACL).

- **Mantenere le versioni precedenti** della chiave nell'insieme di credenziali delle chiavi durante la rotazione delle chiavi in modo tale da poter ripristinare i backup del database meno recenti. Quando si modifica la protezione Transparent Data Encryption per un database, i backup obsoleti del database **non vengono aggiornati** in modo da usare la protezione Transparent Data Encryption più recente. Al momento del ripristino, per ogni backup è necessaria la protezione Transparent Data Encryption con la crittografia al momento della creazione. Le rotazioni delle chiavi possono essere eseguite seguendo le istruzioni riportate in [Ruotare la protezione di Transparent Data Encryption con PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Mantieni tutte le chiavi usate in precedenza in AKV anche dopo il trasferimento alle chiavi gestite dal servizio. Garantisce che i backup del database possano essere ripristinati con le protezioni Transparent Data Encryption archiviate in AKV.  Le protezioni Transparent Data Encryption create con Azure Key Vault devono essere mantenute fino a quando non vengono creati tutti i backup archiviati rimanenti con chiavi gestite dal servizio. Eseguire copie di backup reversibili di queste chiavi utilizzando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Per rimuovere una chiave potenzialmente compromessa durante un evento imprevisto della sicurezza senza il rischio di perdita di dati, seguire la procedura descritta in [rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Protezione Transparent Data Encryption non accessibile

Quando Transparent Data Encryption è configurato per l'uso di una chiave gestita dal cliente, è necessario l'accesso continuo alla protezione Transparent Data Encryption affinché il database resti online. Se il server perde l'accesso alla protezione di Transparent Data Encryption gestita dal cliente in AKV, in un massimo di 10 minuti un database inizierà a negare tutte le connessioni con il messaggio di errore corrispondente e a impostare lo stato su *inaccessibile*. L'unica azione consentita su un database nello stato inaccessibile è l'eliminazione.

> [!NOTE]
> Se il database è inaccessibile a causa di un'interruzione di rete intermittente, non è necessario eseguire alcuna azione e i database torneranno online automaticamente.

Dopo il ripristino dell'accesso alla chiave, per riportare online il database sono necessari tempi e passaggi aggiuntivi, che possono variare in base al tempo trascorso senza l'accesso alla chiave e alla dimensione dei dati nel database:

- Se l'accesso alla chiave viene ripristinato entro 8 ore, il database verrà risanato automaticamente entro l'ora successiva.

- Se l'accesso alla chiave viene ripristinato dopo più di 8 ore, la correzione automatica non è possibile e la restituzione del database può richiedere una quantità di tempo significativa a seconda delle dimensioni del database e richiede l'apertura di un ticket di supporto. Quando il database è di nuovo online, le impostazioni a livello di server configurate in precedenza, ad esempio la configurazione del [gruppo di failover](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) , la cronologia di ripristino temporizzato e i tag andranno perse. Si consiglia pertanto di implementare un sistema di notifica che consente di identificare e risolvere i problemi di accesso alle chiavi sottostanti entro 8 ore.

### <a name="accidental-tde-protector-access-revocation"></a>Revoca accidentale per la protezione Transparent Transparent

Potrebbe verificarsi che un utente con diritti di accesso sufficienti per l'insieme di credenziali delle chiavi Disabilita accidentalmente l'accesso al server alla chiave da:

- revoca delle autorizzazioni *Get*, *wrapKey*, *unwrapKey* di Key Vault dal server

- eliminazione della chiave

- eliminazione dell'insieme di credenziali delle chiavi

- modifica delle regole del firewall del Key Vault

- eliminazione dell'identità gestita del server in Azure Active Directory

Altre informazioni sulle [cause comuni per cui il database diventa inaccessibile](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitoraggio di Transparent Data Encryption gestito dal cliente

Per monitorare lo stato del database e abilitare gli avvisi per la perdita dell'accesso con protezione Transparent Data Encryption, configurare le funzionalità di Azure seguenti:
- [Integrità risorse di Azure](https://docs.microsoft.com/azure/service-health/resource-health-overview). Un database inaccessibile che ha perso l'accesso alla protezione Transparent Data Encryption viene visualizzato come "non disponibile" dopo che è stata negata la prima connessione al database.
- [Log attività](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) quando l'accesso alla protezione Transparent Data Encryption nell'insieme di credenziali delle chiavi gestito dal cliente ha esito negativo, le voci vengono aggiunte al log attività.  La creazione di avvisi per questi eventi consente di ripristinare l'accesso appena possibile.
- I [gruppi di azioni](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) possono essere definiti per inviare notifiche e avvisi in base alle proprie preferenze, ad esempio posta elettronica/SMS/push/voce, app per la logica, webhook, ITSM o Runbook di automazione.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Backup e ripristino di database con Transparent Data Encryption gestito dal cliente

Quando un database viene crittografato con Transparent Data Encryption usando una chiave di Key Vault, anche tutti i backup appena generati vengono crittografati con la stessa protezione Transparent Data Encryption. Quando si modifica la protezione Transparent Data Encryption, i backup obsoleti del database **non vengono aggiornati** in modo da usare la protezione Transparent Data Encryption più recente.

Per ripristinare un backup crittografato con una protezione Transparent Data Encryption da Key Vault, verificare che il materiale della chiave sia disponibile per il server di destinazione. Pertanto, è consigliabile mantenere tutte le versioni precedenti della protezione Transparent Data Encryption in Key Vault, in modo che sia possibile ripristinare i backup del database. 

> [!IMPORTANT]
> In qualsiasi momento non è possibile impostare più di una protezione Transparent Data Encryption per un server. Si tratta della chiave contrassegnata con "make the Key the default Encryption Protector" nel pannello portale di Azure. Tuttavia, è possibile collegare più chiavi aggiuntive a un server senza contrassegnarle come protezione Transparent Data Encryption. Queste chiavi non vengono usate per la protezione della chiave di crittografia, ma possono essere usate durante il ripristino da un backup, se il file di backup è crittografato con la chiave con l'identificazione personale corrispondente.

Se la chiave necessaria per il ripristino di un backup non è più disponibile per il server di destinazione, durante il tentativo di ripristino viene restituito il messaggio di errore seguente: "il server di destinazione `<Servername>` non ha accesso a tutti gli URI AKV creati tra \<timestamp #1 > e @no__t_ #2 timestamp di 2_ >.\< Ripetere l'operazione dopo il ripristino di tutti gli URI AKV ".

Per attenuarlo, eseguire il cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) per il server logico del database SQL di destinazione o [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) per l'istanza gestita di destinazione per restituire l'elenco delle chiavi disponibili e identificare quelle mancanti. Per assicurarsi che tutti i backup possano essere ripristinati, verificare che il server di destinazione per il ripristino abbia accesso a tutte le chiavi necessarie. Queste chiavi non devono essere contrassegnate come protezione Transparent Data Encryption.

Per altre informazioni sul ripristino dei backup per il database SQL, vedere [Recuperare un database SQL di Azure](sql-database-recovery-using-backups.md). Per altre informazioni sul ripristino dei backup per SQL Data Warehouse, vedere [Recuperare un Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md). Per il backup/ripristino nativo di SQL Server con istanza gestita, vedere [Guida introduttiva: ripristinare un database a una istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) 

Considerazioni aggiuntive per i file di log: i file di log di cui è stato eseguito il backup rimangono crittografati con la protezione Transparent Data Encryption originale, anche se è stata ruotata e il database ora usa una nuova protezione Transparent Data Encryption.  In fase di ripristino, per ripristinare il database saranno necessarie entrambe le chiavi.  Se il file di log utilizza una protezione Transparent Data Encryption archiviata in Azure Key Vault, questa chiave sarà necessaria in fase di ripristino, anche se il database è stato modificato per l'utilizzo di Transparent Service-Managed nel frattempo.

## <a name="high-availability-with-customer-managed-tde"></a>Disponibilità elevata con Transparent Data Management gestito dal cliente

Anche nei casi in cui non è stata configurata alcuna ridondanza geografica per il server, è consigliabile configurare il server in modo da usare due insiemi di credenziali delle chiavi diversi in due aree diverse con lo stesso materiale della chiave. Questa operazione può essere eseguita creando una protezione Transparent Data Encryption usando l'insieme di credenziali delle chiavi primarie nella stessa area del server e clonando la chiave in un insieme di credenziali delle chiavi in un'area di Azure diversa, in modo che il server possa accedere a un secondo insieme di credenziali delle chiavi, in base all'insieme di credenziali delle chiavi primarie Exper ience un'interruzione del servizio mentre il database è attivo e in esecuzione. 

Usare il cmdlet Backup-AzKeyVaultKey per recuperare la chiave in formato crittografato dall'insieme di credenziali delle chiavi primarie e quindi usare il cmdlet Restore-AzKeyVaultKey e specificare un insieme di credenziali delle chiavi nella seconda area per clonare la chiave. In alternativa, usare portale di Azure per eseguire il backup e il ripristino della chiave. La chiave nell'insieme di credenziali delle chiavi secondario in un'altra area non deve essere contrassegnata come protezione Transparent Data Encryption e non è consentita.

 Se si verifica un'interruzione che interessano l'insieme di credenziali delle chiavi primarie e solo successivamente, il sistema passa automaticamente all'altra chiave collegata con la stessa identificazione personale nell'insieme di credenziali delle chiavi secondario, se esistente. Si noti che questa opzione non si verifica se la protezione Transparent Data Encryption è inaccessibile a causa di diritti di accesso revocati o perché la chiave o l'insieme di credenziali delle chiavi è stato eliminato, in quanto può indicare che il cliente ha intenzionalmente voluto limitare l'accesso alla chiave da parte del server.

![Disponibilità elevata a server singolo](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>RIPRISTINO di emergenza geografico e crittografia gestita dal cliente

Negli scenari di [replica geografica attiva](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) e di [gruppi di failover](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) , ogni server richiesto richiede un insieme di credenziali delle chiavi separato, che deve avere un percorso condiviso con il server nella stessa area di Azure. Il cliente è responsabile della coerenza del materiale della chiave negli insiemi di credenziali delle chiavi, in modo che la replica geografica secondaria sia sincronizzata e possa assumere l'uso della stessa chiave dall'insieme di credenziali delle chiavi locale se il database primario diventa inaccessibile a causa di un'interruzione nell'area e viene attivato un failover . È possibile configurare fino a quattro repliche secondarie e il concatenamento (database secondari di database secondari) non è supportato.

Per evitare problemi durante la creazione o durante la replica geografica a causa di un materiale della chiave incompleto, è importante attenersi a queste regole durante la configurazione di Transparent Data Encryption gestita dal cliente:

- Tutti gli insiemi di credenziali delle chiavi interessati devono avere le stesse proprietà e gli stessi diritti di accesso per i rispettivi server.

- Tutti gli insiemi di credenziali delle chiavi necessari devono contenere materiale della chiave identico. Si applica non solo alla protezione Transparent Data Encryption corrente, ma a tutte le protezioni Transparent Data Encryption precedenti che possono essere usate nei file di backup.

- Sia la configurazione iniziale che la rotazione della protezione Transparent Data Encryption devono essere eseguite prima nel database secondario e quindi nel database primario.

![Gruppi di failover e ripristino di emergenza geografico](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Per testare un failover, seguire la procedura descritta in [Panoramica della replica geografica attiva](sql-database-geo-replication-overview.md). Questa operazione dovrebbe essere eseguita regolarmente per confermare che le autorizzazioni di accesso per SQL per entrambi gli insiemi di credenziali delle chiavi sono state mantenute.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile controllare gli script di esempio di PowerShell seguenti per le operazioni comuni con Transparent Data Encryption gestito dal cliente:

- [Ruotare la protezione Transparent Data Encryption per il database SQL tramite PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Rimuovere una protezione Transparent Data Encryption (Transparent Data Encryption) per il database SQL tramite PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Gestire Transparent Data Encryption in una Istanza gestita con una chiave personalizzata usando PowerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
