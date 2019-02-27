---
title: TDE - Integrazione con Azure Key Vault o Bring Your Own Key (BYOK) - Database SQL di Azure | Microsoft Docs
description: 'Supporto Bring Your Own Key (BYOK) per Transparent Data Encryption (TDE) con Azure Key Vault per database SQL e Azure SQL Data Warehouse. TDE con BYOK: panoramica, vantaggi, funzionamento, considerazioni ed elementi consigliati.'
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: c8c2c58250b6f806b48241ad1bb2a85202b9b67a
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456878"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure SQL Transparent Data Encryption con chiavi gestite dal cliente in Azure Key Vault: supporto BYOK (Bring Your Own Key).

[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) con integrazione Azure Key Vault consente di crittografare la chiave di crittografia (DEK) del database con una chiave asimmetrica gestita dal cliente chiamata protezione TDE. Questo è anche comunemente noto come supporto Bring Your Own Key (BYOK) per Transparent Data Encryption.  Nello scenario BYOK, la protezione TDE è archiviata in un'istanza di [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), il sistema di gestione delle chiavi esterne basato sul cloud di Azure, che viene gestita dal cliente ed è di sua proprietà. La protezione TDE può essere [generata](https://docs.microsoft.com/en-us/azure/key-vault/about-keys-secrets-and-certificates) da Key Vault o [trasferita](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) in Key Vault da un dispositivo HSM locale. La chiave DEK della crittografia TDE, archiviata nella pagina di avvio di un database, viene crittografata e decrittografata dalla protezione TDE, che rimane archiviata in modo permanente in Azure Key Vault.  Per decrittografare e crittografare la chiave DEK, il database SQL deve possedere le autorizzazioni di accesso all'insieme di credenziali delle chiavi del cliente. Se le autorizzazioni del server SQL per l'insieme di credenziali delle chiavi vengono revocate, un database non è accessibile e tutti i dati rimangono crittografati. Per il database SQL di Azure, la protezione TDE è impostata a livello di server logico SQL e viene ereditata da tutti i database associati al server. Per l'[istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), la protezione TDE è impostata a livello di istanza e viene ereditata da tutti i database *crittografati* nell'istanza. Salvo diversa indicazione, in questo documento il termine *server* fa riferimento sia al server che all'istanza.

Grazie all'integrazione di TDE con Azure Key Vault, gli utenti possono controllare le attività di gestione delle chiavi, tra cui le rotazioni, i backup e le autorizzazioni dell'insieme di credenziali, nonché abilitare il controllo o il reporting per tutte le protezioni TDE usando Azure Key Vault. Key Vault offre la gestione centralizzata delle chiavi, sfrutta i moduli di protezione hardware accuratamente monitorati (HSM) e consente la separazione dei compiti tra la gestione delle chiavi e i dati per contribuire a rispettare la conformità con i criteri di sicurezza.  

L'integrazione TDE con Azure Key Vault offre i vantaggi seguenti:

- Maggiore trasparenza e un controllo granulare con la possibilità di gestire in autonomia la protezione TDE
- Possibilità di revocare le autorizzazioni in qualsiasi momento per rendere il database inaccessibile
- Gestione centralizzata delle protezioni TDE (con altre chiavi e segreti usati in altri servizi di Azure), ospitate in Key Vault
- Separazione delle responsabilità di gestione delle chiavi e dei dati all'interno dell'organizzazione, per supportare la separazione dei compiti
- Maggiore fiducia da parte dei client, poiché Key Vault è progettato in modo che Microsoft non possa vedere o estrarre le chiavi di crittografia.
- Supporto per la rotazione delle chiavi

> [!IMPORTANT]
> Per chi usa TDE gestita dal servizio e desidera iniziare a usare Key Vault, la protezione TDE rimane abilitata durante il processo del passaggio a una protezione TDE in Key Vault. Non vi è alcun il tempo di inattività o riesecuzione della crittografia dei file di database. Il passaggio da una chiave gestita dal servizio a una chiave di Key Vault richiede solo la riesecuzione della crittografia della chiave di crittografia del database (DEK), che è una operazione online rapida.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>In che modo TDE con l'integrazione di Azure Key Vault supporta il lavoro

