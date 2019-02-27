---
title: Risolvere i problemi per la mancata sincronizzazione di un oggetto con Azure Active Directory | Microsoft Docs
description: Risolvere i problemi per la mancata sincronizzazione di un oggetto con Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416922"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Risolvere i problemi per la mancata sincronizzazione di un oggetto con Azure Active Directory

Se un oggetto non viene sincronizzato come previsto con Microsoft Azure Active Directory (Azure AD), il problema può essere dovuto a vari motivi. Se si riceve un messaggio di errore tramite posta elettronica da Azure AD o l'errore viene visualizzato in Azure AD Connect Health, leggere l'articolo [Risoluzione degli errori durante la sincronizzazione](tshoot-connect-sync-errors.md). Se invece si sta risolvendo un problema dovuto al fatto che l'oggetto non è incluso in Azure AD, questo è l'argomento adatto. Questo articolo descrive infatti come trovare gli errori di sincronizzazione nel componente locale Azure AD Connect.

>[!IMPORTANT]
>Per la distribuzione di Azure AD Connect versione 1.1.749.0 o successiva, usare l'[attività specifica](tshoot-connect-objectsync.md) nella procedura guidata per la risoluzione dei problemi di sincronizzazione degli oggetti. 

## <a name="synchronization-process"></a>Processo di sincronizzazione

