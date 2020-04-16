---
title: Risolvere i problemi di gestione di SSIS Integration RuntimeTroubleshoot SSIS Integration Runtime management
description: In questo articolo vengono fornite indicazioni per la risoluzione dei problemi di gestione di SSIS Integration Runtime (SSIS IR)
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 0324044d93f12f6ac6ec96ff1a31be8ee02ada41
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414705"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Risolvere i problemi relativi alla gestione del runtime di integrazione SSIS in Azure Data FactoryTroubleshoot SSIS Integration Runtime management in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo fornisce indicazioni per la risoluzione dei problemi di gestione in Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), noto anche come Runtime di integrazione SSIS.

## <a name="overview"></a>Panoramica

Se si verifica un problema durante il provisioning o il deprovisioning del runtime di messaggistica di accesso SSIS, verrà visualizzato un messaggio di errore nel portale di Microsoft Azure Data Factory o un errore restituito da un cmdlet di PowerShell. L'errore viene sempre visualizzato nel formato di un codice di errore con un messaggio di errore dettagliato.

Se il codice di errore è InternalServerError, il servizio presenta problemi temporanei ed è necessario ritentare l'operazione in un secondo momento. Se un nuovo tentativo non risolve il supporto, contattare il team di supporto di Azure Data Factory.If a retry doesn't help, contact the Azure Data Factory support team.

In caso contrario, tre dipendenze esterne principali possono causare errori: un server di database SQL di Azure o un'istanza gestita, uno script di installazione personalizzato e una configurazione di rete virtuale.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemi relativi al server di database SQL di Azure o all'istanza gestitaAzure SQL Database server or managed instance issues

Se si effettua il provisioning di SSIS IR con un database del catalogo SSIS, è necessario avere un server di database SQL di Azure o un'istanza gestita. SSIS IR deve essere in grado di accedere al server di database SQL di Azure o all'istanza gestita. Inoltre, l'account del server di database SQL di Azure o dell'istanza gestita deve disporre delle autorizzazioni per creare un database del catalogo SSIS (SSISDB). Se si verifica un errore, nel portale di Data Factory verrà visualizzato un codice errore con un messaggio di eccezione SQL dettagliato. Usare le informazioni nell'elenco seguente per risolvere i problemi relativi ai codici di errore.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Questo problema può essere visualizzato quando si effettua il provisioning di un nuovo SSIS IR o quando il runtime di integrazione è in esecuzione. Se l'errore si verifica durante il provisioning del runtime di integrazione, è possibile ricevere un messaggio SqlException dettagliato nel messaggio di errore, a indicare uno dei problemi seguenti:

* Un problema di connessione di rete. Controllare se il nome host di SQL Server o dell'istanza gestita è accessibile. Verificare inoltre che nessun firewall o gruppo di sicurezza di rete (NSG) blocchi l'accesso di SSIS IR al server.
* Errore di accesso durante l'autenticazione a SQL. L'account specificato non può accedere al database di SQL Server. Assicurarsi di specificare l'account utente corretto.
* Errore di accesso durante l'autenticazione a Microsoft Azure Active Directory (Azure AD) (identità gestita). Aggiungere l'identità gestita della factory a un gruppo AAD e verificare che tale identità gestita disponga delle autorizzazioni di accesso al server di database del catalogo.
* Timeout della connessione. Questo errore è sempre causato da una configurazione relativa alla sicurezza. È consigliabile:
  1. Creare una nuova macchina virtuale.
  1. Aggiungere la macchina virtuale alla stessa rete virtuale di risorse di accesso a irribtime di Microsoft Azure se il pacchetto di disponibilità di accesso e supporto utente si trova in una rete virtuale.
  1. Installare SSMS e controllare il server di database SQL di Azure o lo stato dell'istanza gestita.

Per altri problemi, risolvere l'errore visualizzato nel messaggio di errore di eccezione SQL dettagliato. Se i problemi persistono, contattare il team di supporto del server di database SQL di Azure o dell'istanza gestita.

Se l'errore viene visualizzato durante l'esecuzione del runtime di integrazione, è probabile che le modifiche al gruppo di sicurezza di rete o al firewall impediscano al nodo di lavoro di SSIS IR di accedere al server di database SQL di Azure o all'istanza gestita. Sbloccare il nodo di lavoro di SSIS IR in modo che possa accedere al server di database SQL di Azure o all'istanza gestita.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Ecco come potrebbe apparire questo tipo di messaggio di errore: "Il database 'SSISDB' ha raggiunto la quota di dimensioni. Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni." 