![Autenticazione del server per l'insieme in Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Quando TDE viene configurato per la prima volta per l'uso di una protezione TDE da Key Vault, il server invia la chiave DEK di ogni database abilitato per TDE in Key Vault per una richiesta di esecuzione del wrapping della chiave. Key Vault restituisce la chiave di crittografia del database crittografato, che viene archiviata nel database utente.  

> [!IMPORTANT]
> È importante notare che **una volta che una protezione TDE è memorizzata in Azure Key Vault, non si distacca mai da Azure Key Vault**. Il server può solo inviare richieste di operazione della chiave al materiale della chiave di protezione TDE in Key Vault e **non accedere mai o memorizzare nella cache la protezione TDE**. L'amministratore di Key Vault ha il diritto di revocare le autorizzazioni di Key Vault del server in qualsiasi momento, in tal caso, tutte le connessioni al server vengono interrotte.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Linee guida per la configurazione di TDE con Azure Key Vault

### <a name="general-guidelines"></a>Linee guida generali

- Verificare che Azure Key Vault e il database o l'istanza gestita di database SQL di Azure si troveranno stesso tenant.  Le interazioni di server e insieme di credenziali delle chiavi tra tenant **non sono supportate**.
- Decidere quali sottoscrizioni usare per le risorse necessarie; lo spostamento del server tra sottoscrizioni in un secondo momento richiede una nuova configurazione di TDE con le BYOK. Altre informazioni sullo [spostamento di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- Quando si configura TDE con Azure Key Vault, è importante prendere in considerazione il carico rispetto all'insieme di credenziali delle chiavi da parte di operazioni di wrapping/annullamento di wrapping ripetute. Ad esempio, poiché tutti i database associati a un serve di database SQL usano la stessa protezione TDE, un failover del server attiverà tante operazioni chiave nell'insieme di credenziali quante ve ne sono database nel server. Sulla base della nostra esperienza e dei [limiti del servizio dell'insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits) documentati, è consigliabile associare al massimo 500 database standard/per uso generico o 200 premium/business critical con un Azure Key Vault in una singola sottoscrizione per garantire una disponibilità costantemente elevata durante l'accesso alla protezione TDE nell'insieme di credenziali.
- Consigliato: conservare una copia della protezione TDE in locale.  Ciò richiede un dispositivo HSM per creare una protezione TDE locale e un sistema di deposito delle chiavi per archiviare una copia locale della protezione TDE.  Informazioni su [come trasferire una chiave da un modulo HSM locale ad Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

### <a name="guidelines-for-configuring-azure-key-vault"></a>Indicazioni per la configurazione di Azure Key Vault

- Creare un insieme di credenziali delle chiavi con [eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) abilitata per evitare la perdita in caso di eliminazione accidentale della chiave o dell'insieme di credenziali delle chiavi.  È necessario usare [PowerShell per abilitare la proprietà "eliminazione temporanea"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) nell'insieme di credenziali delle chiavi (questa opzione non è disponibile dal portale di Azure Key Vault, ma è richiesta da Azure SQL):  
  - Le risorse eliminate in modo temporaneo vengono conservate per un periodo di tempo prestabilito di 90 giorni, qualora non recuperate o eliminate definitivamente.
  - Alle azioni di **recupero** ed **eliminazione definitiva** sono associate autorizzazioni specifiche nei criteri di accesso dell'insieme di credenziali delle chiavi.
- Impostare un blocco risorsa per l'insieme di credenziali delle chiavi per controllare chi può eliminare questa risorsa critica e impedire eliminazioni accidentali o non autorizzate.  [Altre informazioni sul blocco risorsa](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Concedere l'accesso al server di database SQL all'insieme di credenziali delle chiavi usando la relativa identità di Azure Active Directory (Azure AD).  Quando si usa l'interfaccia utente del portale, l'identità di Azure AD viene creata automaticamente e vengono concesse le autorizzazioni di accesso dell'insieme di credenziali delle chiavi al server.  Usando PowerShell per configurare TDE con BYOK, l'identità di Azure AD deve essere creata, è necessario verificarne il completamento dell'operazione. Vedere [Configurare TDE con BYOK](transparent-data-encryption-byok-azure-sql-configure.md) e [Configurare TDE con BYOK per l'istanza gestita](http://aka.ms/sqlmibyoktdepowershell) per istruzioni dettagliate relative all'uso con PowerShell.

  > [!NOTE]
  > Se l'identità Azure AD **viene accidentalmente eliminata o vengono revocate le autorizzazioni del server** usando i criteri di accesso dell'insieme di credenziali della chiavi, il server perde l'accesso all'insieme di credenziali delle chiavi e i database crittografati TDE vengono resi inaccessibili entro 24 ore.

- Quando si usano firewall e reti virtuali con Azure Key Vault, è necessario configurare quanto segue: in Consentire ai servizi Microsoft attendibili di ignorare il firewall, selezionare SÌ

 > [!NOTE]
 > Se i database SQL crittografati TDE perdono l'accesso all'insieme di credenziali delle chiavi perché non possono ignorare il firewall, vengono resi inaccessibili entro 24 ore.

- Abilitare il controllo e la creazione di report per tutte le chiavi di crittografia: Key Vault include log che si inseriscono facilmente in altri strumenti di gestione di sicurezza delle informazioni e degli eventi (SIEM). Operations Management Suite (OMS) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) è un esempio di un servizio già integrato.
- Per garantire la disponibilità elevata dei database crittografati, configurare ogni server di Database SQL con due Azure Key Vault che risiedono in aree diverse.

### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Linee guida per la configurazione della protezione TDE (chiave asimmetrica)

- Creare la chiave di crittografia in locale in un dispositivo HSM locale. Assicurarsi che si tratti di una chiave RSA 2048 asimmetrica in modo tale da memorizzarla in Azure Key Vault.
- Depositare la chiave in un sistema di deposito delle chiavi.  
- Importare il file della chiave di crittografia (con estensione pfx, byok o backup) in Azure Key Vault.

   > [!NOTE]
   > A scopo di test, è possibile creare una chiave con Azure Key Vault, tale chiave, tuttavia non può essere depositata poiché la chiave privata non può distaccarsi mai dall'insieme di credenziali delle chiavi.  Eseguire sempre il backup e il deposito delle chiavi usate per crittografare i dati di produzione, come la perdita della chiave (eliminazione accidentale dell'insieme di credenziali delle chiavi, scadenza e così via) comporta una perdita di dati permanente.

- Usare una chiave senza data di scadenza e non impostare una data di scadenza per una chiave già in uso: **una volta scaduta la chiave, i database crittografati perdono l'accesso alla protezione TDE e vengono resi inaccessibili entro 24 ore**.
- Verificare che la chiave sia abilitata e disponga delle autorizzazioni per eseguire le operazioni *Ottieni*, *Esegui il wrapping della chiave* e *Annulla il wrapping della chiave*.
- Creare un backup della chiave di Azure Key Vault prima di usare per la prima volta la chiave in Azure Key Vault. Altre informazioni sul comando [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Creare un nuovo backup ogni volta che vengono apportate modifiche alla chiave (ad esempio, aggiungere gli ACL, aggiungere i tag, aggiungere gli attributi chiave).
- **Mantenere le versioni precedenti** della chiave nell'insieme di credenziali delle chiavi durante la rotazione delle chiavi in modo tale da poter ripristinare i backup del database meno recenti. Quando la protezione TDE viene modificata per un database, i backup precedenti del database **non vengono aggiornati** per l'uso con la protezione TDE più recente.  Ogni backup richiede che la protezione TDE sia stata creata in fase di ripristino. Le rotazioni delle chiavi possono essere eseguite seguendo le istruzioni riportate in [Ruotare la protezione di Transparent Data Encryption con PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Mantenere tutte le chiavi usate in precedenza in Azure Key Vault dopo il ripristino delle chiavi gestite dal servizio.  In questo modo è possibile ripristinare i backup dei database con le protezioni TDE archiviate in Azure Key Vault.  Le protezioni TDE create con Azure Key Vault devono essere mantenute fino a quando non sono stati creati tutti i backup memorizzati con chiavi gestite dal servizio.  
- Creare copie di backup recuperabili di tali chiavi usando [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Per rimuovere una chiave potenzialmente compromessa durante un evento imprevisto di sicurezza senza il rischio di perdita di dati, seguire i passaggi descritti in [Rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="high-availability-geo-replication-and-backup--restore"></a>Disponibilità elevata, replica geografica e backup/ripristino

### <a name="high-availability-and-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza

Come configurare la disponibilità elevata con Azure Key Vault dipende dalla configurazione del database e del server di database SQL, di seguito sono presenti le configurazioni consigliate per due casi distinti.  Il primo caso è un database autonomo o un server di database SQL senza alcuna ridondanza geografica configurata.  Il secondo caso è un database o un server di database SQL configurato con i gruppi di failover o di ridondanza geografica, in cui è necessario assicurare che ogni copia con ridondanza geografica abbia un Azure Key Vault locale all'interno del gruppo di failover per garantire il funzionamento di failover geografici.

Nel primo caso, se si richiedono un'elevata disponibilità di un database e un server di database SQL senza alcuna ridondanza geografica configurata, si consiglia di configurare il server per usare due diversi insiemi di credenziali delle chiavi in due aree diverse con lo stesso materiale della chiave. Ciò può essere ottenuto creando una protezione TDE con l'insieme di credenziali delle chiavi primarie posizionato nella stessa area del server di database SQL e clonando la chiave in un insieme di credenziali delle chiavi in un'area di Azure diversa, in modo che il server abbia accesso a un secondo insieme di credenziali delle chiavi se l'insieme di credenziali delle chiavi primarie subisce un'interruzione mentre il database è attivo e in esecuzione. Usare il cmdlet Backup-AzureKeyVaultKey per recuperare la chiave in formato crittografato dall'insieme di credenziali delle chiavi primarie e quindi usare il cmdlet Restore-AzureKeyVaultKey e specificare un insieme di credenziali delle chiavi nella seconda area.

![Server singolo a disponibilità elevata e nessun ripristino di emergenza geografico](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Configurazione di ripristino di emergenza geografico con Azure Key Vault

Per mantenere la disponibilità elevata delle protezioni TDE per i database crittografati, è necessario configurare Azure Key Vault ridondanti in base ai gruppi di failover di database SQL esistenti o desiderati o in base alle istanze di replica geografica attiva.  Ogni server con replica geografica richiede un insieme di credenziali delle chiavi separato, che deve essere situato con il server nella stessa area di Azure. Se un database primario diventasse inaccessibile a causa di un'interruzione del servizio in un'unica area e venga attivato un failover, il database secondario sarà in grado di assumere il controllo usando l'insieme di credenziali delle chiavi secondarie.

Per i database SQL di Azure con replica geografica, è necessaria la configurazione di Azure Key Vault seguente:

- Un database primario con un insieme di credenziali delle chiavi nell'area e un database secondario con un insieme di credenziali delle chiavi nell'area.
- È richiesto almeno un database secondario, sono supportati fino a quattro database secondari.
- Le repliche secondarie dei database secondari (concatenamento) non sono supportate.

La sezione seguente esaminerà i passaggi di installazione e configurazione in modo più dettagliato.

### <a name="azure-key-vault-configuration-steps"></a>Passaggi per la configurazione di Azure Key Vault

- Installare [PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-5.6.0)
- Creare due Azure Key Vault in due aree differenti tramite [PowerShell per abilitare la proprietà "eliminazione temporanea"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) nell'insieme di credenziali delle chiavi (questa opzione non è disponibile dal portale di Azure Key Vault, ma è richiesta da SQL).
- Entrambe gli Azure Key Vault devono trovarsi in due aree disponibili nella stessa area geografica di Azure per l'esecuzione del backup e il ripristino delle chiavi.  Se è necessario che i due insiemi di credenziali delle chiavi siano posizionati in aree geografiche diverse per rispettare i requisiti di ripristino di emergenza geografico di SQL, seguire la [procedura BYOK](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) che consente l'importazione da un modulo HSM locale delle chiavi.
- Creare una nuova chiave nell'insieme di credenziali delle chiavi:  
  - RSA/RSA-HSA 2048 key
  - Nessuna data di scadenza
  - La chiave è abilitata e dispone delle autorizzazioni per eseguire le operazioni Ottieni, Esegui il wrapping della chiave e Annulla il wrapping della chiave
- Eseguire il backup della chiave primaria e ripristinare la chiave per il secondo insieme di credenziali delle chiavi.  Vedere [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) e [Restore-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-5.5.0).

### <a name="azure-sql-database-configuration-steps"></a>Passaggi per la configurazione del database SQL di Azure

I seguenti passaggi di configurazione sono diversi a seconda che si inizi con una nuova distribuzione di SQL o se si usa una distribuzione di ripristino di emergenza geografico di SQL già esistente.  Prima di tutto, vengono descritti i passaggi di configurazione di una nuova distribuzione e quindi viene spiegato come assegnare protezioni TDE archiviate in Azure Key Vault a una distribuzione esistente che dispone già di un collegamento di ripristino di emergenza geografico stabilito.

**Passaggi per una nuova distribuzione**:

- Creare i due server di database SQL nelle stesse due aree degli insiemi di credenziali delle chiavi creati in precedenza.
- Selezionare il riquadro TDE dei server di database SQL e per ogni server di database SQL:  
  - Selezionare l'Azure Key Vault nella stessa area
  - Selezionare la chiave da usare come protezione TDE, ogni server userà la copia locale della protezione TDE.
  - Questa operazione nel portale creerà un [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) per il server di database SQL, che consente di assegnare le autorizzazioni del server di database SQL per accedere a un insieme di credenziali delle chiavi. Non eliminare questa identità. L'accesso potrà essere revocato rimuovendo invece le autorizzazioni in Azure Key Vault per il server di database SQL, che consente di assegnare le autorizzazioni del server di database SQL per accedere all'insieme di credenziali delle chiavi.
- Creare il database primario.
- Seguire il [materiale sussidiario di replica geografica attiva](sql-database-geo-replication-overview.md) per completare lo scenario, questo passaggio creerà il database secondario.

![Gruppi di failover e ripristino di emergenza geografico](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> È importante garantire che le stesse protezioni TDE siano presenti in entrambi gli insiemi di credenziali delle chiavi prima di stabilire il collegamento di replica geografica tra i database.

**Passaggi per un database SQL esistente con la distribuzione di ripristino di emergenza geografico**:

Poiché i server di database SQL esistono già e i database primari e secondari sono già stati assegnati, i passaggi per configurare Azure Key Vault devono essere eseguiti nell'ordine seguente:

- Iniziare con il server di database SQL logico che ospita il database secondario:
  - Assegnare l'insieme di credenziali delle chiavi che si trova nella stessa area
  - Assegnare la protezione TDE
- Andare adesso al server di database SQL che ospita il database primario:
  - Selezionare la stessa protezione TDE usata per il database secondario

![Gruppi di failover e ripristino di emergenza geografico](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>Quando si assegna l'insieme di credenziali delle chiavi al server, è importante iniziare con il server secondario.  Nel secondo passaggio, assegnare l'insieme di credenziali delle chiavi al server primario e aggiornare la protezione TDE, il collegamento di ripristino di emergenza geografico continuerà a funzionare perché a questo punto la protezione TDE usata dal database replicato è disponibile per entrambi i server.

Prima di abilitare Transparent Data Encryption con chiavi gestite dal cliente in Azure Key Vault per uno scenario di database SQL di ripristino di emergenza geografico, è importante creare e gestire due Azure Key Vault con contenuto identico nelle stesse aree che verranno usate per la replica geografica del database SQL.  "Contenuto identico" significa in particolare che entrambi gli insiemi di credenziali delle chiavi devono contenere copie delle stesse protezioni Transparent Data Encryption in modo che entrambi i server abbiano accesso all'uso di protezioni TDE da tutti i database.  In futuro, è necessario mantenere entrambi gli insiemi di credenziali delle chiavi nella sincronizzazione, il che significa che è necessario che contengano le stesse copie delle protezioni TDE dopo la rotazione delle chiavi, mantengano le versioni precedenti delle chiavi usate per i file di log o backup, le protezioni TDE devono mantenere le stesse proprietà della chiave e gli insiemi di credenziali delle chiavi devono mantenere le stesse autorizzazioni di accesso per SQL.  

Seguire i passaggi descritti in [Panoramica sulla replica geografica attiva](sql-database-geo-replication-overview.md) per testare e attivare un failover, che deve essere eseguito a intervalli regolari per verificare che siano state mantenute le autorizzazioni di accesso SQL per entrambi gli insiemi di credenziali delle chiavi.

### <a name="backup-and-restore"></a>Backup e ripristino

Una volta che un database viene crittografato con TDE usando una chiave di Key Vault, eventuali backup generati vengono inoltre crittografati con la stessa protezione TDE.

Per ripristinare un backup crittografato con protezione TDE da Key Vault, assicurarsi che il materiale della chiave sia ancora nell'insieme di credenziali originali con il nome della chiave originale. Quando la protezione TDE viene modificata per un database, i backup precedenti del database **non vengono** aggiornati per l'uso con la protezione TDE più recente. È pertanto consigliabile mantenere tutte le versioni precedenti della protezione TDE in Key Vault, in modo che i backup dei database possano essere ripristinati.

Se una chiave che potrebbe essere necessaria per ripristinare un backup non è più inclusa nell'insieme di credenziali delle chiavi originale, viene restituito il messaggio di errore seguente: "Il server di destinazione `<Servername>` non dispone dell'accesso a tutti gli URI AKV creati tra <Timestamp #1> e <Timestamp #2>. Ripetere l'operazione dopo il ripristino di tutti gli URI AKV."

Per risolvere questo problema, eseguire il cmdlet [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) per restituire l'elenco di chiavi da Key Vault aggiunte al server (a meno che non siano state eliminate da un utente). Per garantire che tutti i backup possano essere ripristinati, verificare che il server di destinazione per il backup abbia accesso a tutte queste chiavi.

```powershell
Get-AzureRmSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Per altre informazioni sul ripristino dei backup per il database SQL, vedere [Recuperare un database SQL di Azure](sql-database-recovery-using-backups.md). Per altre informazioni sul ripristino dei backup per SQL Data Warehouse, vedere [Recuperare un Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md).

Considerazione aggiuntiva per i file di log con backup: i file di log con backup rimangono crittografati con la crittografia TDE originale, anche se è stata ruotata la protezione TDE e il database ora usa una nuova protezione TDE.  In fase di ripristino, per ripristinare il database saranno necessarie entrambe le chiavi.  Se il file di log sta usando una protezione TDE archiviata in Azure Key Vault, questa chiave sarà necessaria in fase di ripristino, anche se il database è stato modificato per usare TDE gestita dal servizio nel frattempo.
