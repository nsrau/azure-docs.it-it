---
title: Risolvere i problemi di gestione di SSIS Integration Runtime in Azure Data Factory | Microsoft Docs
description: Questo articolo fornisce indicazioni per la risoluzione dei problemi di gestione di SSIS Integration Runtime (IR SSIS)
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 8abffdf443e26c03c38c12a3947a47a94157c9da
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609629"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Risolvere i problemi di gestione di SSIS Integration Runtime in Azure Data Factory

Questo articolo fornisce indicazioni per la risoluzione dei problemi di gestione in Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), noto anche come runtime di integrazione SSIS.

## <a name="overview"></a>Panoramica

Se si verificano problemi durante il provisioning o il deprovisioning del runtime di integrazione SSIS, verrà visualizzato un messaggio di errore nel portale di Microsoft Azure Data Factory o un errore restituito da un cmdlet di PowerShell. L'errore viene sempre visualizzato nel formato di un codice di errore con un messaggio di errore dettagliato.

Se il codice di errore è InternalServerError, il servizio presenta problemi temporanei ed è necessario ripetere l'operazione in un secondo momento. Se un nuovo tentativo non è utile, contattare il team di supporto di Azure Data Factory.

In caso contrario, tre dipendenze esterne principali possono causare errori: un server di database SQL di Azure o un'istanza gestita, uno script di installazione personalizzato e una configurazione di rete virtuale.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemi del server o dell'istanza gestita di database SQL di Azure

Un server di database SQL di Azure o un'istanza gestita è necessario se si esegue il provisioning di un runtime di integrazione SSIS con un database del catalogo SSIS. Il runtime di integrazione SSIS deve essere in grado di accedere al server di database SQL di Azure o a un'istanza gestita. Inoltre, l'account del server di database SQL di Azure o dell'istanza gestita deve disporre dell'autorizzazione per la creazione di un database del catalogo SSIS (SSISDB). Se si verifica un errore, nel portale di Data Factory verrà visualizzato un codice di errore con un messaggio di eccezione SQL dettagliato. Usare le informazioni nell'elenco seguente per risolvere i problemi relativi ai codici di errore.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Questo problema può essere visualizzato quando si effettua il provisioning di un nuovo runtime di integrazione SSIS o quando si esegue il runtime di integrazione. Se si verifica questo errore durante il provisioning del runtime di integrazione, è possibile che venga ricevuto un messaggio SqlException dettagliato nel messaggio di errore indicante uno dei seguenti problemi:

* Un problema di connessione di rete. Controllare se il nome host dell'istanza di SQL Server o gestita è accessibile. Verificare inoltre che nessun firewall o un gruppo di sicurezza di rete (NSG) blocchi l'accesso IR SSIS al server.
* Accesso non riuscito durante l'autenticazione SQL. L'account specificato non può accedere al database SQL Server. Assicurarsi di specificare l'account utente corretto.
* Accesso non riuscito durante l'autenticazione Microsoft Azure Active Directory (Azure AD) (identità gestita). Aggiungere l'identità gestita della Factory a un gruppo AAD e verificare che l'identità gestita disponga delle autorizzazioni di accesso al server di database del catalogo.
* Timeout della connessione. Questo errore è sempre causato da una configurazione relativa alla sicurezza. È consigliabile:
  1. Creare una nuova macchina virtuale.
  1. Aggiungere la macchina virtuale alla stessa Rete virtuale di Microsoft Azure di IR se il runtime di integrazione è in una rete virtuale.
  1. Installare SSMS e verificare lo stato del server di database SQL di Azure o dell'istanza gestita.

Per altri problemi, risolvere il problema visualizzato nel messaggio di errore dettagliato dell'eccezione SQL. Se i problemi persistono, contattare il team di supporto del server di database SQL di Azure o dell'istanza gestita.

Se viene visualizzato l'errore durante l'esecuzione del runtime di integrazione, è probabile che il gruppo di sicurezza di rete o le modifiche del firewall impediscano l'accesso al server di database SQL di Azure o all'istanza gestita. Sbloccare il nodo del ruolo di lavoro IR SSIS in modo che possa accedere al server di database SQL di Azure o all'istanza gestita.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Questo tipo di messaggio di errore potrebbe essere simile al seguente: "Il database ' SSISDB ' ha raggiunto la quota di dimensioni. Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni ". 