Le possibili soluzioni sono:
* Aumentare la dimensione della quota di SSISDB.
* Modificare la configurazione di SSISDB per ridurre le dimensioni:
   * Riduzione del periodo di conservazione e del numero di versioni del progetto.
   * Riduzione del periodo di conservazione del registro.
   * Modifica del livello predefinito del registro.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Questo errore indica che il server di database SQL di Azure o l'istanza gestita dispone già di un SSISDB, che è usato da un altro runtime di integrazione. È necessario fornire un server di database SQL di Azure o un'istanza gestita diversa oppure eliminare il database il SSISDB esistente e riavviare il nuovo runtime di integrazione.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Questo errore può verificarsi per uno dei motivi seguenti:

* L'account utente configurato per SSIS IR non dispone dell'autorizzazione per creare il database. È possibile concedere all'utente l'autorizzazione per creare il database.
* Si verifica un timeout durante la creazione del database, ad esempio un timeout nell'esecuzione o nel funzionamento del database. Ripetere l'operazione in un secondo momento. Se il problema non viene risolto nemmeno con il nuovo tentativo, contattare il team di supporto del server di database SQL di Azure o dell'istanza gestita.

Per altri problemi, controllare il messaggio di errore di eccezione SQL e risolvere il problema indicato nei dettagli dell'errore. Se i problemi persistono, contattare il team di supporto del server di database SQL di Azure o dell'istanza gestita.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Questo tipo di messaggio di errore è simile al seguente: "Nome oggetto non valido 'catalogo.catalog_properties'". In questo caso, si dispone già di un database denominato SSISDB ma non è stato creato dal runtime di irrigazione SSIS oppure il database è in uno stato non valido causato da errori nell'ultimo provisioning IR SSIS. È possibile eliminare il database esistente con il nome SSISDB oppure configurare un nuovo server di database SQL di Azure o una nuova istanza gestita per il runtime di integrazione.

## <a name="custom-setup-issues"></a>Problemi di configurazione personalizzati

L'installazione personalizzata offre un'interfaccia per l'aggiunta di passaggi di installazione durante il provisioning o la riconfigurazione di SSIS IR. Per altre informazioni, vedere [Personalizzare l'installazione per Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Assicurarsi che il contenitore includa solo i file di installazione personalizzata necessari; tutti gli altri file nel contenitore verranno scaricati nel nodo di lavoro di SSIS IR. È consigliabile testare lo script di installazione personalizzata in un computer locale per correggere eventuali errori di esecuzione prima di eseguire lo script in SSIS IR.

Il contenitore dello script di installazione personalizzata verrà controllato mentre è in esecuzione il runtime di integrazione, perché SSIS IR viene aggiornato regolarmente. Questo aggiornamento richiede l'accesso al contenitore per scaricare lo script di installazione personalizzata e installarlo di nuovo. Il processo controlla anche se il contenitore è accessibile e se il file main.cmd esiste.

Per qualsiasi errore che prevede l'installazione personalizzata, verrà visualizzato un codice di errore CustomSetupScriptFailure con codice secondario come CustomSetupScriptBlobContainerInaccessible o CustomSetupScriptNotFound.For any error that involves custom setup, you'll see a CustomSetupScriptFailure error code with sub code like CustomSetupScriptBlobContainerInaccessible or CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Questo errore indica che SSIS IR non può accedere al contenitore BLOB di Azure per l'installazione personalizzata. Assicurarsi che l'URI SAS del contenitore sia raggiungibile e non sia scaduto.

Se è in esecuzione, arrestare il runtime di esecuzione, riconfigurarlo con il nuovo URI SAS del contenitore dell'installazione personalizzata, quindi riavviare il runtime di esecuzione.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Questo errore indica che SSIS IR non è in grado di trovare uno script di installazione personalizzata (main.cmd) nel contenitore BLOB. Verificare il punto fondamentale per l'installazione personalizzata, cioè che main.cmd esista nel contenitore.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Questo errore indica che l'esecuzione dello script di installazione personalizzata (main.cmd) ha avuto esito negativo. Provare a eseguire prima lo script nel computer locale oppure controllare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Questo errore indica un timeout di esecuzione dello script di installazione personalizzata. Assicurarsi che lo script possa essere eseguito in modo invisibile all'utente e che non siano necessari input interattivi e assicurarsi anche che il contenitore BLOB contenga solo i file di installazione personalizzati necessari. È consigliabile testare prima lo script sul computer locale. È inoltre necessario controllare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB. Il periodo massimo per l'installazione personalizzata è di 45 minuti prima del timeout, mentre il periodo massimo include il tempo per scaricare tutti i file dal contenitore e installarli in SSIS IR. Se è necessario un periodo più lungo, generare un ticket di supporto.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Questo errore indica che il tentativo di caricare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB ha avuto esito negativo. Questo problema si verifica perché SSIS IR non dispone delle autorizzazioni di scrittura per il contenitore BLOB o a causa di problemi di archiviazione o di rete. Se l'installazione personalizzata ha esito positivo, l'errore non influirà su alcuna funzione di SSIS, ma i log saranno mancanti. Se l'installazione personalizzata ha esito negativo con un altro errore e il log non viene caricato, l'errore verrà segnalato per primo, in modo che il log possa essere caricato per l'analisi. Inoltre, dopo la risoluzione del problema, verranno segnalati eventuali problemi più specifici. Se il problema non viene risolto dopo un nuovo tentativo, contattare il team di supporto di Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configurazione della rete virtuale

