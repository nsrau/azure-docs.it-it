---
title: Connettore PowerShell | Documentazione Microsoft
description: Questo articolo descrive come configurare il connettore Windows PowerShell di Microsoft.
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 6dba8e34-a874-4ff0-90bc-bd2b0a4199b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 27ca89a2032c82a8be909349b38a64fc6aa9579e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="windows-powershell-connector-technical-reference"></a>Documentazione tecnica sul connettore Windows PowerShell
Questo articolo descrive il connettore Windows PowerShell ed è applicabile ai prodotti seguenti:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * È necessario usare l'hotfix 4.1.3671.0 o versione successiva ( [KB3092178](https://support.microsoft.com/kb/3092178)).

Per MIM2016 e FIM2010R2, il connettore è disponibile come download dall' [Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Panoramica del connettore PowerShell
Il connettore PowerShell consente di integrare il servizio di sincronizzazione con sistemi esterni che offrono API basate su Windows PowerShell. Il connettore fornisce un collegamento tra le funzionalità del framework ECMA2 (Extensible Connectivity 2 Management Agent) e Windows PowerShell. Per altre informazioni sul framework ECMA, vedere le [informazioni di riferimento su Extensible Connectivity 2.2 Management Agent](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Prerequisiti
Prima di usare il connettore, verificare che nel server di sincronizzazione sia disponibile quanto segue:

* Microsoft .NET 4.5.2 Framework o versione successiva
* Windows PowerShell 2.0, 3.0 o 4.0

I criteri di esecuzione nel server del servizio di sincronizzazione devono essere configurati per consentire al connettore di eseguire script di Windows PowerShell. Se gli script eseguiti dal connettore non hanno una firma digitale, eseguire questo comando per configurare i criteri di esecuzione:   
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Creare un nuovo connettore
Per creare un connettore Windows PowerShell nel servizio di sincronizzazione è necessario fornire una serie di script Windows PowerShell che eseguono i passaggi richiesti dal servizio di sincronizzazione. Gli script da implementare variano a seconda dell'origine dati a cui ci si connette e alla funzionalità richiesta. Questa sezione illustra ogni script che è possibile implementare e quando sono necessari.

Il connettore Windows PowerShell è progettato per archiviare ogni script all'interno del database del servizio di sincronizzazione. Anche se è possibile eseguire gli script archiviati nel file system, è più semplice inserire il corpo di ogni script direttamente nella configurazione del connettore.

Per creare un connettore PowerShell, in **Synchronization Service** selezionare **Management Agent** e quindi **Create**. Selezionare il connettore **PowerShell (Microsoft)** .

![Create Connector](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Connettività
Specificare i parametri di configurazione per la connessione a un sistema remoto. Questi valori verranno archiviati in modo sicuro dal servizio di sincronizzazione e resi disponibili per gli script Windows PowerShell quando viene eseguito il connettore.

![Connettività](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

È possibile configurare i parametri di connettività seguenti:

**Connettività**

| Parametro | Default Value | Scopo |
| --- | --- | --- |
| Server |<Blank> |Nome del server a cui deve connettersi il connettore. |
| Domain |<Blank> |Dominio delle credenziali da archiviare e usare quando viene eseguito il connettore. |
| Utente |<Blank> |Nome utente delle credenziali da archiviare e usare quando viene eseguito il connettore. |
| Password |<Blank> |Password delle credenziali da archiviare e usare quando viene eseguito il connettore. |
| Impersonate Connector Account |False |Se impostato su True, il servizio di sincronizzazione esegue gli script di Windows PowerShell nel contesto delle credenziali specificate. Se possibile, è consigliabile usare il parametro **$Credentials** passato a ogni script anziché la rappresentazione. Per altre informazioni sulle autorizzazioni aggiuntive necessarie per usare questa opzione, vedere la sezione [Configurazione aggiuntiva per la rappresentazione](#additional-configuration-for-impersonation). |
| Load User Profile When Impersonating |False |Indica a Windows di caricare il profilo utente delle credenziali del connettore durante la rappresentazione. Se l'utente rappresentato ha un profilo mobile, il connettore non carica il profilo mobile. Per altre informazioni sulle autorizzazioni aggiuntive necessarie per usare questo parametro, vedere la sezione [Configurazione aggiuntiva per la rappresentazione](#additional-configuration-for-impersonation). |
| Logon Type When Impersonating |Nessuna |Tipo di accesso durante la rappresentazione. Per altre informazioni, vedere la documentazione di [dwLogonType][dw]. |
| Signed Scripts Only |False |Se impostato su True, il connettore Windows PowerShell verifica che ogni script presenti una firma digitale valida. Se impostato su False, assicurarsi che i criteri di esecuzione di Windows PowerShell per il server del servizio di sincronizzazione siano impostati su RemoteSigned o su Unrestricted. |

**Modulo comune**  
Il connettore consente di archiviare un modulo di Windows PowerShell condiviso nella configurazione. Quando il connettore esegue uno script, il modulo di Windows PowerShell viene estratto nel file system perché possa essere importato da ogni script.

Per gli script di importazione, esportazione e sincronizzazione password il modulo comune viene estratto nella cartella MAData del connettore. Per gli script di schema, convalida, gerarchia e individuazione della partizione, il modulo comune viene estratto nella cartella %TEMP%. In entrambi i casi, lo script del modulo comune estratto viene denominato in base all'impostazione del nome script del modulo comune.

Per caricare un modulo denominato FIMPowerShellConnectorModule.psm1 dalla cartella MAData, usare l'istruzione seguente: `Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Per caricare un modulo denominato FIMPowerShellConnectorModule.psm1 dalla cartella %TEMP%, usare l'istruzione seguente: `Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Convalida dei parametri**  
Lo script di convalida è uno script Windows PowerShell facoltativo che può essere usato per verificare la validità dei parametri di configurazione del connettore specificati dall'amministratore. Questo script viene normalmente usato per convalidare le credenziali di connessione e del server, nonché i parametri di connessione. Lo script di convalida viene chiamato dopo la modifica delle schede e finestre di dialogo seguenti:

* Connettività
* Global Parameters
* Partition Configuration

Dal connettore, lo script di convalida riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameterPage |[ConfigParameterPage][cpp] |La scheda o la finestra di dialogo di configurazione che ha attivato la richiesta di convalida. |
| ConfigParameters |[KeyedCollection][keyk] [stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |

Lo script di convalida deve restituire un singolo oggetto ParameterValidationResult alla pipeline.

**Individuazione dello schema**  
Lo script di individuazione dello schema è obbligatorio. Questo script restituisce i tipi di oggetto, gli attributi e i vincoli di attributo usati dal servizio di sincronizzazione durante la configurazione delle regole del flusso di attributi. Lo script di individuazione dello schema viene eseguito durante la creazione del connettore e popola lo schema del connettore. Viene anche usato dall'azione di aggiornamento dello schema in Synchronization Service Manager.

Dal connettore, lo script di individuazione dello schema riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk] [stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |

Lo script deve restituire un singolo oggetto [Schema][schema] alla pipeline. L'oggetto Schema è costituito da oggetti [SchemaType][schemaT] che rappresentano i tipi di oggetto, ad esempio utenti e gruppi. L'oggetto SchemaType contiene una raccolta di oggetti [SchemaAttribute][schemaA] che rappresentano gli attributi, ad esempio nome, cognome e indirizzo postale del tipo.

**Altri parametri**  
Oltre alle impostazioni di configurazione standard è possibile definire impostazioni di configurazione personalizzate aggiuntive specifiche dell'istanza del connettore. Questi parametri possono essere specificati a livello di connettore, di partizione o di passaggio di esecuzione e sono accessibili dal relativo script di Windows PowerShell. Le impostazioni di configurazione personalizzate possono essere archiviate nel database del servizio di sincronizzazione in formato testo normale o crittografate. Il servizio di sincronizzazione crittografa e decrittografa automaticamente le impostazioni di configurazione protette quando è necessario.

Per specificare impostazioni di configurazione personalizzate, separare il nome di ogni parametro con una virgola (,).

Per accedere alle impostazioni di configurazione personalizzate da uno script, è necessario aggiungere come suffisso al nome un carattere di sottolineatura ( \_ ) e l'ambito del parametro (Global, Partition o RunStep). Ad esempio, per accedere al parametro Global FileName, usare il frammento di codice: `$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Funzionalità
La scheda Capabilities della finestra di progettazione dell'agente di gestione definisce il comportamento e le funzionalità del connettore. Dopo aver creato il connettore, non sarà più possibile modificare le selezioni effettuate in questa scheda. La tabella elenca le impostazioni delle funzionalità.

![Capabilities](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| Funzionalità | DESCRIZIONE |
| --- | --- |
| [Distinguished Name Style][dnstyle] (Stile di nome distinto) |Indica se il connettore supporta i nomi distinti e il relativo stile. |
| [Export Type][exportT] (Tipo di esportazione) |Determina il tipo degli oggetti che vengono presentati allo script di esportazione. <li>AttributeReplace: include il set completo di valori per un attributo multivalore quando l'attributo viene modificato.</li><li>AttributeUpdate: include solo i valori differenziali per un attributo multivalore quando l'attributo viene modificato.</li><li>MultivaluedReferenceAttributeUpdate: include un set completo di valori per attributi multivalore non di riferimento e solo i valori differenziali per gli attributi di riferimento con più valori.</li><li>ObjectReplace: include tutti gli attributi per un oggetto quando viene modificato qualsiasi attributo</li> |
| [Data Normalization][DataNorm] (Normalizzazione dei dati) |Indica al servizio di sincronizzazione di normalizzare gli attributi di ancoraggio prima che vengano forniti agli script. |
| [Object Confirmation][oconf] (Conferma degli oggetti) |Consente di configurare il comportamento dell'importazione in sospeso nel servizio di sincronizzazione. <li>Normale: il comportamento predefinito che prevede che tutte le modifiche esportate siano confermate tramite l'importazione</li><li>NoDeleteConfirmation: quando viene eliminato un oggetto, non viene generata alcuna importazione in sospeso.</li><li>NoAddAndDeleteConfirmation: quando viene creato o eliminato un oggetto, non viene generata alcuna importazione in sospeso.</li> |
| Use DN as Anchor |Se la funzionalità Distinguished Name Style è impostata su LDAP, l'attributo di ancoraggio per lo spazio del connettore è anche il nome distinto. |
| Concurrent Operations of Several Connectors |Se selezionata, è possibile eseguire contemporaneamente più connettori Windows PowerShell. |
| Partitions |Se selezionata, il connettore supporta più partizioni e l'individuazione delle partizioni. |
| Hierarchy |Se selezionata, il connettore supporta una struttura gerarchica di tipo LDAP. |
| Enable Import |Se selezionata, il connettore importa i dati attraverso gli script di importazione. |
| Enable Delta Import |Se selezionata, il connettore può richiedere i differenziali dagli script di importazione. |
| Enable Export |Se selezionata, il connettore esporta i dati attraverso gli script di esportazione. |
| Enable Full Export |Se selezionata, gli script di esportazione supportano l'esportazione dell'intero spazio del connettore. Per poter usare questa opzione deve essere selezionata anche l'opzione Enable Export. |
| No Reference Values In First Export Pass |Se selezionata, gli attributi di riferimento vengono esportati in un secondo passaggio di esportazione. |
| Enable Object Rename |Se selezionata, è possibile modificare i nomi distinti. |
| Delete-Add As Replace |Se selezionata, le operazioni di eliminazione-aggiunta vengono esportate come una singola sostituzione. |
| Enable Password Operations |Se selezionata, gli script di sincronizzazione password sono supportati. |
| Enable Export Password in First Pass |Se selezionata, le password impostate durante il provisioning vengono esportate quando viene creato l'oggetto. |

### <a name="global-parameters"></a>Global Parameters
La scheda Global Parameters (Parametri globali) in Management Agent Designer consente di configurare gli script di Windows PowerShell eseguiti dal connettore. È anche possibile configurare i valori globali per le impostazioni di configurazione personalizzate definite nella scheda Connectivity (Connettività).

**Individuazione della partizione**  
Una partizione è uno spazio dei nomi separato all'interno di uno schema condiviso. Ad esempio, in Active Directory ogni dominio è una partizione all'interno di una foresta. Una partizione è il raggruppamento logico per le operazioni di importazione ed esportazione. Queste hanno per contesto la partizione e tutte le operazioni vengono eseguite in questo contesto. Le partizioni dovrebbero rappresentare una gerarchia in LDAP. Il nome distinto di una partizione viene usato nell'importazione per verificare che tutti gli oggetti restituiti siano all'interno dell'ambito di una partizione. Il nome distinto della partizione viene usato anche durante il provisioning dal metaverse allo spazio connettore per determinare a quale partizione deve essere associato un oggetto durante l'esportazione.

Dal connettore, lo script di individuazione della partizione riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |

Lo script deve restituire un singolo oggetto [Partition][part] o un oggetto List[T] di oggetti Partition alla pipeline.

**Individuazione della gerarchia**  
Lo script di individuazione della gerarchia viene usato solo quando la funzionalità Distinguished Name Style è impostata su LDAP. Lo script viene usato per consentire di cercare e selezionare un set di contenitori che verrà considerato all'interno o all'esterno dell'ambito per le operazioni di importazione ed esportazione. Lo script deve fornire solo un elenco di nodi figlio diretti del nodo radice specificato per lo script.

Dal connettore, lo script di individuazione della gerarchia riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |
| ParentNode |[HierarchyNode][hn] |Il nodo radice della gerarchia in cui lo script deve restituire nodi figlio diretti. |

Lo script deve restituire un singolo oggetto HierarchyNode figlio o un elenco [T] di oggetti HierarchyNode figlio alla pipeline.

#### <a name="import"></a>Importa
I connettori che supportano operazioni di importazione devono implementare tre script.

**Avvio dell'importazione**  
Lo script di avvio dell'importazione viene eseguito all'inizio di un passaggio di esecuzione dell'importazione. Durante questo passaggio è possibile stabilire una connessione al sistema di origine ed eseguire passaggi preliminari prima di importare dati dal sistema connesso.

Dal connettore, lo script di avvio dell'importazione riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Fornisce informazioni allo script sul tipo di esecuzione dell'importazione (differenziale o completa), la partizione, la gerarchia, la filigrana e la dimensione della pagina prevista. |
| Tipi |[Schema][schema] |Schema per lo spazio connettore che verrà importato. |

Lo script deve restituire un singolo oggetto [OpenImportConnectionResults][oicres] alla pipeline, ad esempio: `Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importazione dei dati**  
Lo script di importazione dei dati viene chiamato dal connettore finché lo script non indica che non ci sono altri dati da importare. Il connettore Windows PowerShell ha una dimensione della pagina di 9.999 oggetti. Se lo script restituisce più di 9.999 oggetti per l'importazione, è necessario supportare il paging. Il connettore espone una proprietà dei dati personalizzata che è possibile usare per archiviare una filigrana in modo che ogni volta che viene chiamato lo script di importazione dei dati, questo riprende l'importazione di oggetti dal punto in cui era stata interrotta.

Dal connettore, lo script di importazione dei dati riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |
| GetImportEntriesRunStep |[ImportRunStep][irs] |Contiene la filigrana (CustomData) che può essere usata durante le importazioni di paging e le importazioni differenziali. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Fornisce informazioni allo script sul tipo di esecuzione dell'importazione (differenziale o completa), la partizione, la gerarchia, la filigrana e la dimensione della pagina prevista. |
| Tipi |[Schema][schema] |Schema per lo spazio connettore che verrà importato. |

Lo script di importazione dei dati deve scrivere un oggetto List[[CSEntryChange][csec]] nella pipeline. Questa raccolta è costituita da attributi CSEntryChange che rappresentano ogni oggetto da importare. Durante l'esecuzione di una importazione completa, questa raccolta deve avere un set completo di oggetti CSEntryChange con tutti gli attributi per ogni oggetto. Durante un'importazione differenziale, l'oggetto CSEntryChange deve contenere i differenziali a livello di attributo per ogni oggetto da importare oppure una rappresentazione completa degli oggetti modificati (modalità di sostituzione).

**Fine importazione**  
Al termine dell'esecuzione dell'importazione viene eseguito lo script di fine importazione. che deve eseguire tutte le operazioni di pulizia necessarie, ad esempio chiudere le connessioni ai sistemi e rispondere agli errori.

Dal connettore, lo script di fine importazione riceve i parametri seguenti:

| Name | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Fornisce informazioni allo script sul tipo di esecuzione dell'importazione (differenziale o completa), la partizione, la gerarchia, la filigrana e la dimensione della pagina prevista. |
| CloseImportConnectionRunStep |[CloseImportConnectionRunStep][cecrs] |Fornisce informazioni allo script sul motivo per cui è stata terminata l'importazione. |

Lo script deve restituire un singolo oggetto [CloseImportConnectionResults][cicres] alla pipeline, ad esempio `Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Esportazione
Come per l'architettura di importazione del connettore, i connettori che supportano l'esportazione devono implementare tre script.

**Avvio dell'importazione**  
Lo script di avvio dell'esportazione viene eseguito all'inizio di un passaggio di esecuzione dell'esportazione. Durante questo passaggio è possibile stabilire una connessione al sistema di origine ed eseguire passaggi preliminari prima di esportare dati nel sistema connesso.

Dal connettore, lo script di avvio dell'esportazione riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Fornisce informazioni allo script sul tipo di esecuzione dell'esportazione (differenziale o completa), la partizione, la gerarchia e la dimensione della pagina prevista. |
| Tipi |[Schema][schema] |Schema per lo spazio connettore che verrà esportato. |

Lo script non deve restituire alcun output alla pipeline.

**Esportazione dei dati**  
Il servizio di sincronizzazione chiama lo script di esportazione dei dati per il numero di volte necessario a elaborare tutte le esportazioni in sospeso. Se le esportazioni in sospeso dello spazio connettore superano le dimensioni di pagina del connettore, lo script di esportazione dei dati può essere chiamato più volte e anche più volte per lo stesso oggetto.

Dal connettore, lo script di esportazione dei dati riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |
| CSEntries |IList[CSEntryChange][csec] |Elenco di tutti gli oggetti dello spazio del connettore con esportazioni in sospeso da elaborare durante questo passaggio. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Fornisce informazioni allo script sul tipo di esecuzione dell'esportazione (differenziale o completa), la partizione, la gerarchia e la dimensione della pagina prevista. |
| Tipi |[Schema][schema] |Schema per lo spazio connettore che verrà esportato. |

Lo script di esportazione dei dati deve restituire un oggetto [PutExportEntriesResults][peeres] alla pipeline. Questo oggetto non deve necessariamente includere le informazioni sui risultati per ogni connettore esportato a meno che non si verifichi un errore o una modifica dell'attributo di ancoraggio. Ad esempio, per restituire un oggetto PutExportEntriesResults alla pipeline: `Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Fine esportazione**  
Al termine dell'esecuzione dell'esportazione viene eseguito lo script di fine esportazione. che deve eseguire tutte le operazioni di pulizia necessarie, ad esempio chiudere le connessioni ai sistemi e rispondere agli errori.

Dal connettore, lo script di fine esportazione riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Fornisce informazioni allo script sul tipo di esecuzione dell'esportazione (differenziale o completa), la partizione, la gerarchia e la dimensione della pagina prevista. |
| CloseExportConnectionRunStep |[CloseExportConnectionRunStep][cecrs] |Fornisce informazioni allo script sul motivo per cui è stata terminata l'esportazione. |

Lo script non deve restituire alcun output alla pipeline.

#### <a name="password-synchronization"></a>Sincronizzazione delle password
I connettori Windows PowerShell possono essere usati come destinazione per la modifica o la reimpostazione delle password.

Dal connettore, lo script della password riceve i parametri seguenti:

| NOME | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][stringa, [ConfigParameter][cp]] |Tabella dei parametri di configurazione per il connettore. |
| Credenziali |[PSCredential][pscred] |Contiene le credenziali immesse dall'amministratore nella scheda Connectivity. |
| Partition |[Partizione][part] |Partizione della directory in cui si trova CSEntry. |
| CSEntry |[CSEntry][cse] |Voce dello spazio del connettore per l'oggetto che ha ricevuto una modifica o la reimpostazione della password. |
| OperationType |string |Indica se l'operazione è una reimpostazione (**SetPassword**) o una modifica (**ChangePassword**). |
| PasswordOptions |[PasswordOptions][pwdopt] |Flag che specifica il comportamento di reimpostazione della password preferito. Questo parametro è disponibile unicamente se OperationType è **SetPassword**. |
| OldPassword |string |Popolato con la vecchia password dell'oggetto per le operazioni di modifica della password. Questo parametro è disponibile unicamente se OperationType è **ChangePassword**. |
| NewPassword |string |Popolato con la nuova password dell'oggetto che lo script deve impostare. |

Lo script della password non deve restituire alcun risultato alla pipeline di Windows PowerShell. Se si verifica un errore nello script della password, lo script genera una delle eccezioni seguenti per segnalare il problema al servizio di sincronizzazione:

* [PasswordPolicyViolationException][pwdex1]: viene generata se la password non soddisfa i criteri della password nel sistema connesso.
* [PasswordIllFormedException][pwdex2]: viene generata se la password non è accettabile per il sistema connesso.
* [PasswordExtension][pwdex3]: viene generata per tutti gli altri errori nello script della password.

## <a name="sample-connectors"></a>Connettori di esempio
Per una panoramica completa dei connettori di esempio disponibili, vedere [Windows PowerShell Connector Sample Connector Collection][samp] (Raccolta di connettori di esempio per Windows PowerShell).

## <a name="other-notes"></a>Altre note
### <a name="additional-configuration-for-impersonation"></a>Configurazione aggiuntiva per la rappresentazione
Concedere all'utente rappresentato le seguenti autorizzazioni nel server del servizio di sincronizzazione:

Accesso in scrittura alle chiavi del Registro di sistema seguenti:

* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Software\Microsoft\PowerShell
* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Environment

Per determinare l'ID di sicurezza (SID) dell'account di servizio del servizio di sincronizzazione, eseguire i comandi di PowerShell seguenti:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Accesso in lettura alle cartelle del file system seguenti:

* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\Extensions
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Sostituire il nome del connettore Windows PowerShell per il segnaposto {ConnectorName}.

## <a name="troubleshooting"></a>risoluzione dei problemi
* Per informazioni su come abilitare la registrazione per risolvere i problemi relativi al connettore, vedere l'articolo relativo a [come abilitare la traccia ETW per i connettori](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