Le soluzioni possibili sono:
* Aumentare le dimensioni della quota di SSISDB.
* Modificare la configurazione di SSISDB per ridurre le dimensioni in base a:
   * Riduzione del periodo di memorizzazione e del numero di versioni del progetto.
   * Riduzione del periodo di memorizzazione del log.
   * Modifica del livello predefinito del log.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Questo errore indica che il server di database SQL di Azure o l'istanza gestita dispone già di un SSISDB e che è usato da un altro runtime di integrazione. È necessario fornire un server di database SQL di Azure o un'istanza gestita diversa oppure eliminare il database SSISDB esistente e riavviare il nuovo runtime di integrazione.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Questo errore può verificarsi per uno dei motivi seguenti:

* L'account utente configurato per il runtime di integrazione SSIS non dispone dell'autorizzazione per creare il database. È possibile concedere all'utente l'autorizzazione per creare il database.
* Si verifica un timeout durante la creazione del database, ad esempio un timeout di esecuzione o un'operazione di database. Ripetere l'operazione in un secondo momento. Se il nuovo tentativo non funziona, contattare il server di database SQL di Azure o il team di supporto di Istanza gestita.

Per altri problemi, controllare il messaggio di errore eccezione SQL e risolvere il problema indicato nei dettagli dell'errore. Se i problemi persistono, contattare il team di supporto del server di database SQL di Azure o dell'istanza gestita.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Questo tipo di messaggio di errore è simile al seguente: "Nome oggetto non valido ' Catalog. catalog_properties '". In questa situazione, si dispone già di un database denominato SSISDB ma non è stato creato dal runtime di integrazione SSIS oppure il database è in uno stato non valido causato da errori nell'ultimo provisioning del runtime di integrazione SSIS. È possibile eliminare il database esistente con il nome SSISDB oppure è possibile configurare un nuovo server di database SQL di Azure o istanza gestita per il runtime di integrazione.

## <a name="custom-setup-issues"></a>Problemi di installazione personalizzati

Il programma di installazione personalizzato fornisce un'interfaccia per aggiungere i propri passaggi di installazione durante il provisioning o la riconfigurazione del runtime di integrazione SSIS. Per altre informazioni, vedere [personalizzare la configurazione per il Integration Runtime Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Verificare che il contenitore includa solo i file di installazione personalizzati necessari. tutti i file nel contenitore verranno scaricati nel nodo del ruolo di lavoro IR SSIS. È consigliabile testare lo script di installazione personalizzato in un computer locale per correggere eventuali problemi di esecuzione dello script prima di eseguire lo script nel runtime di integrazione SSIS.

Il contenitore dello script di installazione personalizzato verrà verificato mentre è in esecuzione il runtime di integrazione, perché il runtime di integrazione SSIS viene aggiornato regolarmente. Questo aggiornamento richiede l'accesso al contenitore per scaricare lo script di installazione personalizzato e installarlo di nuovo. Il processo controlla anche se il contenitore è accessibile e se il file Main. cmd esiste.

Per eventuali errori che coinvolgono la configurazione personalizzata, verrà visualizzato un codice di errore CustomSetupScriptFailure con codice secondario come CustomSetupScriptBlobContainerInaccessible o CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Questo errore indica che il runtime di integrazione SSIS non può accedere al contenitore BLOB di Azure per l'installazione personalizzata. Verificare che l'URI SAS del contenitore sia raggiungibile e non sia scaduto.

Arrestare il runtime di integrazione se è in esecuzione, riconfigurare il runtime di integrazione con nuovo URI della firma di accesso condiviso del contenitore di installazione personalizzata, quindi riavviare il runtime di integrazione.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Questo errore indica che il runtime di integrazione SSIS non è in grado di trovare uno script di installazione personalizzato (Main. cmd) nel contenitore BLOB. Verificare che nel contenitore sia presente Main. cmd, che rappresenta il punto di ingresso per l'installazione personalizzata del programma di installazione.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Questo errore indica che l'esecuzione dello script di installazione personalizzato (Main. cmd) non è riuscita. Provare a eseguire prima lo script nel computer locale oppure controllare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Questo errore indica un timeout di esecuzione dello script di installazione personalizzata. Verificare che il contenitore BLOB contenga solo i file di installazione personalizzati necessari. È inoltre consigliabile controllare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB. Il periodo massimo per la configurazione personalizzata è di 45 minuti prima del timeout e il periodo massimo include il tempo per scaricare tutti i file dal contenitore e installarli in SSIS IR. Se è necessario un periodo più lungo, generare un ticket di supporto.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Questo errore indica che il tentativo di caricare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB non è riuscito. Questo problema si verifica perché il runtime di integrazione SSIS non dispone delle autorizzazioni di scrittura per il contenitore BLOB o a causa di problemi di archiviazione o di rete. Se l'installazione personalizzata ha esito positivo, questo errore non influirà su alcuna funzione SSIS, ma i log risultano mancanti. Se l'installazione personalizzata ha esito negativo con un altro errore e il log non viene caricato, questo errore verrà segnalato per primo, in modo che il log possa essere caricato per l'analisi. Inoltre, dopo la risoluzione di questo problema, si segnalano eventuali problemi più specifici. Se il problema non viene risolto dopo un nuovo tentativo, contattare il team di supporto di Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configurazione della rete virtuale

