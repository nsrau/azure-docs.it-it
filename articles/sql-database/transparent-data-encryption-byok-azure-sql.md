---
title: Crittografia dei dati trasparente gestita dal cliente (TDE)
description: 'Portare la propria chiave (BYOK) supporto per Transparent Data Encryption (TDE) con Azure Key Vault per il database SQL e Azure Synapse. TDE con BYOK: panoramica, vantaggi, funzionamento, considerazioni ed elementi consigliati.'
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528729"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Crittografia dei dati trasparente di SQL azure con chiave gestita dal clienteAzure SQL Transparent Data Encryption with customer-managed key

Azure SQL [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) con chiave gestita dal cliente abilita lo scenario BYOK (Bring Your Own Key) per la protezione dei dati inattivi e consente alle organizzazioni di implementare la separazione dei compiti nella gestione di chiavi e dati. Con la crittografia dei dati trasparente gestita dal cliente, il cliente è responsabile e in un controllo completo di una gestione del ciclo di vita delle chiavi (creazione delle chiavi, caricamento, rotazione, eliminazione), autorizzazioni di utilizzo delle chiavi e controllo delle operazioni sulle chiavi.

In questo scenario, la chiave utilizzata per la crittografia della chiave DEK (Database Encryption Key), denominata protezione TDE, è una chiave asimmetrica gestita dal cliente archiviata in un archivio delle chiavi di [Azure (AKV, Key Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)di proprietà del cliente e gestito dal cliente, un sistema di gestione delle chiavi esterne basato su cloud. Key Vault è un'archiviazione sicura a disponibilità elevata e scalabile per le chiavi crittografiche RSA, facoltativamente supportata da hSM convalidati FIPS 140-2 Level 2. Non consente l'accesso diretto a una chiave archiviata, ma fornisce servizi di crittografia/decrittografia utilizzando la chiave alle entità autorizzate. La chiave può essere generata dall'insieme di credenziali delle chiavi, importata o trasferita nell'insieme di credenziali delle [chiavi da un dispositivo HSM](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)precedente.

Per il database SQL di Azure e Azure Synapse, la protezione TDE viene impostata a livello di server logico ed è ereditata da tutti i database crittografati associati a tale server. Per l'istanza gestita SQL di Azure, la protezione TDE viene impostata a livello di istanza ed è ereditata da tutti i database crittografati in tale istanza. Il termine *server* si riferisce sia al server logico del database SQL che all'istanza gestita in tutto il documento, a meno che non sia indicato in modo diverso.

> [!IMPORTANT]
> Per coloro che utilizzano TDE gestito dal servizio che desiderano iniziare a utilizzare TDE gestito dal cliente, i dati rimangono crittografati durante il processo di passaggio e non vi è alcun tempo di inattività né ricrittografia dei file di database. Il passaggio da una chiave gestita dal servizio a una chiave gestita dal cliente richiede solo una ricrittografia della chiave DEK, che è un'operazione veloce e online.

## <a name="benefits-of-the-customer-managed-tde"></a>Vantaggi del TDE gestito dal cliente

TDE gestito dal cliente offre i seguenti vantaggi al cliente:

- Controllo completo e granulare sull'utilizzo e la gestione della protezione TDE;

- Trasparenza dell'utilizzo del protettore TDE;

- Capacità di implementare la separazione dei compiti nella gestione delle chiavi e dei dati all'interno dell'organizzazione;

- L'amministratore del Vault delle chiavi può revocare le autorizzazioni di accesso alla chiave per rendere inaccessibile il database crittografato;

- Gestione centralizzata delle chiavi in AKV;

- Maggiore fiducia da parte dei clienti finali, poiché AKV è progettato in modo tale che Microsoft non possa vedere né estrarre chiavi di crittografia;

## <a name="how-customer-managed-tde-works"></a>Come funziona TDE gestito dal cliente

![Configurazione e funzionamento del TDE gestito dal cliente](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Affinché il server sia in grado di utilizzare la protezione TDE archiviata in AKV per la crittografia del file DEK, l'amministratore dell'insieme di credenziali delle chiavi deve assegnare i seguenti diritti di accesso al server utilizzando la propria identità AAD univoca:

- **get** - per il recupero della parte pubblica e delle proprietà della chiave nell'insieme di credenziali delle chiavi

- **wrapKey** - per essere in grado di proteggere (crittografare) DEK

- **unwrapKey** - per essere in grado di rimuovere la protezione (decrittografare) DEK

L'amministratore dell'insieme di credenziali delle chiavi può inoltre [abilitare la registrazione degli eventi di controllo dell'insieme](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)di credenziali delle chiavi, in modo che possano essere controllati in un secondo momento.

Quando il server è configurato per utilizzare una protezione TDE da AKV, il server invia la chiave DEK di ogni database abilitato per TDE all'insieme di credenziali delle chiavi per la crittografia. L'insieme di credenziali delle chiavi restituisce il valore DEK crittografato, che viene quindi archiviato nel database utente.

Quando necessario, il server invia la chiave DEK protetta all'insieme di credenziali delle chiavi per la decrittografia.

I revisori possono usare Monitoraggio di Azure per esaminare i log AuditEvent dell'insieme di credenziali delle chiavi, se la registrazione è abilitata.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Requisiti per la configurazione di TDE gestito dal cliente

### <a name="requirements-for-configuring-akv"></a>Requisiti per la configurazione di AKV

- L'insieme di credenziali delle chiavi e il database SQL/istanza gestita devono appartenere allo stesso tenant di Azure Active Directory.Key vault and SQL Database/managed instance must belong to the same Azure Active Directory tenant. Le interazioni tra tenant di insieme di credenziali delle chiavi e server non sono supportate. Per spostare le risorse in seguito, TDE con AKV dovrà essere riconfigurato. Ulteriori informazioni sullo [spostamento delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- [La](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) funzionalità di eliminazione temporanea deve essere abilitata nell'insieme di credenziali delle chiavi, per proteggersi dall'eliminazione accidentale delle chiavi (o dell'insieme di credenziali dei dati). Le risorse eliminate temporaneamente vengono conservate per 90 giorni, a meno che nel frattempo non vengano ripristinate o eliminate dal cliente. Alle azioni di *recupero* e *pulizia* sono associate autorizzazioni specifiche nei criteri di accesso dell'insieme di credenziali delle chiavi. La funzionalità di eliminazione temporanea è disattivata per impostazione predefinita e può essere abilitata tramite [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) o [l'interfaccia della riga di comando.](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete) Non può essere abilitato tramite il portale di Azure.It cannot be enabled via Azure portal.  

- Concedere al server di database SQL o all'istanza gestita l'accesso all'insieme di credenziali delle chiavi (get, wrapKey, unwrapKey) usando l'identità di Azure Active Directory.Grant the SQL Database server or managed instance access to the key vault (get, wrapKey, unwrapKey) using its Azure Active Directory identity. Quando si usa il portale di Azure, viene creata automaticamente l'identità di Azure AD. Quando si usa PowerShell o l'interfaccia della riga di comando, l'identità di Azure AD deve essere creata in modo esplicito e deve essere verificato il completamento. Vedere [Configurare TDE con BYOK](transparent-data-encryption-byok-azure-sql-configure.md) e [Configurare TDE con BYOK per l'istanza gestita](https://aka.ms/sqlmibyoktdepowershell) per istruzioni dettagliate relative all'uso con PowerShell.

- Quando si utilizza il firewall con AKV, è necessario abilitare l'opzione *Consenti ai servizi Microsoft attendibili*di ignorare il firewall .

### <a name="requirements-for-configuring-tde-protector"></a>Requisiti per la configurazione della protezione TDE

- Il protettore TDE può essere solo asimmetrico, RSA 2048 o RSA HSM 2048 chiave.

- La data di attivazione della chiave (se impostata) deve essere una data e un'ora nel passato. La data di scadenza (se impostata) deve essere una data e un'ora future.

- La chiave deve essere nello stato *Abilitato.*

- Se si importa una chiave esistente nell'insieme di credenziali delle chiavi, assicurarsi di fornirla nei formati di file supportati (con estensione pfx, byok o backup).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Suggerimenti per la configurazione di TDE gestito dal clienteRecommendations when configuring customer-managed TDE

### <a name="recommendations-when-configuring-akv"></a>Raccomandazioni per la configurazione di AKV

- Associare al massimo 500 database General Purpose o 200 Business Critical in totale a un insieme di credenziali delle chiavi in un'unica sottoscrizione per garantire un'elevata disponibilità quando il server accede alla protezione TDE nell'insieme di credenziali delle chiavi. Queste cifre si basano sull'esperienza e documentate nei limiti del [servizio dell'insieme](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)di credenziali delle chiavi. L'intenzione in questo caso è di evitare problemi dopo il failover del server, in quanto attiverà un numero di operazioni chiave sull'insieme di credenziali quanti sono i database in tale server.

- Impostare un blocco delle risorse sull'insieme di credenziali delle chiavi per controllare chi può eliminare questa risorsa critica e impedire l'eliminazione accidentale o non autorizzata. Ulteriori informazioni sui [blocchi delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Abilitare il controllo e la creazione di report su tutte le chiavi di crittografia: l'insieme di credenziali delle chiavi fornisce log che sono facili da inserire in altre informazioni di sicurezza e strumenti di gestione degli eventi. Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) è un esempio di servizio già integrato.

- Collegare ogni server con due insiemi di credenziali delle chiavi che risiedono in aree diverse e contengono lo stesso materiale della chiave, per garantire un'elevata disponibilità dei database crittografati. Contrassegnare solo la chiave dall'insieme di credenziali delle chiavi nella stessa area di un protettore TDE. Il sistema passerà automaticamente all'insieme di credenziali delle chiavi nell'area remota se si verifica un'interruzione che interessa l'insieme di credenziali delle chiavi nella stessa area.

### <a name="recommendations-when-configuring-tde-protector"></a>Consigli per la configurazione della protezione TDERecommendations when configuring TDE protector
- Conservare una copia del protettore TDE in un luogo sicuro o depositarla al servizio di deposito a garanzia.

- Se la chiave viene generata nell'insieme di credenziali delle chiavi, creare un backup della chiave prima di utilizzare la chiave in AKV per la prima volta. Il backup può essere ripristinato solo in un insieme di credenziali delle chiavi di Azure.Backup can be restored to an Azure Key Vault only. Ulteriori informazioni sul comando [Backup-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Creare un nuovo backup ogni volta che vengono apportate modifiche alla chiave (ad esempio attributi chiave, tag, ACL).

- **Mantenere le versioni precedenti** della chiave nell'insieme di credenziali delle chiavi durante la rotazione delle chiavi in modo tale da poter ripristinare i backup del database meno recenti. Quando la protezione TDE viene modificata per un database, i backup precedenti del database **non vengono aggiornati** per utilizzare la protezione TDE più recente. Al momento del ripristino, ogni backup richiede la protezione TDE con cui è stato crittografato al momento della creazione. Le rotazioni delle chiavi possono essere eseguite seguendo le istruzioni riportate in [Ruotare la protezione di Transparent Data Encryption con PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Mantenere tutte le chiavi utilizzate in precedenza in AKV anche dopo il passaggio alle chiavi gestite dal servizio. Garantisce il ripristino dei backup del database con le protezioni TDE archiviate in AKV.  Le protezioni TDE create con l'insieme di credenziali delle chiavi di Azure devono essere mantenute fino a quando non sono stati creati tutti i backup archiviati rimanenti con chiavi gestite dal servizio. Creare copie di backup recuperabili di queste chiavi utilizzando [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Per rimuovere una chiave potenzialmente compromessa durante un incidente di sicurezza senza il rischio di perdita di dati, seguire i passaggi dalla procedura di [rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Protettore TDE inaccessibile

Quando la crittografia dei dati trasparente è configurata per utilizzare una chiave gestita dal cliente, è necessario un accesso continuo alla protezione TDE affinché il database rimanga online. Se il server perde l'accesso alla protezione TDE gestita dal cliente in AKV, in fino a 10 minuti un database inizierà a negare tutte le connessioni con il messaggio di errore corrispondente e a modificarne lo stato in *Inaccessibile*. L'unica azione consentita in un database nello stato Inaccessibile è l'eliminazione.

> [!NOTE]
> Se il database non è accessibile a causa di un'interruzione della rete intermittente, non è necessaria alcuna azione e i database torneranno automaticamente in linea.

Dopo il ripristino dell'accesso alla chiave, il ripristino del database richiede ulteriore tempo e passaggi, che possono variare in base al tempo trascorso senza l'accesso alla chiave e alle dimensioni dei dati nel database:

- Se l'accesso alla chiave viene ripristinato entro 8 ore, il database verrà automaticamente ripristinato entro l'ora successiva.

- Se l'accesso alla chiave viene ripristinato dopo più di 8 ore, la correzione automatica non è possibile e la riconnessione del database richiede passaggi aggiuntivi nel portale e può richiedere una notevole quantità di tempo a seconda delle dimensioni del database. Una volta che il database è tornato online, le impostazioni a livello di server configurate in precedenza, ad esempio la configurazione del gruppo di [failover,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) la cronologia di ripristino temporizzato e i tag **andranno perse.** Pertanto, è consigliabile implementare un sistema di notifica che consente di identificare e risolvere i problemi di accesso chiave sottostante entro 8 ore.

### <a name="accidental-tde-protector-access-revocation"></a>Revoca accidentale dell'accesso al protettore TDE

Può accadere che qualcuno con diritti di accesso sufficienti all'insieme di credenziali delle chiavi disattivi accidentalmente l'accesso del server alla chiave:

- revocando le autorizzazioni *get*, *wrapKey*, *unwrapKey* dell'insieme di credenziali delle chiavi dal server

- l'eliminazione della chiave

- eliminazione dell'insieme di credenziali delle chiavi

- modifica delle regole del firewall dell'insieme di credenziali delle chiavi

- eliminazione dell'identità gestita del server in Azure Active Directory

Ulteriori informazioni sulle [cause più comuni per l'inaccessibilità del database](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitoraggio del TDE gestito dal cliente

Per monitorare lo stato del database e abilitare gli avvisi per la perdita dell'accesso alla protezione TDE, configurare le funzionalità di Azure seguenti:To monitor database state and to enable alerting for loss of TDE protector access, configure the following Azure features:
- [Integrità risorse di Azure](https://docs.microsoft.com/azure/service-health/resource-health-overview). Un database inaccessibile che ha perso l'accesso alla protezione TDE verrà visualizzato come "Non disponibile" dopo che la prima connessione al database è stata negata.
- [Registro attività](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) quando l'accesso alla protezione TDE nell'insieme di credenziali delle chiavi gestito dal cliente ha esito negativo, le voci vengono aggiunte al log attività.  La creazione di avvisi per questi eventi consentirà di ripristinare l'accesso il prima possibile.
- [I gruppi di](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) azioni possono essere definiti per inviare notifiche e avvisi in base alle preferenze, ad esempio E-mail/SMS/Push/Voice, App logica, Webhook, ITSM o Runbook di automazione.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Backup e ripristino del database con TDE gestito dal cliente

Una volta che un database viene crittografato con TDE utilizzando una chiave da Key Vault, anche tutti i backup appena generati vengono crittografati con la stessa protezione TDE. Quando la protezione TDE viene modificata, i vecchi backup del database **non vengono aggiornati** per utilizzare la protezione TDE più recente.

Per ripristinare un backup crittografato con una protezione TDE dall'insieme di credenziali delle chiavi, assicurarsi che il materiale della chiave sia disponibile per il server di destinazione. Pertanto, è consigliabile mantenere tutte le versioni precedenti della protezione TDE nell'insieme di credenziali delle chiavi, in modo da poter ripristinare i backup del database.

> [!IMPORTANT]
> In qualsiasi momento non può essere impostato più di un protettore TDE per un server. È la chiave contrassegnata con "Rendi la chiave la protezione TDE predefinita" nel pannello del portale di Azure.It's the key marked with "Make the key the default TDE protector" in the Azure portal blade. Tuttavia, più chiavi aggiuntive possono essere collegate a un server senza contrassegnarle come protezione TDE. Queste chiavi non vengono utilizzate per proteggere DEK, ma possono essere utilizzate durante il ripristino da un backup, se il file di backup viene crittografato con la chiave con l'identificazione personale corrispondente.

Se la chiave necessaria per il ripristino di un backup non è più disponibile per il server `<Servername>` di destinazione, viene restituito il seguente \<messaggio di \<errore nella prova di ripristino: "Il server di destinazione non ha accesso a tutti gli URI AKV creati tra timestamp #1> e timestamp #2>. Riprovare dopo il ripristino di tutti gli URI AKV."

Per attenuarlo, eseguire il cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) per il server logico del database SQL di destinazione o [Get-AzSqlInstanceKeyVault](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) per l'istanza gestita di destinazione per restituire l'elenco delle chiavi disponibili e identificare quelle mancanti. Per garantire che tutti i backup possano essere ripristinati, assicurarsi che il server di destinazione per il ripristino abbia accesso a tutte le chiavi necessarie. Queste chiavi non devono essere contrassegnate come protettore TDE.

Per altre informazioni sul ripristino dei backup per il database SQL, vedere [Recuperare un database SQL di Azure](sql-database-recovery-using-backups.md). Per ulteriori informazioni sul ripristino del backup per il pool SQL, vedere [Ripristinare un pool SQL.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) Per il backup/ripristino nativo di SQL Server con l'istanza gestita, vedere [Guida introduttiva: Ripristinare un database in un'istanza gestitaFor](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) sql Server's native backup/restore with managed instance, see Quickstart: Restore a database to a Managed Instance

Ulteriore considerazione per i file di registro: i file di registro di backup rimangono crittografati con la protezione TDE originale, anche se è stato ruotato e il database sta ora utilizzando una nuova protezione TDE.  In fase di ripristino, per ripristinare il database saranno necessarie entrambe le chiavi.  Se il file di log usa una protezione TDE archiviata nell'insieme di credenziali delle chiavi di Azure, questa chiave sarà necessaria in fase di ripristino, anche se il database è stato modificato per usare TDE gestito dal servizio nel frattempo.

## <a name="high-availability-with-customer-managed-tde"></a>Disponibilità elevata con TDE gestito dal cliente

Anche nei casi in cui non è presente una ridondanza geografica configurata per il server, è consigliabile configurare il server per l'utilizzo di due diversi insiemi di credenziali delle chiavi in due aree diverse con lo stesso materiale della chiave. Può essere eseguita creando una protezione TDE usando l'insieme di credenziali della chiave primaria, che si trova nella stessa area del server e clonando la chiave in un insieme di credenziali delle chiavi in un'area di Azure diversa, in modo che il server abbia accesso a un secondo insieme di credenziali delle chiavi nel caso dell'insieme di credenziali della chiave primaria si verifica un'interruzione mentre il database è in funzione.

Utilizzare il cmdlet Backup-AzKeyVaultKey per recuperare la chiave in formato crittografato dall'insieme di credenziali della chiave primaria, quindi utilizzare il cmdlet Restore-AzKeyVaultKey e specificare un insieme di credenziali delle chiavi nella seconda area per clonare la chiave. In alternativa, usare il portale di Azure per eseguire il backup e il ripristino della chiave. La chiave nell'insieme di credenziali della chiave secondaria nell'altra area non deve essere contrassegnata come protezione TDE e non è nemmeno consentita.

 Se si verifica un'interruzione che interessa l'insieme di credenziali della chiave primaria e solo allora, il sistema passerà automaticamente all'altra chiave collegata con la stessa identificazione personale nell'insieme di credenziali della chiave secondaria, se esistente. Si noti tuttavia che il passaggio non si verificherà se la protezione TDE non è accessibile a causa dei diritti di accesso revocato o perché la chiave o l'insieme di credenziali delle chiavi viene eliminato, in quanto potrebbe indicare che il cliente desiderava limitare l'accesso alla chiave da parte del server.

![HA a server singolo](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR e TDE gestito dal cliente

In entrambi gli scenari di replica geografica attiva e gruppi di [failover,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) ogni server coinvolto richiede un insieme di credenziali delle chiavi separato, che deve essere collocato insieme al server nella stessa area di Azure.In both [active geo-replication](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) and failover groups scenarios, each server involved requires a separate key vault, that must be co-located with the server in the same Azure region. Il cliente è responsabile di mantenere coerente il materiale della chiave negli insiemi di credenziali delle chiavi, in modo che il geo-secondario sia sincronizzato e possa assumere la stessa chiave dell'insieme di credenziali delle chiavi locale se il database della chiave locale diventa inaccessibile a causa di un'interruzione nell'area e viene attivato un failover . È possibile configurare fino a quattro database secondari e il concatenamento (secondari dei secondari) non è supportato.

Per evitare problemi durante la creazione o durante la replica geografica a causa di materiale della chiave incompleto, è importante seguire queste regole durante la configurazione di TDE gestito dal cliente:

- Tutti gli insiemi di credenziali delle chiavi coinvolti devono avere le stesse proprietà e gli stessi diritti di accesso per i rispettivi server.

- Tutti gli insiemi di credenziali delle chiavi coinvolti devono contenere materiale della chiave identico. Si applica non solo alla protezione TDE corrente, ma a tutte le protezioni TDE precedenti che possono essere utilizzate nei file di backup.

- Sia l'impostazione iniziale che la rotazione della protezione TDE devono essere eseguite prima sul database secondario e quindi sul database primario.

![Gruppi di failover e ripristino di emergenza geografico](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Per testare un failover, seguire i passaggi descritti in Panoramica della [replica geografica attiva.](sql-database-geo-replication-overview.md) Deve essere eseguita regolarmente per confermare che le autorizzazioni di accesso per SQL a entrambi gli insiemi di credenziali delle chiavi sono state mantenute.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile controllare i seguenti script di esempio di PowerShell per le operazioni comuni con TDE gestito dal cliente:

- [Ruotare la protezione trasparente della crittografia dei dati per il database SQL tramite PowerShellRotate the Transparent Data Encryption Protector for SQL Database Using PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Rimuovere una protezione TDE (Transparent Data Encryption) per il database SQL tramite PowerShellRemove a Transparent Data Encryption (TDE) protector for SQL Database using PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Gestire Transparent Data Encryption in un'istanza gestita con la propria chiave tramite PowerShellManage Transparent Data Encryption in a Managed Instance with your own key using PowerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
