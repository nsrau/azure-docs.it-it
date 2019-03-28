---
title: Risolvere i problemi di sincronizzazione dati SQL di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi comuni di sincronizzazione dati SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 4e2808378834a0270586ce674e1043ca443320c5
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58539633"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Risolvere i problemi della sincronizzazione dati SQL

Questo articolo descrive come risolvere i problemi noti di sincronizzazione dati SQL di Azure. Le possibili risoluzioni a un problema vengono fornite qui.

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Al momento, la sincronizzazione dati SQL di Azure **non** supporta Istanza gestita di database SQL di Azure.

## <a name="sync-issues"></a>Problemi di sincronizzazione

- [La sincronizzazione non viene eseguita correttamente nell'interfaccia utente del portale per i database locali associati all'agente client.](#sync-fails)

- [Il gruppo di sincronizzazione è bloccato nello stato di elaborazione](#sync-stuck)

- [Vengono visualizzati dati non corretti nelle tabelle](#sync-baddata)

- [Vengono visualizzati dati della chiave primaria incoerenti dopo una sincronizzazione riuscita](#sync-pkdata)

- [Si osserva una riduzione delle prestazioni considerevole](#sync-perf)

- [Viene visualizzato il messaggio: "Impossibile inserire il valore NULL nella colonna \<colonna >. La colonna non ammette valori Null." Cosa significa e come è possibile correggere l'errore?](#sync-nulls)

- [In che modo la sincronizzazione dati gestisce i riferimenti circolari? In altre parole, quando vengono sincronizzati gli stessi dati in più gruppi di sincronizzazione e pertanto continuano a cambiare?](#sync-circ)

### <a name="sync-fails"></a>La sincronizzazione non viene eseguita correttamente nell'interfaccia utente del portale per i database locali associati all'agente client

La sincronizzazione non viene eseguita correttamente nell'interfaccia utente del portale di sincronizzazione dati SQL per i database locali associati all'agente client. Nel computer locale che esegue l'agente vengono visualizzati errori System.IO.IOException nel log eventi. Gli errori indicano che lo spazio su disco non è sufficiente.

- **Causa**. L'unità dispone di spazio sufficiente.

- **Risoluzione**. Creare più spazio nell'unità in cui si trova la directory %TEMP%.

### <a name="sync-stuck"></a> Il gruppo di sincronizzazione è bloccato nello stato di elaborazione

Un gruppo di sincronizzazione in sincronizzazione dati SQL è rimasto nello stato di elaborazione per un lungo periodo. Non risponde al comando di **arresto** e il log non indica voci nuove.

Una delle condizioni seguenti può causare il blocco di un gruppo di sincronizzazione nello stato di elaborazione:

- **Causa**. L'agente client è offline

- **Risoluzione**. Assicurarsi che l'agente client sia online, quindi riprovare.

- **Causa**. L'agente client è disinstallato o mancante.

- **Risoluzione**. Se l'agente client è disinstallato o comunque mancante:

    1. Se esiste, rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL.
    1. Installare l'agente in un computer locale che può essere lo stesso o un altro computer. Inviare quindi la chiave dell'agente generata nel portale per l'agente che viene visualizzato come offline.

- **Causa**. Il servizio di sincronizzazione dati SQL è stato arrestato.

- **Risoluzione**. Riavviare il servizio di sincronizzazione dati SQL.

    1. Nel menu **Start** cercare **Servizi**.
    1. Nei risultati della ricerca fare clic su **Servizi**.
    1. Trovare il servizio di **sincronizzazione dati SQL**.
    1. Se lo stato del servizio è **Arrestato**, fare clic con il pulsante destro del mouse sul nome del servizio e scegliere **Avvia**.

> [!NOTE]
> Se queste informazioni non hanno consentito di cambiare lo stato di elaborazione del gruppo di sincronizzazione, il supporto tecnico Microsoft può reimpostare lo stato del gruppo di sincronizzazione. Per fare in modo che lo stato del gruppo di sincronizzazione venga reimpostato, creare un post nel [forum del database SQL di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted). Nel post includere l'ID sottoscrizione e l'ID gruppo di sincronizzazione per il gruppo da reimpostare. Un tecnico del supporto tecnico Microsoft risponderà al post e comunicherà quando lo stato sarà stato reimpostato.

### <a name="sync-baddata"></a> Vengono visualizzati dati non corretti nelle tabelle

Se in una sincronizzazione vengono incluse tabelle con lo stesso nome, ma che provengono da schemi di database diversi, dopo la sincronizzazione sono presenti dati errati nelle tabelle.

- **Causa**. Il processo di provisioning di sincronizzazione dati SQL usa le stesse tabelle di rilevamento per le tabelle con lo stesso nome, ma in schemi diversi. Per questo motivo, le modifiche in entrambe le tabelle vengono applicate nella stessa tabella di rilevamento. Questa condizione causa errate modifiche ai dati durante la sincronizzazione.

- **Risoluzione**. Assicurarsi che i nomi delle tabelle interessate dalla sincronizzazione siano diversi, anche se appartengono a schemi diversi di un database.

### <a name="sync-pkdata"></a> Vengono visualizzati dati della chiave primaria incoerenti dopo una sincronizzazione riuscita

Dopo una sincronizzazione segnalata come riuscita e nonostante il log non indichi righe non riuscite o ignorate, si osserva tuttavia che i dati della chiave primaria sono incoerenti tra i database del gruppo di sincronizzazione.

- **Causa**. Si tratta di un comportamento previsto da progettazione. Le modifiche apportate a una colonna chiave primaria restituiscono dati incoerenti nelle righe in cui la chiave primaria è stata modificata.

- **Risoluzione**. Per evitare questo problema, assicurarsi che non vengano modificati dati in una colonna chiave primaria. Per risolvere questo problema dopo che si è verificato, eliminare la riga contenente i dati incoerenti da tutti gli endpoint del gruppo di sincronizzazione. Reinserire quindi la riga.

### <a name="sync-perf"></a> Si osserva una riduzione delle prestazioni considerevole

Le prestazioni vengono considerevolmente ridotte, anche al punto da non poter aprire l'interfaccia utente di sincronizzazione dati.

- **Causa**. La causa più probabile è un ciclo di sincronizzazione. Un ciclo di sincronizzazione si verifica quando una sincronizzazione per il gruppo di sincronizzazione A attiva una sincronizzazione per il gruppo di sincronizzazione B, che attiva quindi una sincronizzazione per il gruppo di sincronizzazione A. La situazione effettiva potrebbe essere più complessa e coinvolgere più di due gruppi di sincronizzazione nel ciclo. Il problema è che si è verificata un'attivazione circolare della sincronizzazione dovuta a gruppi di sincronizzazione in sovrapposizione.

- **Risoluzione**. La correzione migliore è prevenire questa situazione. Verificare che non siano presenti riferimenti circolari nei gruppi di sincronizzazione. Le righe sincronizzate da un gruppo di sincronizzazione non possono essere sincronizzate da un altro gruppo di sincronizzazione.

### <a name="sync-nulls"></a> Viene visualizzato il messaggio: "Impossibile inserire il valore NULL nella colonna \<colonna >. La colonna non ammette valori Null." Cosa significa e come è possibile correggere l'errore? 
Questo messaggio di errore indica che si è verificato uno dei due problemi seguenti:
-  Una tabella non dispone di una chiave primaria. Per risolvere il problema, aggiungere una chiave primaria a tutte le tabelle da sincronizzare.
-  Potrebbe essere presente una clausola WHERE nell'istruzione CREATE INDEX. La sincronizzazione dati non gestisce questa condizione. Per risolvere il problema, rimuovere la clausola WHERE o apportare manualmente le modifiche a tutti i database. 
 
### <a name="sync-circ"></a> In che modo la sincronizzazione dati gestisce i riferimenti circolari? In altre parole, quando vengono sincronizzati gli stessi dati in più gruppi di sincronizzazione e pertanto continuano a cambiare?
La sincronizzazione dati non gestisce i riferimenti circolari. Si consiglia di evitarli. 

## <a name="client-agent-issues"></a>Problemi relativi all'agente client

Per risolvere i problemi con l'agente client, vedere [Troubleshoot Data Sync Agent issues](sql-database-data-sync-agent.md#agent-tshoot) (Risolvere i problemi relativi all'agente di sincronizzazione dati).

## <a name="setup-and-maintenance-issues"></a>Problemi relativi all'installazione e alla manutenzione

- [Viene visualizzato un messaggio di spazio su disco non sufficiente](#setup-space)

- [Non è possibile eliminare il gruppo di sincronizzazione](#setup-delete)

- [Non è possibile annullare la registrazione di un database SQL Server locale](#setup-unreg)

- [Non si hanno privilegi sufficienti per avviare i servizi di sistema](#setup-perms)

- [Un database ha lo stato "Non aggiornato"](#setup-date)

- [Un gruppo di sincronizzazione ha lo stato "Non aggiornato"](#setup-date2)

- [Un gruppo di sincronizzazione non può essere eliminato entro tre minuti dalla disinstallazione o dall'arresto dell'agente](#setup-delete2)

- [Cosa accade quando si ripristina un database perso o danneggiato?](#setup-restore)

### <a name="setup-space"></a> Viene visualizzato un messaggio di spazio su disco non sufficiente

- **Causa**. Il messaggio di spazio su disco non sufficiente può essere visualizzato quando rimangono ancora file da eliminare. Questa condizione può verificarsi a causa del software antivirus o perché i file sono aperti quando si prova a eseguire le operazioni di eliminazione.

- **Risoluzione**. Eliminare manualmente i file di sincronizzazione nella cartella %temp% (`del \*sync\* /s`). Eliminare quindi le sottodirectory nella cartella %temp%.

> [!IMPORTANT]
> Attendere che la sincronizzazione venga completata prima di eliminare qualsiasi file.

### <a name="setup-delete"></a> Non è possibile eliminare il gruppo di sincronizzazione

Non si riesce a eliminare un gruppo di sincronizzazione. Gli scenari seguenti possono impedire l'eliminazione di un gruppo di sincronizzazione.

- **Causa**. L'agente client è offline.

- **Risoluzione**. Assicurarsi che l'agente client sia online, quindi riprovare.

- **Causa**. L'agente client è disinstallato o mancante.

- **Risoluzione**. Se l'agente client è disinstallato o comunque mancante:  
    a. Se esiste, rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL.  
    b. Installare l'agente in un computer locale che può essere lo stesso o un altro computer. Inviare quindi la chiave dell'agente generata nel portale per l'agente che viene visualizzato come offline.

- **Causa**. Un database è offline.

- **Risoluzione**. Assicurarsi che i database SQL e i database SQL Server siano tutti online.

- **Causa**. È in corso il provisioning o la sincronizzazione del gruppo di sincronizzazione.

- **Risoluzione**. Attendere il termine del processo di provisioning o di sincronizzazione, quindi riprovare a eliminare il gruppo di sincronizzazione.

### <a name="setup-unreg"></a> Non è possibile annullare la registrazione di un database SQL Server locale

- **Causa**. Molto probabilmente si sta provando ad annullare la registrazione di un database che è già stato eliminato.

- **Risoluzione**. Per annullare la registrazione di un database SQL Server locale, selezionare il database e quindi **Forza eliminazione**.

  Se questa operazione non riesce a rimuovere il database dal gruppo di sincronizzazione:

  1. Arrestare e quindi riavviare il servizio host dell'agente client.  
    a. Selezionare il menu **Start**.  
    b. Immettere **services.msc** nella casella di ricerca.  
    c. Nella sezione **Programmi** del riquadro dei risultati della ricerca fare doppio clic su **Servizi**.  
    d. Fare clic con il pulsante destro del mouse sul servizio di **sincronizzazione dati SQL**.  
    e. Se il servizio è in esecuzione, arrestarlo.  
    f. Fare clic con il pulsante destro del mouse sulservizio e quindi selezionare **Avvia**.  
    g. Controllare se il database è ancora registrato. Se non è più registrato, l'operazione è stata completata. In caso contrario, procedere al passaggio successivo.
  1. Aprire l'app dell'agente client (SqlAzureDataSyncAgent).
  1. Selezionare **Modifica credenziali**, quindi immettere le credenziali per il database.
  1. Procedere con l'annullamento della registrazione.

### <a name="setup-perms"></a> Non si hanno privilegi sufficienti per avviare i servizi di sistema

- **Causa**. Questo errore si verifica in due situazioni:
  -   Il nome utente e/o la password non è corretta.
  -   L'account utente specificato non ha privilegi sufficienti per accedere come servizio.

- **Risoluzione**. Concedere all'account utente le credenziali di accesso come servizio.

  1. Passare a **Start** > **Pannello di controllo** > **Strumenti di amministrazione** > **Criteri di sicurezza locali**  >  **Criteri locali** > **Assegnazione diritti utente**.
  1. Selezionare **Accesso come servizio**.
  1. Nella finestra di dialogo **Proprietà** aggiungere l'account utente.
  1. Selezionare **Apply** (Applica) e quindi **OK**.
  1. Chiudere tutte le finestre.

### <a name="setup-date"></a> Un database ha lo stato "Non aggiornato"

- **Causa**. La sincronizzazione dati SQL rimuove dal servizio i database che sono stati offline per 45 o più giorni, calcolati dal momento in cui il database è stato portato offline. Se un database è offline per 45 o più giorni e quindi torna online, lo stato viene è **Non aggiornato**.

- **Risoluzione**. Per evitare lo stato **Non aggiornato**, assicurarsi che nessuno dei database rimanga offline per 45 o più giorni.

  Se lo stato del database è **Non aggiornato**:

  1. Rimuovere il database con lo stato **Non aggiornato** dal gruppo di sincronizzazione.
  1. Aggiungere di nuovo il database al gruppo di sincronizzazione.

  > [!WARNING]
  > Si perdono tutte le modifiche apportate al database mentre era offline.

### <a name="setup-date2"></a> Un gruppo di sincronizzazione ha lo stato "Non aggiornato"

- **Causa**. Se non è possibile applicare una o più modifiche per l'intero periodo di conservazione di 45 giorni, un gruppo di sincronizzazione può diventare obsoleto.

- **Risoluzione**. Per evitare lo stato **Non aggiornato** per un gruppo di sincronizzazione, esaminare i risultati dei processi di sincronizzazione nel visualizzatore cronologia con regolarità. Analizzare e risolvere eventuali modifiche non applicate.

  Se lo stato di un gruppo di sincronizzazione è **Non aggiornato**, è necessario eliminare il gruppo di sincronizzazione e quindi ricrearlo.

### <a name="setup-delete2"></a> Un gruppo di sincronizzazione non può essere eliminato entro tre minuti dalla disinstallazione o dall'arresto dell'agente

Non è possibile eliminare un gruppo di sincronizzazione entro tre minuti dalla disinstallazione o dall'arresto dell'agente client di sincronizzazione dati SQL associato.

- **Risoluzione**.

  1. Rimuovere un gruppo di sincronizzazione mentre gli agenti di sincronizzazione associati sono online (consigliato).
  1. Se l'agente è offline, ma è installato, portarlo online nel computer locale. Attendere che lo stato dell'agente venga visualizzato come **online** nel portale di sincronizzazione dati SQL. Rimuovere quindi il gruppo di sincronizzazione.
  1. Se l'agente è offline perché è stato disinstallato:  
    a.  Se esiste, rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL.  
    b.  Installare l'agente in un computer locale che può essere lo stesso o un altro computer. Inviare quindi la chiave dell'agente generata nel portale per l'agente che viene visualizzato come offline.  
    c. Provare a eliminare il gruppo di sincronizzazione.

### <a name="setup-restore"></a> Cosa accade quando si ripristina un database perso o danneggiato?

Se si ripristina un database perso o danneggiato da un backup, potrebbe verificarsi la non convergenza dei dati nei gruppi di sincronizzazione a cui appartiene il database.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   Panoramica: [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   Configurare la sincronizzazione dati
    - Nel portale - [Esercitazione: Configurare la sincronizzazione dati SQL per sincronizzare i dati tra il database SQL di Azure e SQL Server in locale](sql-database-get-started-sql-data-sync.md)
    - Con PowerShell
        -  [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente di sincronizzazione dei dati: [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure](sql-database-data-sync-agent.md)
-   Procedure consigliate: [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)
-   Monitoraggio: [Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure](sql-database-sync-monitor-oms.md)
-   Aggiornare lo schema di sincronizzazione
    -   Con Transact-SQL: [Automatizzare la replica delle modifiche dello schema nella sincronizzazione dati SQL di Azure](sql-database-update-sync-schema.md)
    -   Con PowerShell: [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](scripts/sql-database-sync-update-schema.md)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