Quando viene aggiunto alla rete virtuale di Azure, SSIS IR usa la rete virtuale prevista dalla sottoscrizione dell'utente. Per altre informazioni, vedere [Aggiungere Azure-SSIS Integration Runtime a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Quando si verifica un problema relativo alla rete virtuale, viene visualizzato uno degli errori seguenti.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Questo errore può verificarsi per vari motivi. Per risolverlo, vedere le sezioni [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) e [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Accesso negato

Questo tipo di errore potrebbe essere simile al seguente: "SubnetId non è abilitato per l'account corrente. Il provider di risorse Microsoft.Batch non è registrato nella stessa sottoscrizione della rete virtuale."

Questi dettagli indicano che Azure Batch non può accedere alla rete virtuale. Registrare il provider di risorse Microsoft.Batch nella stessa sottoscrizione della rete virtuale.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Questo tipo di errore potrebbe essere simile a uno dei seguenti: 

- "La rete virtuale specificata non esiste oppure il servizio Batch non ha accesso ad essa."
- "La subnet specificata xxx non esiste."

Questi errori indicano che la rete virtuale non esiste, che il servizio Azure Batch non può accedervi o che la subnet specificata non esiste. Verificare che la rete virtuale e la subnet esistano e che Azure Batch possa accedervi.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Questo tipo di messaggio di errore può essere simile al seguente: "Impossibile eseguire il provisioning di Integration Runtime nella rete virtuale. Se sono configurate le impostazioni del server DNS o del gruppo di sicurezza di rete, assicurarsi che il server DNS sia accessibile e che il gruppo di sicurezza di rete sia configurato correttamente."

In questa situazione è probabilmente presente una configurazione personalizzata delle impostazioni del server DNS o del gruppo di sicurezza di rete, che impedisce la risoluzione o l'accesso al nome del server di Azure richiesto da SSIS IR. Per altre informazioni, vedere [Configurazione della rete virtuale di SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se i problemi persistono, contattare il team di supporto di Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR verrà aggiornato automaticamente a intervalli regolari. Durante l'aggiornamento viene creato un nuovo pool di Azure Batch e viene eliminato quello precedente. Inoltre, le risorse correlate alla rete virtuale per il pool precedente vengono eliminate e nella sottoscrizione vengono create nuove risorse correlate alla rete virtuale. Questo errore indica che l'eliminazione delle risorse correlate alla rete virtuale per il pool precedente ha avuto esito negativo a causa di un blocco di eliminazione a livello di sottoscrizione o di gruppo di risorse. Poiché il cliente controlla e imposta il blocco di eliminazione, in questo caso è necessario rimuoverlo.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Se il provisioning di SSIS IR ha esito negativo, vengono eliminate tutte le risorse che sono state create. Tuttavia, se è presente un blocco di eliminazione delle risorse a livello di sottoscrizione o di gruppo di risorse, le risorse di rete virtuale non vengono eliminate come previsto. Per correggere l'errore, rimuovere il blocco di eliminazione e riavviare il runtime di integrazione.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Quando viene arrestato SSIS IR, vengono eliminate tutte le risorse correlate alla rete virtuale. L'eliminazione può tuttavia avere esito negativo se è presente un blocco di eliminazione delle risorse a livello di sottoscrizione o di gruppo di risorse. Anche in questo caso il cliente controlla e imposta il blocco di eliminazione. Di conseguenza, deve rimuoverlo e arrestare di nuovo SSIS IR.

### <a name="nodeunavailable"></a>NodeUnavailable

