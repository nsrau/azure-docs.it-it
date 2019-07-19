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
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253028"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Risolvere i problemi di gestione di SSIS Integration Runtime in Azure Data Factory

Questo documento fornisce guide per la risoluzione dei problemi di gestione di SSIS Integration Runtime (IR SSIS).

## <a name="overview"></a>Panoramica

Verrà visualizzato un messaggio di errore nel portale di ADF o restituito dal cmdlet di PowerShell se si verifica un problema durante il provisioning o il deprovisioning del runtime di integrazione SSIS. L'errore è sempre nel formato come codice di errore con un messaggio di errore dettagliato.

Ciò significa che il servizio presenta alcuni problemi temporanei se il codice di errore è InternalServerError. È possibile ripetere l'operazione in un secondo momento. Se il nuovo tentativo non è utile, contattare il team di supporto di Azure Data Factory.

Esistono tre dipendenze esterne principali che possono causare errori: Server di database SQL di Azure o Istanza gestita, script di installazione personalizzato e configurazione di rete virtuale se il codice di errore non è InternalServerError.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemi del server o dell'istanza gestita di database SQL di Azure

È necessario un server di database SQL di Azure o Istanza gestita se si esegue il provisioning del runtime di integrazione SSIS con il database del catalogo SSIS. Il server di database SQL di Azure o il Istanza gestita deve essere accessibile dal runtime di integrazione SSIS. L'account del server di database SQL di Azure o di Istanza gestita deve disporre dell'autorizzazione per la creazione del database del catalogo SSIS (SSISDB). Se si verifica un errore, nel portale di ADF verrà visualizzato un codice di errore con il messaggio di eccezione dettagli SQL. Attenersi alla procedura seguente per risolvere i problemi relativi ai codici di errore.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Questo problema può essere visualizzato quando si effettua il provisioning di un nuovo runtime di integrazione SSIS o durante l'esecuzione di un runtime di integrazione.

Questo problema può essere causato dai motivi seguenti se viene visualizzato l'errore durante il provisioning del runtime di integrazione, ed è possibile ottenere i dettagli del messaggio SqlException nel messaggio di errore.

* Problema di connessione di rete. Verificare che il SQL Server o Istanza gestita nome host sia accessibile e che non siano presenti firewall o NSG blocca il runtime di integrazione SSIS per accedere al server.
* L'accesso non è riuscito e viene utilizzata l'autenticazione SQL. Ciò significa che l'account fornito non può accedere al SQL Server. Verificare che sia stato specificato l'account utente corretto.
* L'accesso non è riuscito e viene usata l'autenticazione AAD (identità gestita). Aggiungere l'identità gestita della factory in un gruppo AAD e fare in modo che l'identità gestita disponga delle autorizzazioni di accesso al server di database del catalogo.
* Il timeout della connessione è sempre dovuto alla configurazione relativa alla sicurezza. È consigliabile creare una nuova macchina virtuale, fare in modo che la macchina virtuale si unisca allo stesso VNet di IR se il runtime di integrazione si trova in un VNet, quindi installare SSMS e verificare lo stato del server di database SQL di Azure o del Istanza gestita.

Per altri problemi, fare riferimento al messaggio di errore relativo all'eccezione SQL dettagliata e risolvere il problema visualizzato nel messaggio di errore. Se si verificano ancora problemi, contattare il server di database SQL di Azure o il team di supporto di Istanza gestita.

È probabile che vengano apportate modifiche al gruppo di sicurezza di rete o al firewall se viene visualizzato l'errore durante l'esecuzione del runtime di integrazione, causando il mancato accesso al server di database SQL di Azure o al Istanza gestita più. Sbloccare il nodo del ruolo di lavoro IR SSIS per accedere al server di database SQL di Azure o a Istanza gestita.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Il messaggio di errore è simile a "il database ' SSISDB ' ha raggiunto la quota di dimensioni. Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni ". Le soluzioni possibili sono:
* Aumentare la quota delle dimensioni del database SSISDB.
* Modificare le configurazioni di SSISDB per ridurre le dimensioni, ad esempio:
   * Riduzione del periodo di memorizzazione e del numero di versioni del progetto.
   * Riduzione del periodo di memorizzazione del log.
   * Modifica del livello predefinito del log e così via.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Questo errore indica che il server di database SQL di Azure o Istanza gestita dispone già di un SSISDB creato e usato da un altro IR. È necessario fornire un server di database SQL di Azure diverso o Istanza gestita oppure eliminare il database SSISDB esistente e riavviare il nuovo runtime di integrazione.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Questo errore può essere causato dai motivi seguenti:

* L'account utente configurato per il runtime di integrazione SSIS non dispone delle autorizzazioni necessarie per creare il database. È possibile concedere all'utente l'autorizzazione per la creazione del database.
* Creare un timeout del database come il timeout di esecuzione, il timeout dell'operazione DB e così via. È possibile riprovare in seguito per verificare se il problema è stato risolto. Se il nuovo tentativo non funziona, contattare il server di database SQL di Azure o il team di supporto di Istanza gestita.

Per altri problemi, controllare il messaggio di errore eccezione SQL e risolvere il problema indicato nel messaggio di errore. Se i problemi persistono, contattare il server di database SQL di Azure o il team di supporto di Istanza gestita.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Il messaggio di errore è simile a "nome oggetto non valido ' Catalog. catalog_properties '.", significa che si dispone già di un database denominato SSISDB ma che non è stato creato dal runtime di integrazione SSIS oppure che lo stato del database non è valido causato da errori nell'ultimo provisioning del runtime di integrazione SSIS. È possibile eliminare il database esistente con il nome SSISDB oppure configurare un nuovo server di database SQL di Azure o Istanza gestita per il runtime di integrazione.

## <a name="custom-setup"></a>Installazione personalizzata

Il programma di installazione personalizzato fornisce un'interfaccia per aggiungere i propri passaggi di installazione durante il provisioning o la riconfigurazione del runtime di integrazione SSIS. Per altre informazioni, vedere [personalizzare il programma di installazione per il runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Verificare che il contenitore includa solo i file di installazione personalizzati necessari, poiché tutti i file nel contenitore verranno scaricati nel nodo del ruolo di lavoro IR SSIS. È consigliabile testare lo script di installazione personalizzato in un computer locale per correggere eventuali problemi di esecuzione dello script prima di eseguire lo script nel runtime di integrazione SSIS.

Il contenitore dello script di installazione personalizzato verrà verificato durante l'esecuzione del runtime di integrazione. il runtime di integrazione SSIS è regolarmente aggiornato, per cui è necessario accedere di nuovo al contenitore per scaricare lo script di installazione personalizzato e installarlo di nuovo. Il controllo includerà se il contenitore è accessibile e se il file Main. cmd esiste.

Qualsiasi errore con l'installazione personalizzata verrà visualizzato un errore con il codice CustomSetupScriptFailure, si verificherà il messaggio di errore con codice di errore secondario.  Attenersi alla procedura seguente per risolvere i problemi relativi ai codici di errore secondari.  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Significa che il runtime di integrazione SSIS non può accedere al contenitore BLOB di Azure per l'installazione personalizzata. Verificare che l'URI SAS del contenitore sia raggiungibile e non scaduto.

Arrestare prima il runtime di integrazione se il runtime di integrazione è in stato di esecuzione, riconfigurare il runtime di integrazione con il nuovo URI della firma di accesso condiviso del contenitore di installazione personalizzata e riavviarlo.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Significa che SSIS IR non è in grado di trovare lo script di installazione personalizzato (Main. cmd) nel contenitore BLOB. Verificare che Main. cmd esista nel contenitore, che rappresenta il punto di ingresso per l'installazione personalizzata del programma di installazione.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Questo significa che l'esecuzione dello script di installazione personalizzato (Main. cmd) non è riuscita, è possibile provare lo script nel computer locale prima o controllare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Significa eseguire il timeout dello script di installazione personalizzato. Verificare che il contenitore BLOB contenga solo i file di installazione personalizzati necessari. È anche possibile controllare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB. Il periodo massimo di installazione personalizzata è impostato su 45 minuti prima del timeout e il periodo massimo include il tempo per scaricare tutti i file dal contenitore e installarli nel runtime di integrazione SSIS. Se è necessario un periodo più lungo, generare un ticket di supporto.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Significa che il caricamento dei log di esecuzione dell'installazione personalizzata nel contenitore BLOB non è riuscito, perché il runtime di integrazione SSIS non dispone dell'autorizzazione di scrittura per il contenitore BLOB o di alcuni problemi di archiviazione o di rete. Se l'installazione personalizzata ha esito positivo, questo errore non ha alcun effetto sulle funzioni SSIS, ma i log risultano mancanti. Se l'installazione personalizzata non è riuscita con altro errore e non è stato possibile caricare il log, questo errore verrà segnalato per primo, in modo che il log possa essere caricato per l'analisi e dopo la risoluzione del problema verrà segnalato un problema più specifico. Se il problema non viene risolto dopo un nuovo tentativo, contattare il team di supporto di Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configurazione della rete virtuale