Prima di esaminare i problemi di sincronizzazione, è importante conoscere il processo di sincronizzazione di Azure AD Connect:

  ![Diagramma del processo di sincronizzazione di Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** spazio connettore, una tabella in un database
* **MV:** metaverse, una tabella in un database

### <a name="synchronization-steps"></a>**Passaggi di sincronizzazione**
Il processo di sincronizzazione prevede i passaggi seguenti:

1. **Importazione da AD:** gli oggetti di Active Directory vengono importati nello spazio connettore di Active Directory.

2. **Importazione da Azure AD:** gli oggetti di Azure AD vengono importati nello spazio connettore di Azure AD.

3. **Sincronizzazione:** le regole di sincronizzazione in ingresso e in uscita vengono eseguite in base al numero di precedenza, dal più basso al più alto. Per visualizzare le regole di sincronizzazione, è possibile passare all'editor delle regole di sincronizzazione da un'applicazione desktop. Le regole di sincronizzazione in ingresso importano i dati dallo spazio connettore al metaverse, mentre le regole di sincronizzazione in uscita spostano i dati dal metaverse allo spazio connettore.

4. **Esportazione in AD:** dopo la sincronizzazione, gli oggetti vengono esportati dallo spazio connettore di Active Directory in Active Directory.

5. **Esportazione in Azure AD:** dopo la sincronizzazione, gli oggetti vengono esportati dallo spazio connettore di Azure AD in Azure AD.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per trovare gli errori, controllare in diverse aree, nell'ordine seguente:

1. I [registri delle operazioni](#operations) per cercare gli errori rilevati dal motore di sincronizzazione durante l'importazione e la sincronizzazione.
2. Lo [spazio connettore](#connector-space-object-properties) per cercare gli oggetti mancanti e gli errori di sincronizzazione.
3. Il [metaverse](#metaverse-object-properties) per trovare i problemi relativi ai dati.

Avviare [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) prima di iniziare questa procedura.

## <a name="operations"></a>Operazioni
La risoluzione dei problemi deve essere avviata dalla scheda **Operations** (Operazioni) di Synchronization Service Manager, in cui vengono visualizzati i risultati delle ultime operazioni. 

![Screenshot di Synchronization Service Manager con la scheda Operations (Operazioni) selezionata](./media/tshoot-connect-object-not-syncing/operations.png)  

Nella metà superiore della scheda **Operations** (Operazioni) sono elencate tutte le esecuzioni in ordine cronologico. Per impostazione predefinita, il log delle operazioni mantiene le informazioni relative agli ultimi sette giorni, ma è possibile modificare questa impostazione tramite l'[utilità di pianificazione](how-to-connect-sync-feature-scheduler.md). Cercare eventuali esecuzioni il cui stato è diverso da **success** (esito positivo). È possibile modificare l'ordinamento facendo clic sulle intestazioni.

La colonna **Status** (Stato) contiene le informazioni più importanti e segnala il problema più grave di un'esecuzione. Di seguito è riportato un breve riepilogo degli stati più comuni, ordinati in base alla priorità con cui devono essere analizzati (dove * indica varie possibili stringhe di errore).

| Stato | Commento |
| --- | --- |
| stopped-* |Impossibile completare l'esecuzione. Questa condizione si verifica, ad esempio, se il sistema remoto è inattivo e non può essere contattato. |
| stopped-error-limit |Sono presenti più di 5.000 errori. L'esecuzione è stata arrestata automaticamente a causa dell'elevato numero di errori. |
| completed-\*-errors |L'esecuzione è stata completata, ma sono presenti errori (meno di 5.000) che devono essere analizzati. |
| completed-\*-warnings |L'esecuzione è stata completata, ma alcuni dati non si trovano nello stato previsto. Se sono presenti errori, questo messaggio è in genere solo un sintomo. Evitare di analizzare gli avvisi fino a quando non sono stati risolti tutti gli errori. |
| success |Non sono presenti problemi. |

Quando si seleziona una riga, la parte inferiore della scheda **Operations** (Operazioni) viene aggiornata in base ai dettagli dell'esecuzione corrispondente. All'estrema sinistra di questa area è possibile che sia presente un elenco con voci di tipo **Step #** (Passaggio n.). L'elenco viene visualizzato solo se nella foresta sono presenti più domini e ogni dominio è rappresentato da un passaggio. Il nome di dominio è visibile sotto l'intestazione **Partition** (Partizione). Sotto l'intestazione **Synchronization Statistics** (Statistiche di sincronizzazione) sono presenti altre informazioni sul numero delle modifiche elaborate. Selezionare i collegamenti per ottenere un elenco degli oggetti modificati. Se alcuni oggetti presentano errori, questi verranno visualizzati sotto l'intestazione **Synchronization Errors** (Errori di sincronizzazione).

### <a name="errors-on-the-operations-tab"></a>Errori nella scheda Operations (Operazioni)
In caso di errori, Synchronization Service Manager mostra sia l'oggetto con l'errore sia l'errore stesso sotto forma di collegamenti per la visualizzazione di altre informazioni.

![Screenshot degli errori in Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Per iniziare, selezionare la stringa di errore. Nella figura precedente, la stringa di errore è **sync-rule-error-function-triggered**. Verrà prima presentata una panoramica dell'oggetto. Per visualizzare l'errore effettivo, selezionare **Stack Trace** (Analisi dello stack). Questa analisi fornisce informazioni di debug relative all'errore.

Fare clic con il pulsante destro del mouse nella casella **Call Stack Information** (Informazioni sullo stack delle chiamate), fare clic su **Select All** (Seleziona tutto) e quindi selezionare **Copy** (Copia). Copiare quindi lo stack ed esaminare l'errore nell'editor preferito, ad esempio nel Blocco note.

Se l'errore proviene da **SyncRulesEngine**, le informazioni sullo stack delle chiamate includono come primo elemento un elenco di tutti gli attributi dell'oggetto. Scorrere verso il basso fino all'intestazione **InnerException =>**.  

  ![Screenshot di Synchronization Service Manager con informazioni sull'errore sotto l'intestazione InnerException = >](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
L'errore viene visualizzato nella riga successiva. Nella figura precedente, l'errore proviene da una regola di sincronizzazione personalizzata creata da Fabrikam.

Se l'errore non fornisce informazioni sufficienti, è necessario esaminare i dati. Selezionare il collegamento con l'identificatore dell'oggetto e continuare la risoluzione del problema relativo all'[oggetto importato nello spazio connettore](#cs-import).

## <a name="connector-space-object-properties"></a>Proprietà dell'oggetto nello spazio connettore
Se nella scheda [**Operations**](#operations) (Operazioni) non vengono visualizzati errori, seguire l'oggetto dello spazio connettore da Active Directory al metaverse, fino ad Azure Active Directory. In questo percorso, è necessario trovare il problema.

### <a name="searching-for-an-object-in-the-cs"></a>Ricerca di un oggetto nello spazio connettore

In Synchronization Service Manager selezionare **Connectors** (Connettori), il connettore di Active Directory e **Search Connector Space** (Ricerca spazio connettore).

Nella casella **Scope** (Ambito) selezionare **RDN** se si vuole eseguire la ricerca in base all'attributo CN o **DN or anchor** (DN o ancoraggio) se si vuole eseguire la ricerca in base all'attributo **distinguishedName**. Immettere un valore e selezionare **Search** (Cerca). 
 
![Screenshot di una ricerca nello spazio connettore](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Se non si trova l'oggetto che si sta cercando, è possibile che sia stato filtrato con il [filtro basato sul dominio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) o con il [filtro basato sull'unità organizzativa](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Per verificare che il filtro sia stato configurato come previsto, leggere l'argomento [Servizio di sincronizzazione Azure AD Connect: Configurare il filtro](how-to-connect-sync-configure-filtering.md).

È possibile eseguire un'altra ricerca utile selezionando il connettore Azure Active Directory. Nella casella **Scope** (Ambito) selezionare **Pending Import** (Importazione in sospeso) e quindi la casella di controllo **Add** (Aggiungi). Questa ricerca restituisce tutti gli oggetti sincronizzati in Azure Active Directory che non possono essere associati a un oggetto locale.  

![Screenshot di oggetti orfani in una ricerca nello spazio connettore](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Questi oggetti sono stati creati da un altro motore di sincronizzazione o da un motore di sincronizzazione con una diversa configurazione dei filtri. Questi oggetti orfani non vengono più gestiti. Esaminare questo elenco e considerare la possibilità di rimuovere questi oggetti tramite i cmdlet di [Azure AD PowerShell](https://aka.ms/aadposh).

### <a name="cs-import"></a>Importazione nello spazio connettore
Quando si apre un oggetto nello spazio connettore, nella parte superiore vengono visualizzate varie schede. La scheda **Import** (Importa) visualizza i dati di gestione temporanea dopo l'importazione.  

![Screenshot della finestra Connector Space Object Properties (Proprietà dell'oggetto spazio connettore) con la scheda Import (Importa) selezionata](./media/tshoot-connect-object-not-syncing/csobject.png)    

La colonna **Old Value** (Valore precedente) elenca gli elementi attualmente memorizzati in Connect, mentre la colonna **New Value** (Nuovo valore) mostra gli elementi ricevuti dal sistema di origine e non ancora applicati. Se si verifica un errore nell'oggetto, le modifiche non vengono elaborate.

La scheda **Synchronization Error** (Errore di sincronizzazione) viene visualizzata nella finestra **Connector Space Object Properties** (Proprietà dell'oggetto spazio connettore) solo se si è verificato un problema con l'oggetto. Per altre informazioni, rivedere come [risolvere errori di sincronizzazione nella scheda **Operations** (Operazioni)](#errors-on-the-operations-tab).

![Screenshot della scheda Synchronization Error (Errore di sincronizzazione) nella finestra Connector Space Object Properties (Proprietà dell'oggetto spazio connettore)](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>Derivazione dello spazio connettore
La scheda **Lineage** (Derivazione) disponibile nella finestra **Connector Space Object Properties** (Proprietà dell'oggetto spazio connettore) visualizza la correlazione fra l'oggetto nello spazio connettore e l'oggetto nel metaverse. È possibile vedere l'ultima volta in cui il connettore ha importato una modifica dal sistema connesso e quali regole sono state applicate per popolare i dati nel metaverse.  

![Screenshot della scheda Lineage (Derivazione) nella finestra Connector Space Object Properties (Proprietà dell'oggetto spazio connettore)](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Nella figura precedente, la colonna **Action** (Azione) mostra una regola di sincronizzazione in ingresso con l'azione **Provision** (Provisioning). Questo indica che l'oggetto nel metaverse rimarrà fino a quando sarà presente l'oggetto nello spazio connettore. Se invece nell'elenco delle regole di sincronizzazione è presente una regola di sincronizzazione in uscita con l'azione **Provision** (Provisioning), l'oggetto viene eliminato quando viene rimosso l'oggetto nel metaverse.  

![Screenshot di una finestra di derivazione nella scheda Lineage (Derivazione) della finestra Connector Space Object Properties (Proprietà dell'oggetto spazio connettore)](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Nella figura precedente, nella colonna **PasswordSync** (Sincronizzazione password) lo spazio connettore in ingresso può modificare la password, poiché una regola di sincronizzazione presenta il valore **True**. Questa password viene inviata ad Azure AD attraverso la regola in uscita.

Dalla scheda **Lineage** (Derivazione) è possibile accedere al metaverse selezionando [**Metaverse Object Properties**](#mv-attributes) (Proprietà dell'oggetto metaverse).

### <a name="preview"></a>Anteprima
Nell'angolo inferiore sinistro della finestra **Connector Space Object Properties** (Proprietà dell'oggetto spazio connettore) è disponibile il pulsante **Preview** (Anteprima). Selezionare questo pulsante per aprire la pagina **Preview** (Anteprima), in cui è possibile sincronizzare un singolo oggetto. Questa pagina è utile mentre si risolvono problemi relativi alle regole di sincronizzazione personalizzate e si vuole vedere l'effetto di una modifica su un singolo oggetto. È possibile selezionare una **sincronizzazione completa** o una **sincronizzazione differenziale**. È anche possibile selezionare **Generate Preview** (Genera anteprima), che mantiene semplicemente la modifica in memoria, o **Commit Preview** (Esegui commit anteprima), che aggiorna il metaverse e inserisce tutte le modifiche negli spazi connettore di destinazione.  

![Screenshot della pagina di anteprima, con l'opzione Start Preview (Avvia anteprima) selezionata](./media/tshoot-connect-object-not-syncing/preview.png)  

Nell'anteprima è possibile esaminare l'oggetto e individuare la regola applicata per un particolare flusso di attributi.  

![Screenshot della pagina di anteprima con la scheda Import Attribute Flow (Importa flusso attributi)](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Log
Accanto al pulsante **Preview** (Anteprima) selezionare il pulsante **Log** per aprire la pagina **Log**, in cui sono riportati lo stato e la cronologia di sincronizzazione della password. Per altre informazioni, vedere [Risolvere i problemi di sincronizzazione dell'hash delle password con la sincronizzazione di Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Proprietà dell'oggetto nel metaverse
È in genere preferibile avviare la ricerca dallo spazio connettore di origine di Active Directory, ma è possibile avviarla anche dal metaverse.

### <a name="searching-for-an-object-in-the-mv"></a>Ricerca di un oggetto nel metaverse
In Synchronization Service Manager selezionare **Metaverse Search** (Ricerca metaverse), come illustrato nella figura seguente. Creare una query che rilevi l'utente. È possibile cercare gli attributi comuni, ad esempio **accountName** (**sAMAccountName**) e **userPrincipalName**. Per altre informazioni, vedere [Ricerca metaverse di Synchronization Service Managerr](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Screenshot di Synchronization Service Manager con la scheda Metaverse Search (Ricerca metaverse) selezionata](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Nella finestra **Risultati della ricerca**, fare clic sull'oggetto.

Se l'oggetto non è stato trovato, significa che non ha ancora raggiunto il metaverse. Continuare la ricerca dell'oggetto nello [spazio connettore](#connector-space-object-properties) di Active Directory. Se l'oggetto viene trovato nello spazio connettore di Active Directory, potrebbe essersi verificato un errore di sincronizzazione che sta bloccando il passaggio dell'oggetto nel metaverse oppure potrebbe essere applicato un filtro di ambito per la regola di sincronizzazione.

### <a name="object-not-found-in-the-mv"></a>Oggetto non trovato nel metaverse
Se l'oggetto è presente nello spazio connettore di Active Directory ma non è presente nel metaverse, è stato applicato un filtro di ambito. Per esaminare il filtro di ambito, passare al menu delle applicazioni desktop e selezionare **Synchronization Rules Editor** (Editor delle regole di sincronizzazione). Filtrare le regole applicabili all'oggetto modificando il filtro seguente.

  ![Screenshot dell'editor delle regole di sincronizzazione con una ricerca di regole di sincronizzazione in ingresso](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Visualizzare ogni regola nell'elenco precedente e verificare **Scoping filter** (Filtro di ambito). Nel filtro di ambito seguente, se il valore **isCriticalSystemObject** è null o FALSE oppure è vuoto, significa che si trova nell'ambito.

  ![Screenshot di un filtro di ambito in una ricerca di regole di sincronizzazione in ingresso](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Andare all'elenco di attributi di [CS Import](#cs-import) (Importazione CS) e controllare quale filtro sta bloccando il passaggio dell'oggetto nel metaverse. L'elenco di attributi di **Connector Space** (Spazio connettore) mostrerà solo gli attributi non null e non vuoti. Se ad esempio **isCriticalSystemObject** non è visibile nell'elenco, significa che il valore di questo attributo è null o vuoto.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Oggetto non trovato nello spazio connettore di Azure Active Directory
Se l'oggetto non è presente nello spazio connettore di Azure Active Directory ma è presente nel metaverse, esaminare il filtro di ambito delle regole in uscita dello spazio connettore corrispondente e verificare se l'oggetto viene escluso perché gli [attributi del metaverse](#mv-attributes) non soddisfano i criteri.

Per esaminare il filtro di ambito in uscita, selezionare le regole applicabili per l'oggetto modificando il filtro riportato di seguito. Visualizzare ogni regola ed esaminare il valore dell'[attributo del metaverse](#mv-attributes) corrispondente.

  ![Screenshot di una ricerca di regole di sincronizzazione in uscita nell'editor delle regole di sincronizzazione](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Attributi del metaverse
Nella scheda **Attributes** (Attributi) è possibile visualizzare i valori e il connettore che li ha indicati.  

![Screenshot della finestra Metaverse Object Properties (Proprietà dell'oggetto metaverse) con la scheda Attributes (Attributi) selezionata](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Se un oggetto non viene sincronizzato, formulare le domande seguenti in merito agli stati degli attributi nel metaverse:
- L'attributo **cloudFiltered** è presente e impostato su **True**? In questo caso, è stato filtrato in base ai passaggi descritti in [Filtro basato su attributo](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- L'attributo **sourceAnchor** è presente? Se non lo è, si dispone di una topologia di foresta account-risorse? Se un oggetto viene identificato come cassetta postale collegata (l'attributo **msExchRecipientTypeDetails** ha il valore **2**), l'attributo **sourceAnchor** viene fornito dalla foresta con un account di Active Directory abilitato. Assicurarsi che l'account principale sia stato importato e sincronizzato correttamente. L'account principale deve essere elencato tra i [connettori](#mv-connectors) dell'oggetto.

### <a name="mv-connectors"></a>Connettori del metaverse
La scheda **Connectors** (Connettori) visualizza tutti gli spazi connettore che hanno una rappresentazione dell'oggetto. 
 
![Screenshot della finestra Metaverse Object Properties (Proprietà dell'oggetto metaverse) con la scheda Connectors (Connettori) selezionata](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

È necessario disporre di un connettore per:

- Ogni foresta di Active Directory in cui l'utente viene rappresentato. Questa rappresentazione può includere oggetti **foreignSecurityPrincipals** e **Contact**.
- Un connettore in Azure AD.

Se non si ha un connettore in Azure AD, rivedere la sezione [Attributi del metaverse](#mv-attributes) per verificare i criteri per il provisioning in Azure AD.

Dalla scheda **Connectors** (Connettori) è possibile anche passare all'[oggetto nello spazio connettore](#connector-space-object-properties). Selezionare una riga e fare clic su **Properties** (Proprietà).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [servizio di sincronizzazione di Azure AD Connect](how-to-connect-sync-whatis.md).
- Altre informazioni sulle [identità ibride](whatis-hybrid-identity.md).