Questo errore si verifica quando il runtime di integrazione è in esecuzione; significa che il runtime di integrazione è diventato non integro. Questo errore è sempre causato da una modifica nella configurazione del server DNS o del gruppo di sicurezza di rete che impedisce a SSIS IR di connettersi al servizio necessario. Poiché la configurazione del server DNS e del gruppo di sicurezza di rete è controllata dal cliente, il cliente deve risolvere i problemi di blocco da parte sua. Per altre informazioni, vedere [Configurazione della rete virtuale di SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se i problemi persistono, contattare il team di supporto di Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Configurazione degli indirizzi IP pubblici statici

Quando si aggiunge il componente di accesso Azure-SSIS alla rete virtuale di Azure, è anche possibile portare i propri indirizzi IP pubblici statici per il flusso di accesso virtuale in modo che il componente di accesso client può accedere alle origini dati che limitano l'accesso a indirizzi IP specifici. Per altre informazioni, vedere [Aggiungere Azure-SSIS Integration Runtime a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Oltre ai problemi di rete virtuale di cui sopra, è anche possibile incontrare problemi statici relativi agli indirizzi IP pubblici. Si prega di controllare i seguenti errori per aiuto.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecifiedInvalidPublicIPSpecified

Questo errore può verificarsi per diversi motivi quando si avvia il runtime di gestione azure-SSIS:This error can occur for a variety reasons when you start the Azure-SSIS IR:

| Messaggio di errore | Soluzione|
|:--- |:--- |
| L'indirizzo IP pubblico statico fornito è già usato, fornire due inutilizzati per il runtime di integrazione Azure-SSIS. | È necessario selezionare due indirizzi IP pubblici statici inutilizzati o rimuovere i riferimenti correnti all'indirizzo IP pubblico specificato e quindi riavviare il componente di accesso Azure-SSIS. |
| L'indirizzo IP pubblico statico fornito non ha un nome DNS, fornire due di essi con nome DNS per il runtime di integrazione Azure-SSIS. | È possibile configurare il nome DNS dell'indirizzo IP pubblico nel portale di Azure, come illustrato nell'immagine seguente. I passaggi specifici sono i seguenti: (1) Aprire il portale di Azure e passare alla pagina delle risorse di questo indirizzo IP pubblico; (2) Seleziona la sezione **Configurazione** e imposta il nome DNS, quindi fai clic su **Salva** pulsante; (3) Riavviare il rinisso Azure-SSIS. |
| La rete virtuale fornita e gli indirizzi IP pubblici statici per il runtime di integrazione Azure-SSIS devono trovarsi nella stessa posizione. | In base ai requisiti di Rete di Azure, l'indirizzo IP pubblico statico e la rete virtuale devono trovarsi nella stessa posizione e sottoscrizione. Fornire due indirizzi IP pubblici statici validi e riavviare il componente di accesso Azure-SSIS. |
| L'indirizzo IP pubblico statico fornito è di base, fornire due standard per il runtime di integrazione Azure-SSIS.The provided static public IP address is a basic one, please provide two standard one for your Azure-SSIS Integration Runtime. | Fare riferimento a [SKU dell'indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) per assistenza. |

![Runtime di integrazione Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Se il provisioning del sistema di oggetti di messaggizione a oggetti Azure-SSIS ha esito negativo, vengono eliminate tutte le risorse create. Tuttavia, se è presente un blocco di eliminazione delle risorse a livello di sottoscrizione o di risorse (che contiene l'indirizzo IP pubblico statico), le risorse di rete non vengono eliminate come previsto. Per correggere l'errore, rimuovere il blocco di eliminazione e riavviare il sistema di gestione delle informazioni.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Quando si arresta il controllo di accesso Azure-SSIS, tutte le risorse di rete create nel gruppo di risorse contenente l'indirizzo IP pubblico verranno eliminate. Tuttavia, l'eliminazione può non riuscire se è presente un blocco di eliminazione delle risorse a livello di sottoscrizione o di risorse (che contiene l'indirizzo IP pubblico statico). Rimuovere il blocco di eliminazione e riavviare il sistema iR.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Il componente di ri-azione Azure-SSIS viene aggiornato automaticamente a intervalli regolari. I nuovi nodi a irlavoro vengono creati durante l'aggiornamento e i nodi precedenti verranno eliminati. Inoltre, le risorse di rete create (ad esempio, il servizio di bilanciamento del carico e il gruppo di sicurezza di rete) per i nodi precedenti vengono eliminate e le nuove risorse di rete vengono create nella sottoscrizione. Questo errore indica che l'eliminazione delle risorse di rete per i nodi precedenti non è riuscita a causa di un blocco di eliminazione a livello di sottoscrizione o di risorse (che contiene l'indirizzo IP pubblico statico). Rimuovere il blocco di eliminazione in modo da poter pulire i vecchi nodi e rilasciare l'indirizzo IP pubblico statico per i vecchi nodi. In caso contrario, l'indirizzo IP pubblico statico non può essere rilasciato e non saremo in grado di aggiornare ulteriormente il tuo iR.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Quando si desidera portare i propri indirizzi IP pubblici statici, devono essere forniti due indirizzi IP pubblici. Uno di essi verrà utilizzato per creare immediatamente i nodi IR e un altro verrà utilizzato durante l'aggiornamento del ir. Questo errore può verificarsi quando l'altro indirizzo IP pubblico è inutilizzabile durante l'aggiornamento. Fare riferimento a [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) per le possibili cause.