Quando si aggiunge un runtime di integrazione SSIS a una rete virtuale (VNet), viene usato il VNet nella sottoscrizione utente. Per altre informazioni, vedere [aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Quando si verifica un problema correlato a VNet, verrà visualizzato un errore come riportato di seguito

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Potrebbe essere causato da motivi Variant. Attenersi alla procedura seguente per risolvere i problemi relativi ai codici di errore secondari.

### <a name="forbidden"></a>Accesso negato

Il messaggio di errore è simile a "subnetId non è abilitato per l'account corrente. Il provider di risorse Microsoft. batch non è registrato nella stessa sottoscrizione di VNet. "

Significa Azure Batch non è possibile accedere al VNet. Registrare il provider di risorse Microsoft. batch nella stessa sottoscrizione di VNet.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Il messaggio di errore è simile a "il VNet specificato non esiste o il servizio batch non dispone dell'accesso" o "la subnet specificata xxx non esiste".

Significa che VNet non esiste o Azure Batch servizio non è in grado di accedervi oppure che la subnet specificata non esiste. Verificare che il VNet e la subnet esistano e Azure Batch possano accedervi.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Il messaggio è simile a "Impossibile eseguire il provisioning Integration Runtime in vnet. Se sono configurate le impostazioni server DNS o NSG, verificare che il server DNS sia accessibile e che NSG sia configurato correttamente "

È probabile che si disponga di una configurazione personalizzata delle impostazioni del server DNS o del NSG, che causano il nome del server di Azure richiesto dal runtime di integrazione SSIS non può essere risolto o non è possibile accedervi. Per altre informazioni, vedere il documento di [configurazione di VNET IR SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) . Se i problemi persistono, contattare il team di supporto di Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Il runtime di integrazione SSIS verrà automaticamente aggiornato a intervalli regolari e verrà creato un nuovo pool di Azure Batch durante l'aggiornamento e il pool di Azure Batch precedente verrà eliminato, la risorsa correlata di VNet per il pool precedente verrà eliminata e verrà creata una nuova risorsa correlata VNet nel abbonamento. Questo errore indica che l'eliminazione della risorsa correlata a VNet per il pool precedente non è riuscita a causa del blocco di eliminazione a livello di sottoscrizione o di gruppo Consente di rimuovere il blocco Delete.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Il provisioning del runtime di integrazione SSIS potrebbe avere esito negativo a causa di un tipo di motivo e, in caso di errore, verranno eliminate tutte le risorse create. Tuttavia, le risorse VNet non sono state eliminate a causa di un blocco di eliminazione delle risorse a livello di sottoscrizione o di gruppo di risorse. Rimuovere il blocco di eliminazione e riavviare il runtime di integrazione.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Quando si arresta il runtime di integrazione SSIS, tutte le risorse correlate a VNet verranno eliminate, ma l'eliminazione non riesce a causa di un blocco di eliminazione delle risorse a livello di sottoscrizione o di gruppo di risorse. Consente di rimuovere il blocco di eliminazione e riprovare.

### <a name="nodeunavailable"></a>NodeUnavailable

Questo errore si verifica durante l'esecuzione del runtime di integrazione, significa che il runtime di integrazione è stato prima e diventa non integro, ma è sempre dovuto alla modifica della configurazione del server DNS o del NSG e il runtime di integrazione SSIS non è in grado di connettersi al servizio dipendente, aiutare a risolvere i problemi di configurazione del server DNS o NSG Per altre informazioni, vedere [configurazione di VNET IR SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se i problemi persistono, contattare il team di supporto di Azure Data Factory.