Quando si aggiunge il runtime di integrazione SSIS alla rete virtuale di Azure, il runtime di integrazione SSIS usa la rete virtuale sotto la sottoscrizione utente. Per altre informazioni, vedere [aggiungere un Integration Runtime Azure-SSIS a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Quando si verifica un problema relativo alla rete virtuale, viene visualizzato uno degli errori seguenti.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Questo errore può verificarsi per diversi motivi. Per risolvere il problemi, vedere le sezioni [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue)e [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) .

### <a name="forbidden"></a>Accesso negato

Questo tipo di errore potrebbe essere simile al seguente: "SubnetId non è abilitato per l'account corrente. Il provider di risorse Microsoft. batch non è registrato nella stessa sottoscrizione di VNet. "

Questi dettagli indicano che Azure Batch non può accedere alla rete virtuale. Registrare il provider di risorse Microsoft. batch nella stessa sottoscrizione della rete virtuale.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Questo tipo di errore potrebbe essere simile a uno dei seguenti: 

- "Il VNet specificato non esiste o il servizio batch non dispone dell'accesso."
- "La subnet specificata xxx non esiste".

Questi errori indicano che la rete virtuale non esiste, che il servizio Azure Batch non è in grado di accedervi o che la subnet specificata non esiste. Verificare che la rete virtuale e la subnet esistano e che Azure Batch possano accedervi.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Questo tipo di messaggio di errore può essere simile al seguente: "Impossibile eseguire il provisioning Integration Runtime in VNet. Se sono configurate le impostazioni server DNS o NSG, verificare che il server DNS sia accessibile e che NSG sia configurato correttamente.

In questa situazione è probabilmente disponibile una configurazione personalizzata delle impostazioni del server DNS o del NSG, che impedisce la risoluzione o l'accesso al nome del server di Azure richiesto dal runtime di integrazione SSIS. Per altre informazioni, vedere [configurazione della rete virtuale IR SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se i problemi persistono, contattare il team di supporto di Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Il runtime di integrazione SSIS verrà automaticamente aggiornato a intervalli regolari. Viene creato un nuovo pool di Azure Batch durante l'aggiornamento e il pool di Azure Batch precedente viene eliminato. Inoltre, le risorse correlate alla rete virtuale per il pool precedente vengono eliminate e le nuove risorse correlate alla rete virtuale vengono create nella sottoscrizione. Questo errore indica che l'eliminazione delle risorse correlate alla rete virtuale per il pool precedente non è riuscita a causa di un blocco di eliminazione a livello di sottoscrizione o di gruppo di risorse. Poiché il cliente controlla e imposta il blocco Delete, è necessario rimuovere il blocco Delete in questa situazione.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Se il provisioning del runtime di integrazione SSIS ha esito negativo, vengono eliminate tutte le risorse create. Tuttavia, se è presente un blocco di eliminazione delle risorse a livello di sottoscrizione o di gruppo di risorse, le risorse di rete virtuale non vengono eliminate come previsto. Per correggere l'errore, rimuovere il blocco di eliminazione e riavviare il runtime di integrazione.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Quando si arresta il runtime di integrazione SSIS, vengono eliminate tutte le risorse correlate alla rete virtuale. L'eliminazione può tuttavia avere esito negativo se è presente un blocco di eliminazione delle risorse a livello di sottoscrizione o di gruppo di risorse. Qui, anche il cliente controlla e imposta il blocco Delete. Pertanto, devono rimuovere il blocco di eliminazione e quindi arrestare di nuovo il runtime di integrazione SSIS.

### <a name="nodeunavailable"></a>NodeUnavailable

Questo errore si verifica quando viene eseguito il runtime di integrazione e significa che il runtime di integrazione è diventato non integro. Questo errore è sempre causato da una modifica nel server DNS o nella configurazione NSG che impedisce la connessione del runtime di integrazione SSIS al servizio necessario. Poiché la configurazione del server DNS e di NSG è controllata dal cliente, il cliente deve risolvere i problemi di blocco alla fine. Per altre informazioni, vedere [configurazione della rete virtuale IR SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se i problemi persistono, contattare il team di supporto di Azure Data Factory.
