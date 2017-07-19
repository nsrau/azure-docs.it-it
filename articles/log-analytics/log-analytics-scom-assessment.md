---
title: Ottimizzare l&quot;ambiente System Center Operations Manager con Azure Log Analytics |Microsoft Docs
description: "È possibile usare la soluzione System Center Operations Manager Assessment per valutare i rischi e l&quot;integrità degli ambienti server a intervalli regolari."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 6754505e7f58a8e7305987db9204271ca9b93870
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017


---

# <a name="optimize-your-environment-with-the-system-center-operations-manager-assessment-preview-solution"></a>Ottimizzare l'ambiente con la soluzione System Center Operations Manager Assessment (Anteprima)

![Simbolo di Valutazione System Center Operations Manager](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

È possibile usare la soluzione System Center Operations Manager Assessment per valutare i rischi e l'integrità degli ambienti server di System Center Operations Manager a intervalli regolari. Questo articolo consente di installare, configurare e usare la soluzione in modo che si possano intraprendere azioni correttive per problemi potenziali.

La soluzione offre un elenco con priorità di raccomandazioni specifiche per l'infrastruttura distribuita dei server, classificate in quattro aree di interesse che consentono di comprendere rapidamente il rischio e agire in maniera appropriata.

Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft in base a migliaia di visite dei clienti. Ogni raccomandazione offre informazioni aggiuntive sui motivi per cui un problema può essere rilevante per l'utente e su come implementare le modifiche suggerite.

Si possono scegliere le aree di interesse più importanti per l'organizzazione e tenere traccia dello stato di avanzamento verso la realizzazione di un ambiente integro ed esente da rischi.

Dopo aver aggiunto la soluzione e completato una valutazione, nel dashboard di **System Center Operations Manager Assessment** per l'infrastruttura verranno visualizzate le informazioni di riepilogo per le aree di interesse. Le sezioni seguenti descrivono come usare le informazioni visualizzate nel dashboard di **System Center Operations Manager Assessment** per intraprendere le azioni consigliate per l'infrastruttura SCOM.

![Riquadro della soluzione System Center Operations Manager](./media/log-analytics-scom-assessment/scom-tile.png)

![Dashboard di System Center Operations Manager Assessment](./media/log-analytics-scom-assessment/scom-dashboard01.png)

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione

La soluzione funziona con Microsoft System Operations Manager 2012 R2 e 2012 SP1.

Usare le informazioni seguenti per installare e configurare la soluzione.

 - Prima di poter usare una soluzione di valutazione in OMS, è necessario averla installata. Installare la soluzione da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview) o seguendo le istruzioni indicate in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).

 - Dopo aver aggiunto la soluzione all'area di lavoro, un messaggio nel riquadro di System Center Operations Manager Assessment del dashboard richiede la configurazione aggiuntiva. Fare clic sul riquadro e attenersi alla procedura di configurazione riportata nella pagina

 ![Riquadro del dashboard di System Center Operations Manager](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

 La configurazione di System Center Operations Manager può essere eseguita tramite lo script seguendo i passaggi illustrati nella pagina di configurazione della soluzione in OMS.

 Per configurare la valutazione tramite SCOM Console, eseguire invece i passaggi seguenti nello stesso ordine
1. [Impostare l'account RunAs per System Center Operations Manager Assessment](#operations-manager-run-as-accounts-for-oms)  
2. [Configurare la regola di System Center Operations Manager Assessment](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>Dettagli della raccolta di dati della valutazione di System Center Operations Manager

La valutazione di System Center Operations Manager raccoglie i dati WMI, del Registro di sistema, del log eventi e di Operations Manager tramite Windows PowerShell, query SQL e agente di raccolta di informazioni file usando il server che è stato abilitato.

La tabella seguente illustra i metodi di raccolta dati per System Center Operations Manager Assessment e la frequenza con cui i dati vengono raccolti da un agente.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  ![No](./media/log-analytics-scom-assessment/oms-bullet-red.png) | ![No](./media/log-analytics-scom-assessment/oms-bullet-red.png)  | ![No](./media/log-analytics-scom-assessment/oms-bullet-red.png)  |  ![Sì](./media/log-analytics-scom-assessment/oms-bullet-green.png) | ![No](./media/log-analytics-scom-assessment/oms-bullet-red.png)  | 7 giorni |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Account RunAs di Operations Manager per OMS

OMS si basa su Management Pack per i carichi di lavoro per offrire servizi a valore aggiunto. Ogni carico di lavoro richiede privilegi specifici per eseguire i Management Pack in un contesto di sicurezza diverso, ad esempio un account di dominio. Configurare un account RunAs di Operations Manager per specificare le informazioni sulle credenziali.

Usare le informazioni seguenti per impostare l'account RunAs di Operations Manager per System Center Operations Manager Assessment.

### <a name="set-the-run-as-account"></a>Impostare l'account RunAs

1. Nella console di Operations Manager passare alla scheda **Amministrazione**.
2. In **Run As Configuration** (Configurazione RunAs) fare clic su **Account**.
3. Creare l'account RunAs seguendo la procedura guidata per la creazione di un account di Windows. L'account da usare è quello identificato e avente tutti i prerequisiti seguenti:

    >[!NOTE]
    L'account RunAs deve soddisfare i requisiti seguenti:
    - Essere membro dell'account di dominio del gruppo di amministratori locale in tutti i server dell'ambiente (tutti i ruoli di Operations Manager: server di gestione, database OpsMgr, data warehouse, creazione di report, console Web, gateway)
    - Appartenere a un ruolo di amministratore di Operations Manager per il gruppo di gestione valutato
    - Eseguire lo [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) per concedere autorizzazioni dettagliate all'account nell'istanza SQL usata da Operations Manager.
      Nota: se questo account dispone già di diritti di amministratore di sistema, ignorare l'esecuzione dello script.

4. In **Sicurezza della distribuzione** selezionare **Più protetto**.
5. Specificare il server di gestione in cui viene distribuito l'account.
3. Tornare alla configurazione dell'account RunAs e fare clic su **Profili**.
4. Cercare il profilo *SCOM Assessment*.
5. Il nome del profilo è *Microsoft System Center Advisor SCOM Assessment Run As Profile*.
6. Fare clic con il pulsante destro del mouse, aggiornare le proprietà e aggiungere l'account RunAs creato nel passaggio 3.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script SQL per la concessione di autorizzazioni dettagliate all'account RunAs

Eseguire questo script SQL per concedere le autorizzazioni necessarie all'account RunAs nell'istanza SQL usata da Operations Manager.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```


### <a name="configure-the-assessment-rule"></a>Configurare la regola di valutazione

Il Management Pack della soluzione System Center Operations Manager Assessment include una regola denominata *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule*. Questa regola è responsabile dell'esecuzione della valutazione. Per abilitare la regola e configurare la frequenza, seguire queste procedure.

Per impostazione predefinita, la regola Microsoft System Center Advisor SCOM Assessment Run Assessment Rule è disabilitata. Per eseguire la valutazione è necessario abilitare la regola in un server di gestione. Seguire questa procedura.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Abilitare la regola per un server di gestione specifico

1. Nell'area di lavoro **Creazione e modifica** della console di Operations Manager, cercare la regola *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* nel pannello **Regole**.
2. Nei risultati della ricerca selezionare quello che include il testo *Tipo: Server di gestione*.
3. Fare clic con il pulsante destro del mouse sulla regola, quindi scegliere **Override** > **Oggetto specifico della classe: Server di gestione**.
4.  Nell'elenco dei server di gestione disponibili selezionare il server di gestione in cui eseguire la regola.
5.  Assicurarsi di modificare il valore di override in **True** per il valore del parametro **Enabled**.  
    ![parametro di override](./media/log-analytics-scom-assessment/rule.png)

Sempre in questa finestra, configurare la frequenza di esecuzione usando la procedura successiva.

#### <a name="configure-the-run-frequency"></a>Configurare la frequenza di esecuzione

La valutazione è configurata per l'esecuzione ogni 10.080 minuti (o sette giorni), l'intervallo predefinito. È possibile sostituire il valore con un valore minimo di 1440 minuti (o un giorno). Il valore rappresenta l'intervallo minimo tra le esecuzioni della valutazione. Per sostituire l'intervallo, usare la procedura seguente.

1. Nell'area di lavoro **Creazione e modifica** della console di Operations Manager, cercare la regola *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* nel pannello **Regole**.
2. Nei risultati della ricerca selezionare quello che include il testo *Tipo: Server di gestione*.
3. Fare clic con il pulsante destro del mouse sulla regola, quindi scegliere **Sostituisci regola** > **Tutti gli oggetti della classe: Server di gestione**.
4. Impostare il parametro **Interval** (Intervallo) sul valore desiderato. Nell'esempio seguente, il valore è impostato su 1440 minuti (un giorno).  
    ![parametro interval](./media/log-analytics-scom-assessment/interval.png)  
    Se il valore impostato è minore di 1440 minuti, la regola verrà eseguita a intervalli di un giorno. In questo esempio, la regola ignora il valore dell'intervallo e viene eseguita con una frequenza di un giorno.


## <a name="understanding-how-recommendations-are-prioritized"></a>Informazioni sulla classificazione in ordine di priorità delle raccomandazioni

A ogni raccomandazione generata viene assegnato un valore di ponderazione che identifica l'importanza relativa della raccomandazione. Vengono visualizzate solo le 10 raccomandazioni più importanti.

### <a name="how-weights-are-calculated"></a>Come vengono calcolate le ponderazioni

Le ponderazioni sono valori aggregati che si basano su tre fattori chiave:

- La *probabilità* che un problema identificato causi inconvenienti. Una probabilità più elevata equivale a un punteggio complessivamente maggiore per la raccomandazione.
- L' *impatto* del problema per l'organizzazione se causa effettivamente un problema. Un impatto più elevato equivale a un punteggio complessivamente maggiore per la raccomandazione.
- Il *lavoro* richiesto per implementare la raccomandazione. Un lavoro richiesto più elevato equivale a un punteggio complessivamente inferiore per la raccomandazione.

La ponderazione per ogni raccomandazione è espressa come percentuale del punteggio totale disponibile per ogni area di interesse. Se ad esempio una raccomandazione nell'area di interesse Disponibilità e continuità aziendale ha un punteggio pari al 5%, l'implementazione della raccomandazione aumenterà del 5% il punteggio complessivo di quell'area.

### <a name="focus-areas"></a>Aree di interesse

**Disponibilità e continuità aziendale** : quest'area di interesse descrive raccomandazioni relative alla disponibilità del servizio, alla resilienza dell'infrastruttura e alla protezione aziendale.

**Prestazioni e scalabilità** : quest'area di interesse descrive raccomandazioni utili per favorire la crescita dell'infrastruttura IT dell'organizzazione, assicurando che l'ambiente IT soddisfi gli attuali requisiti in termini di prestazioni e possa rispondere alle mutevoli esigenze dell'infrastruttura.

**Aggiornamento, migrazione e distribuzione**: quest'area di interesse mostra le raccomandazioni utili per aggiornare, eseguire la migrazione e distribuire SQL Server nell'infrastruttura esistente.

**Operazioni e monitoraggio**: quest'area di interesse mostra le raccomandazioni utili per semplificare le operazioni IT, implementare la manutenzione preventiva e ottimizzare le prestazioni.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>È consigliabile mirare a ottenere un punteggio del 100% in tutte le aree di interesse?

Non necessariamente. Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft attraverso migliaia di visite dei clienti. Non esistono tuttavia due infrastrutture di server uguali e raccomandazioni specifiche possono essere più o meno pertinenti per l'utente. Ad esempio, alcune raccomandazioni sulla sicurezza possono risultare meno pertinenti se le macchine virtuali non sono esposte a Internet. Alcune raccomandazioni sulla disponibilità possono risultare meno pertinenti per i servizi che forniscono creazione di report e raccolta dei dati ad hoc a bassa priorità. I problemi che possono essere importanti per un'azienda collaudata possono esserlo meno per una start-up. È consigliabile identificare quali sono le proprie aree di interesse prioritarie e quindi osservare il cambiamento dei punteggi nel tempo.

Ogni raccomandazione include informazioni aggiuntive sui motivi per cui potrebbe essere importante. Usare queste informazioni aggiuntive per valutare se l'implementazione della raccomandazione è appropriata, a seconda della natura dei servizi IT e delle esigenze aziendali dell'organizzazione.

## <a name="use-assessment-focus-area-recommendations"></a>Usare le raccomandazioni relative all'area di interesse della valutazione

Prima di poter usare una soluzione di valutazione in OMS, è necessario averla installata. Per altre informazioni sull'installazione di soluzioni, vedere [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). Dopo l'installazione, sarà possibile visualizzare il riepilogo delle raccomandazioni usando il riquadro di System Center Operations Manager Assessment nella pagina Panoramica in OMS.

Visualizzare il riepilogo delle valutazioni relative alla conformità per l'infrastruttura, quindi visualizzare le raccomandazioni nel dettaglio.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Per visualizzare le raccomandazioni per un'area di interesse e applicare un'azione correttiva

1. Nella pagina **Panoramica** fare clic sul riquadro **System Center Operations Manager Assessment**.
2. Nella pagina **System Center Operations Manager Assessment** verificare le informazioni di riepilogo in uno dei pannelli dell'area di interesse, quindi fare clic su un'area specifica per visualizzare le raccomandazioni corrispondenti.
3. In una delle pagine relative alle aree di interesse è possibile visualizzare le raccomandazioni relative all'ambiente specifico, classificate in ordine di priorità. Fare clic su una raccomandazione in **Affected Objects** (Oggetti interessati) per visualizzare i dettagli relativi al motivo per cui è stata generata.  
    ![area di interesse](./media/log-analytics-scom-assessment/focus-area.png)
4. È possibile eseguire le azioni correttive suggerite in **Suggested Actions**(Azioni suggerite). Dopo la risoluzione dell'elemento, le valutazioni successive indicheranno che le azioni consigliate sono state effettuate e il punteggio relativo alla conformità aumenterà. Gli elementi corretti vengono visualizzati come **Passed Objects**.

## <a name="ignore-recommendations"></a>Ignorare le raccomandazioni

Per ignorare delle raccomandazioni è possibile creare un file di testo che OMS userà per impedirne la visualizzazione nei risultati della valutazione.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Per identificare le raccomandazioni da ignorare

1. Accedere all'area di lavoro e aprire Ricerca log. Usare la query seguente per elencare le raccomandazioni non riuscite per i computer nell'ambiente.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Ecco lo screenshot che mostra la query di ricerca nei log:   
    ![ricerca log](./media/log-analytics-scom-assessment/scom-log-search.png)

2. Scegliere le raccomandazioni da ignorare. Nella procedura successiva verranno usati i valori per ID raccomandazione.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Per creare e usare un file di testo IgnoreRecommendations.txt

1. Creare un file denominato IgnoreRecommendations.txt.
2. Incollare o digitare ogni ID raccomandazione per ogni raccomandazione che OMS dovrà ignorare in una riga separata e quindi salvare e chiudere il file.
3. Inserire il file nella cartella seguente in ogni computer in cui si vuole che OMS ignori le raccomandazioni.
4. Nel server di gestione di Operations Manager *SystemDrive*:\Programmi\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Per verificare che le raccomandazioni vengano ignorate

1. Dopo l'esecuzione della valutazione successiva pianificata, per impostazione predefinita ogni sette giorni, le raccomandazioni specificate vengono contrassegnate come ignorate e non verranno visualizzate nel dashboard di valutazione.
2. È possibile usare le query di Ricerca log seguenti per elencare tutte le raccomandazioni ignorate.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

3. Se in seguito si decide che si vogliono vedere le raccomandazioni ignorate, rimuovere eventuali file IgnoreRecommendations.txt oppure è possibile rimuovere gli ID raccomandazione dagli stessi.

## <a name="system-center-operations-manager-assessment-solution-faq"></a>Domande frequenti sulla soluzione System Center Operations Manager Assessment

*È stata aggiunta la soluzione di valutazione all'area di lavoro di OMS. Non vengono tuttavia visualizzate le raccomandazioni. Perché?* Dopo aver aggiunto la soluzione, procedere come segue per visualizzare le raccomandazioni nel dashboard OMS.  

- [Impostare l'account RunAs per System Center Operations Manager Assessment](#operations-manager-run-as-accounts-for-oms)  
- [Configurare la regola di System Center Operations Manager Assessment](#configure-the-assessment-rule)


*È possibile configurare la frequenza di esecuzione della valutazione?* Sì. Vedere [Configurare la frequenza di esecuzione](#configure-the-run-frequency).

*Se viene rilevato un altro server dopo l'aggiunta della soluzione System Center Operations Manager Assessment, il server verrà valutato?* Sì, verrà valutato dal momento della rilevazione, per impostazione predefinita ogni sette giorni.

*Qual è il nome del processo che esegue la raccolta di dati?* AdvisorAssessment.exe

*Dove viene eseguito il processo AdvisorAssessment.exe?* AdvisorAssessment.exe viene eseguito nel servizio di integrità del server di gestione in cui è abilitata la regola di valutazione. Con questo processo, l'individuazione dell'intero ambiente avviene tramite la raccolta di dati remoti.

*Quanto tempo occorre per la raccolta di dati?* La raccolta di dati sul server richiede circa un'ora. Potrebbe richiedere più tempo in ambienti con molte istanze o database di Operations Manager.

*Cosa accade se l'intervallo di valutazione viene impostato su un valore inferiore a 1440 minuti?* La valutazione è preconfigurata per essere eseguita al massimo una volta al giorno. Se si sostituisce il valore dell'intervallo con un valore inferiore a 1440 minuti, la valutazione userà 1440 minuti come valore dell'intervallo.

*Come è possibile verificare se sono presenti errori relativi ai prerequisiti?* Se è stata eseguita la valutazione e non vengono visualizzati risultati, è probabile che si siano verificati errori per alcuni dei prerequisiti della valutazione. È possibile eseguire le query: `Type=Operation Solution=SCOMAssessment` e `Type=SCOMAssessmentRecommendation FocusArea=Prerequisites` in Ricerca log per visualizzare i prerequisiti con errori.

*È presente un messaggio `Failed to connect to the SQL Instance (….).` negli errori relativi ai prerequisiti. Qual è il problema?* AdvisorAssessment.exe, il processo che raccoglie i dati, viene eseguito nel servizio di integrità del server di gestione. Nell'ambito della valutazione, il processo prova a connettersi al server SQL in cui è presente il database di Operations Manager. Questo errore può verificarsi quando le regole del firewall bloccano la connessione all'istanza di SQL Server.

*Quali tipi di dati vengono raccolti?* Vengono raccolti i tipi di dati seguenti: dati WMI, del Registro di sistema, del log eventi e di Operations Manager tramite Windows PowerShell, query SQL e agente di raccolta di informazioni file.

*Perché è necessario configurare un account RunAs?* Per un server di Operations Manager vengono eseguite diverse query SQL. Per consentirne l'esecuzione è necessario usare un account RunAs con le necessarie autorizzazioni. Sono anche necessarie credenziali di amministratore locale per eseguire query su WMI.

*Perché vengono visualizzate solo le prime 10 raccomandazioni?* Invece di esaminare un lunghissimo elenco completo di attività, è consigliabile concentrare l'attenzione sulle raccomandazioni con priorità maggiore. Dopo la verifica delle raccomandazioni principali, verranno rese disponibili raccomandazioni aggiuntive. Se si preferisce visualizzare l'elenco dettagliato, usare Ricerca log per mostrare tutte le raccomandazioni.

*È possibile ignorare una raccomandazione?* Sì, vedere [Ignorare le raccomandazioni](#Ignore-recommendations).


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare raccomandazioni e dati dettagliati di System Center Operations Manager Assessment.

