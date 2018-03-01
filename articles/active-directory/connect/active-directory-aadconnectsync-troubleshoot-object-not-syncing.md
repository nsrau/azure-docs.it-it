---
title: Risoluzione dei problemi relativi a un oggetto che non esegue la sincronizzazione in Azure AD | Microsoft Docs
description: Risoluzione dei problemi per cui un oggetto non esegue la sincronizzazione in Azure AD.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: e68b70ce87a6fedab1b85bf2800a50e512910dea
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Risoluzione dei problemi relativi a un oggetto che non esegue la sincronizzazione in Azure AD

Se un oggetto non esegue la sincronizzazione come previsto in Azure AD, le cause possono essere diverse. Se si riceve un'email di errore da Azure AD o viene visualizzato l'errore in Azure AD Connect Health, leggere l'articolo sulla [risoluzione degli errori di esportazione](active-directory-aadconnect-troubleshoot-sync-errors.md). Tuttavia se si vuole risolvere un problema per cui l'oggetto non è in Azure AD, questo è l'argomento adatto. Viene descritto come individuare gli errori di sincronizzazione nella componente locale di Azure AD Connect.

>[!IMPORTANT]
>Per la distribuzione di Azure Active Directory (AAD) Connect versione <verison> o successiva, usare l'attività specifica nella procedura guidata per la [risoluzione dei problemi](active-directory-aadconnect-troubleshoot-objectsync.md) di sincronizzazione degli oggetti. 

Per individuare gli errori è necessario esaminare vari punti nell'ordine seguente:

1. I [registri delle operazioni](#operations) per cercare gli errori rilevati dal motore di sincronizzazione durante l'importazione e la sincronizzazione.
2. Lo [spazio connettore](#connector-space-object-properties) per cercare gli oggetti mancanti e gli errori di sincronizzazione.
3. [Metaverse](#metaverse-object-properties) per individuare i problemi relativi ai dati.

Avviare [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) prima di iniziare questa procedura.

## <a name="operations"></a>Operazioni
La risoluzione dei problemi deve essere avviata dalla scheda Operazioni in Synchronization Service Manager. La scheda Operazioni mostra i risultati delle ultime operazioni.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

La metà superiore mostra tutte le esecuzioni in ordine cronologico. Per impostazione predefinita, il log delle operazioni mantiene le informazioni relative agli ultimi sette giorni, ma è possibile modificare questa impostazione tramite l' [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md). Si vogliono cercare le esecuzioni che non hanno lo stato di operazione riuscita. È possibile modificare l'ordinamento facendo clic sulle intestazioni.

La colonna **Status** (Stato) visualizza le informazioni più importanti e segnala il problema più grave di un'esecuzione. Ecco un breve riepilogo degli stati disponibili, ordinati in base alla priorità con cui vanno analizzati (dove * indica diverse stringhe di errore possibili).

| Status | Comment |
| --- | --- |
| stopped-* |Impossibile completare l'esecuzione. Ad esempio se il sistema remoto è inattivo e non può essere contattato. |
| stopped-error-limit |Sono presenti più di 5.000 errori. L'esecuzione è stata arrestata automaticamente a causa dell'elevato numero di errori. |
| completato-\*-errori |L'esecuzione è stata completata, ma sono presenti errori (meno di 5.000) che devono essere analizzati. |
| completato-\*-avvisi |L'esecuzione è stata completata, ma alcuni dati non si trovano nello stato previsto. Se sono presenti errori, questo messaggio è in genere solo un sintomo. Evitare di analizzare gli avvisi fino a quando gli errori non sono stati risolti. |
| esito positivo |Non sono presenti problemi. |

Quando si seleziona una riga, la parte inferiore viene aggiornata per visualizzare i dettagli di quella esecuzione. All'estrema sinistra della parte inferiore è possibile avere un elenco con voci del tipo **Step #**(Passaggio [n.]). L'elenco è mostrato solo quando nella foresta sono presenti più domini e ogni dominio è rappresentato da un passaggio. Il nome di dominio è visibile sotto l'intestazione **Partition**(Partizione). In **Synchronization Statistics**(Statistiche di sincronizzazione) sono presenti altre informazioni sul numero delle modifiche elaborate. È possibile fare clic sui collegamenti per ottenere un elenco degli oggetti modificati. Se alcuni oggetti presentano errori, questi verranno visualizzati in **Synchronization Errors**(Errori di sincronizzazione).

### <a name="troubleshoot-errors-in-operations-tab"></a>Risolvere gli errori nella scheda Operazioni
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
In caso di errori, sia l'oggetto con l'errore che l'errore stesso appaiono sotto forma di collegamenti per la visualizzazione di altre informazioni.

Per iniziare, fare clic su una stringa di errore. Nella figura si tratta di **sync-rule-error-function-triggered**. Verrà prima presentata una panoramica dell'oggetto. Per visualizzare l'errore effettivo, fare clic sul pulsante **Stack Trace** (Analisi dello stack). Questa analisi indica informazioni di debug relative all'errore.

È possibile fare clic con il pulsante destro del mouse nella casella delle **call stack information** (Informazioni sullo stack delle chiamate), scegliere **Seleziona tutto** e quindi **Copia**. Si può quindi copiare lo stack ed esaminare l'errore nell'editor preferito, ad esempio il Blocco note.

* Se l'errore proviene da **SyncRulesEngine**, le informazioni sullo stack delle chiamate visualizzano prima di tutto un elenco di tutti gli attributi dell'oggetto. Scorrere verso il basso fino all'intestazione **InnerException =>**.  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  L'errore è visualizzato nella riga successiva. Nell'immagine precedente l'errore proviene da una regola di sincronizzazione personalizzata creata da Fabrikam.

Se l'errore non fornisce informazioni sufficienti, è necessario esaminare i dati. È possibile fare clic sul collegamento con l'identificatore dell'oggetto e continuare la risoluzione dei problemi dell'[oggetto importato spazio connettore](#cs-import).

## <a name="connector-space-object-properties"></a>Proprietà dell’oggetto spazio connettore
Se nella scheda [Operazioni](#operations) non sono stati rilevati errori, il passaggio successivo consiste nel seguire l'oggetto spazio connettore da Active Directory, in Metaverse e in Azure AD. In questo percorso, è necessario trovare il problema.

### <a name="search-for-an-object-in-the-cs"></a>Ricerca di un oggetto in CS

In **Synchronization Service Manager**, fare clic su **Connettori**, selezionare il connettore di Active Directory e scegliere **Search Connector Space** (Ricerca spazio connettore).

In **Ambito** selezionare **RDN** (quando si desidera eseguire la ricerca dell'attributo CN) o **DN o ancoraggio** (quando si desidera eseguire la ricerca nell'attributo distinguishedName). Immettere un valore e fare clic su **Cerca**.  
![Ricerca spazio connettore](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

Se non si trova l'oggetto che si sta cercando, questo potrebbe essere stato filtrato con [il filtro basato su dominio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) o [con il filtro basato sull'unità organizzativa](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering). Leggere l'argomento su come [configurare il filtro](active-directory-aadconnectsync-configure-filtering.md) per verificare che il filtro sia configurato come previsto.

Un'altra ricerca utile consiste nel selezionare il connettore Azure AD: nell'**Ambito** selezionare **Importazione in sospeso** e quindi la casella di controllo **Aggiungi**. Questa ricerca restituisce tutti gli oggetti sincronizzati in Azure AD che non possono essere associati a un oggetto locale.  
![Orfano ricerca spazio connettore](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
Tali oggetti sono stati creati da un altro motore di sincronizzazione o da un motore di sincronizzazione con una diversa configurazione filtro. Questa vista contiene un elenco di oggetti **orfani** non più gestiti. È opportuno esaminare questo elenco e considerare la possibilità di rimuovere questi oggetti tramite il cmdlet [Azure AD PowerShell](https://aka.ms/aadposh).

### <a name="cs-import"></a>Importazione CS
Quando si apre un oggetto cs, nella parte superiore sono presenti diverse schede. La scheda **Import** (Importa) visualizza i dati di gestione temporanea dopo l'importazione.  
![Oggetto CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
La colonna **Valore precedente** mostra ciò che è attualmente memorizzato in Connect, mentre **Nuovo valore** ciò che è stato ricevuto dal sistema di origine e che non è stato ancora applicato. Se si verifica un errore sull'oggetto, le modifiche non vengono elaborate.

**Error (Errore) (Error (Errore)e)**  
![Oggetto CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
La scheda **Errore di sincronizzazione** è visibile solo se si è verificato un problema con l'oggetto. Per altre informazioni, vedere l'articolo su come [risolvere gli errori di sincronizzazione](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>Derivazione CS
La scheda Lineage (Derivazione) visualizza la correlazione fra l'oggetto spazio connettore e l'oggetto metaverse. È possibile vedere l'ultima volta in cui il connettore ha importato una modifica dal sistema connesso e quali regole sono state applicate per popolare i dati nel metaverse.  
![Derivazione CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
Nella colonna **Action** (Azione) è presente una regola di sincronizzazione **Inbound** (In ingresso) con l'azione **Provision** (Provisioning). Questo indica che l'oggetto metaverse rimarrà fino a quando sarà presente l'oggetto spazio connettore. Se nell'elenco delle regole di sincronizzazione è invece visualizzata una regola di sincronizzazione con direzione **Outbound** (In uscita) e l'azione **Provision** (Provisioning), questo oggetto viene eliminato alla rimozione dell'oggetto metaverse.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
Si noti anche che nella colonna **PasswordSync** (Sincronizzazione password) lo spazio connettore in ingresso può apportare modifiche alla password poiché una regola di sincronizzazione ha il valore **True**. Questa password viene poi inviata ad Azure AD attraverso la regola in uscita.

Dalla scheda Lineage (Derivazione) è possibile accedere al metaverse facendo clic su [Metaverse Object Properties](#mv-attributes)(Proprietà oggetto metaverse).

In fondo a tutte le schede sono disponibili due pulsanti: **Preview** (Anteprima) e **Log** (Log).

### <a name="preview"></a>Preview
La pagina Preview (Anteprima) viene usata per sincronizzare un solo oggetto. È utile mentre si risolvono problemi relativi alle regole di sincronizzazione personalizzate e si vuole vedere l'effetto di una modifica su un singolo oggetto. È possibile scegliere tra **Sincronizzazione completa** e **Sincronizzazione delta**. È anche possibile scegliere tra **Genera anteprima**, per mantenere semplicemente la modifica in memoria, ed **Esegui commit anteprima**, per inserire tutte le modifiche negli spazi connettore di destinazione.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
Si può esaminare l'oggetto e individuare la regola applicata per un particolare flusso di attributi.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>Log
La pagina Log consente di visualizzare lo stato e la cronologia di sincronizzazione della password. Per altre informazioni, vedere [Risolvere i problemi di sincronizzazione delle password](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="metaverse-object-properties"></a>Proprietà dell'oggetto Metaverse
In genere è preferibile iniziare la ricerca dallo [spazio connettore](#connector-space) di origine di Active Directory. Ma è anche possibile avviare la ricerca da metaverse.

### <a name="search-for-an-object-in-the-mv"></a>Ricerca di un oggetto in MV
In **Synchronization Service Manager**, fare clic su **Metaverse Search** (Cerca Metaverse). Creare una query che rileva l'utente. È possibile cercare gli attributi comuni, ad esempio accountName (sAMAccountName) e userPrincipalName. Per altre informazioni, vedere [Cerca Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md).
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

Nella finestra **Risultati della ricerca**, fare clic sull'oggetto.

Se l'oggetto non è stato trovato, non ha ancora raggiunto metaverse. Continuare la ricerca dell'oggetto nello [spazio connettore](#connector-space-object-properties) di Active Directory. Potrebbe essersi verificato un errore di sincronizzazione che non consente all'oggetto di raggiungere metaverse oppure potrebbe essere stato applicato un filtro.

### <a name="mv-attributes"></a>Attributi MV
Nella scheda Attributes (Attributi) è possibile visualizzare i valori e il connettore che li ha indicati.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

Se un oggetto non viene sincronizzato, esaminare i seguenti attributi in metaverse:
- L'attributo **cloudFiltered** è presente e impostato su **vero**? In questo caso, è stato filtrato in base ai passaggi in [attribute based filtering](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering) (Filtri basati sugli attributi).
- L'attributo **sourceAnchor** è presente? Se non lo è, si dispone di una topologia di foresta account-risorse? Se un oggetto viene identificato come cassetta postale collegata (l'attributo **msExchRecipientTypeDetails** ha il valore 2), l'attributo sourceAnchor viene fornito dalla foresta con un account di Active Directory abilitato. Assicurarsi che l'account principale sia stato importato e sincronizzato correttamente. L'account principale deve essere elencato tra i [connettori](#mv-connectors) dell'oggetto.

### <a name="mv-connectors"></a>Connettori MV
La scheda Connectors (Connettori) visualizza tutti gli spazi connettore che hanno una rappresentazione dell'oggetto.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
È necessario disporre di un connettore per:

- Ogni foresta di Active Directory in cui l'utente viene rappresentato. Questa rappresentazione può includere foreignSecurityPrincipals e gli oggetti Contatto.
- Un connettore in Azure AD.

Se manca il connettore in Azure AD, leggere [Attributi MV](#MV-attributes) per verificare i criteri per il provisioning in Azure AD.

Questa scheda consente anche di passare all'[oggetto spazio connettore](#connector-space-object-properties). Selezionare una riga e fare clic su **Proprietà**.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
