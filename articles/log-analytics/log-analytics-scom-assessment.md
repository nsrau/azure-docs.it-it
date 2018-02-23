---
title: Ottimizzare l'ambiente System Center Operations Manager con Azure Log Analytics |Microsoft Docs
description: "È possibile usare la soluzione Controllo integrità System Center Operations Manager per valutare i rischi e l'integrità degli ambienti a intervalli regolari."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86484ca2bc7dc14035f48b8f7b1514a4fc471b74
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Ottimizzare l'ambiente con la soluzione Controllo integrità System Center Operations Manager (Anteprima)

![Simbolo di Controllo integrità System Center Operations Manager](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

È possibile usare la soluzione Controllo integrità System Center Operations Manager per valutare i rischi e l'integrità del gruppo di gestione di System Center Operations Manager a intervalli regolari. Questo articolo consente di installare, configurare e usare la soluzione in modo che si possano intraprendere azioni correttive per problemi potenziali.

La soluzione offre un elenco con priorità di raccomandazioni specifiche per l'infrastruttura distribuita dei server, classificate in quattro aree di interesse che consentono di comprendere rapidamente il rischio e agire in maniera appropriata.

Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft in base a migliaia di visite dei clienti. Ogni raccomandazione offre informazioni aggiuntive sui motivi per cui un problema può essere rilevante per l'utente e su come implementare le modifiche suggerite.

Si possono scegliere le aree di interesse più importanti per l'organizzazione e tenere traccia dello stato di avanzamento verso la realizzazione di un ambiente integro ed esente da rischi.

Dopo aver aggiunto la soluzione e aver eseguito una valutazione, nel dashboard di **Controllo integrità System Center Operations Manager** per l'infrastruttura vengono visualizzate le informazioni di riepilogo per le aree di interesse. Le sezioni seguenti descrivono come usare le informazioni visualizzate nel dashboard di **Controllo integrità System Center Operations Manager** per intraprendere le azioni consigliate per l'ambiente Operations Manager.

![Riquadro della soluzione System Center Operations Manager](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Dashboard di Controllo integrità System Center Operations Manager](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione

La soluzione funziona con Microsoft System Operations Manager 2012 Service Pack (SP) 1 e 2012 R2.

Usare le informazioni seguenti per installare e configurare la soluzione.

 - Prima di poter usare la soluzione Controllo integrità in Log Analytics, è necessario averla installata. Installare la soluzione da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - Dopo avere aggiunto la soluzione all'area di lavoro, nel riquadro **Controllo integrità System Center Operations Manager** del dashboard viene visualizzato un altro messaggio di richiesta della configurazione. Fare clic sul riquadro e attenersi alla procedura di configurazione riportata nella pagina

 ![Riquadro del dashboard di System Center Operations Manager](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Per eseguire la configurazione di System Center Operations Manager, è possibile usare lo script seguendo i passaggi illustrati nella pagina di configurazione della soluzione in Log Analytics.

 Per configurare la valutazione tramite la console operatore di Operations Manager Operations, eseguire i passaggi descritti sotto nell'ordine seguente:
1. [Impostare l'account RunAs per Controllo integrità System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
2. [Configurare la regola di Controllo integrità System Center Operations Manager](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>Dettagli della raccolta di dati della valutazione di System Center Operations Manager

Con Valutazione System Center Operations Manager vengono raccolti dati dalle origini seguenti:

* Registro
* Strumentazione gestione Windows (WMI)
* Registro eventi
* Dati dei file
* Direttamente da Operations Manager usando PowerShell e le query SQL, da un server di gestione specificato.  

I dati vengono raccolti nel server di gestione e inoltrati a Log Analytics ogni sette giorni.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Account RunAs di Operations Manager per Log Analytics

Log Analytics si basa sui Management Pack per i carichi di lavoro per offrire servizi a valore aggiunto. Ogni carico di lavoro richiede privilegi specifici per eseguire i Management Pack in un contesto di sicurezza diverso, ad esempio un account utente di dominio. Configurare un account RunAs di Operations Manager con credenziali con privilegi. Per altre informazioni, vedere [Come creare un account RunAs](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) nella documentazione di Operations Manager.

Usare le informazioni seguenti per impostare l'account RunAs di Operations Manager per Controllo integrità System Center Operations Manager.

### <a name="set-the-run-as-account"></a>Impostare l'account RunAs

Prima di procedere l'account RunAs deve soddisfare i requisiti seguenti:

* Essere un account utente di dominio membro del gruppo Administrators locale in tutti i server che supportano qualsiasi ruolo di Operations Manager, ovvero server di gestione, istanza di SQL Server che ospita il data warehouse operativo e il database ACS, Creazione report, Console Web e Server gateway.
* Appartenere a un ruolo di amministratore di Operations Manager per il gruppo di gestione valutato
* Se l'account non ha i diritti di amministratore di sistema SQL, eseguire lo [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) per concedere autorizzazioni granulari all'account in ogni istanza di SQL Server che ospita uno o tutti i database di Operations Manager.

1. Nella console di Operations Manager selezionare il pulsante di spostamento **Amministrazione**.
2. In **Run As Configuration** (Configurazione RunAs) fare clic su **Account**.
3. Nella pagina **Introduzione** della procedura guidata **Crea account RunAs** fare clic su **Avanti**.
4. Nella pagina **Proprietà generali** selezionare **Windows** nell'elenco **Tipo di account RunAs**.
5. Digitare un nome visualizzato nella casella di testo **Nome visualizzato** e facoltativamente una descrizione nella casella **Descrizione** e quindi fare clic su **Avanti**.
6. Nella pagina **Sicurezza della distribuzione** selezionare **Più protetto**.
7. Fare clic su **Crea**.  

Dopo aver creato l'account RunAs, è necessario indicare i server di gestione di destinazione nel gruppo di gestione e associare l'account a un profilo RunAs predefinito in modo che i flussi di lavoro vengano eseguiti usando le credenziali.  

1. In **Run As Configuration** (Configurazione RunAs), **Account** fare doppio clic sull'account creato in precedenza nel riquadro dei risultati.
2. Nella scheda **Distribuzione** fare clic su **Aggiungi**per la casella **Computer selezionati** e aggiungere il server di gestione in cui distribuire l'account.  Fare due volte clic su **OK** per salvare le modifiche.
3. In **Run As Configuration** (Configurazione RunAs) fare clic su **Profili**.
4. Cercare il profilo *SCOM Assessment*.
5. Il nome del profilo è *Microsoft System Center Advisor SCOM Assessment Run As Profile*.
6. Fare clic con il pulsante destro del mouse, aggiornare le proprietà e aggiungere l'account RunAs creato in precedenza.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script SQL per la concessione di autorizzazioni dettagliate all'account RunAs

Eseguire questo script SQL per concedere le autorizzazioni necessarie all'account RunAs nell'istanza di SQL Server usata da Operations Manager che ospita il data warehouse operativo e il database ACS.

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

### <a name="configure-the-health-check-rule"></a>Configurare la regola di controllo integrità

Il Management Pack della soluzione Controllo integrità System Center Operations Manager include una regola denominata *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule*. Questa regola è responsabile del controllo integrità. Per abilitare la regola e configurare la frequenza, seguire queste procedure.

Per impostazione predefinita, la regola Microsoft System Center Advisor SCOM Assessment Run Assessment Rule è disabilitata. Per eseguire il controllo integrità, è necessario abilitare la regola in un server di gestione. Seguire questa procedura.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Abilitare la regola per un server di gestione specifico

1. Nell'area di lavoro **Creazione e modifica** della console operativa di Operations Manager, cercare la regola *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* nel pannello **Regole**.
2. Nei risultati della ricerca selezionare quello che include il testo *Tipo: Server di gestione*.
3. Fare clic con il pulsante destro del mouse sulla regola, quindi scegliere **Override** > **Oggetto specifico della classe: Server di gestione**.
4.  Nell'elenco dei server di gestione disponibili selezionare il server di gestione in cui eseguire la regola.  Deve trattarsi dello stesso server di gestione configurato in precedenza per l'associazione con l'account RunAs.
5.  Assicurarsi di modificare il valore di override in **True** per il valore del parametro **Enabled**.<br><br> ![parametro di override](./media/log-analytics-scom-assessment/rule.png)

    Sempre in questa finestra, configurare la frequenza di esecuzione usando la procedura successiva.

#### <a name="configure-the-run-frequency"></a>Configurare la frequenza di esecuzione

Per impostazione predefinita, la valutazione è configurata per l'esecuzione ogni 10.080 minuti (o sette giorni). È possibile sostituire il valore con un valore minimo di 1440 minuti (o un giorno). Il valore rappresenta l'intervallo minimo tra le esecuzioni della valutazione. Per sostituire l'intervallo, usare la procedura seguente.

1. Nell'area di lavoro **Creazione e modifica** della console di Operations Manager, cercare la regola *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* nella sezione **Regole**.
2. Nei risultati della ricerca selezionare quello che include il testo *Tipo: Server di gestione*.
3. Fare clic con il pulsante destro del mouse sulla regola, quindi scegliere **Sostituisci regola** > **Tutti gli oggetti della classe: Server di gestione**.
4. Impostare il parametro **Interval** (Intervallo) sul valore desiderato. Nell'esempio seguente, il valore è impostato su 1440 minuti (un giorno).<br><br> ![parametro interval](./media/log-analytics-scom-assessment/interval.png)<br>  

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

## <a name="use-health-check-focus-area-recommendations"></a>Usare le raccomandazioni relative alle aree di interesse del controllo integrità

Prima di poter usare una soluzione di controllo integrità in Log Analytics, è necessario averla installata. Per altre informazioni sull'installazione di soluzioni, vedere [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). Dopo l'installazione, è possibile visualizzare il riepilogo delle raccomandazioni usando il riquadro Controllo integrità System Center Operations Manager nella pagina Panoramica del portale di OMS.

Visualizzare il riepilogo delle valutazioni relative alla conformità per l'infrastruttura, quindi visualizzare le raccomandazioni nel dettaglio.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Per visualizzare le raccomandazioni per un'area di interesse e applicare un'azione correttiva
1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
3. Nel riquadro delle sottoscrizioni di Log Analytics selezionare un'area di lavoro e quindi fare clic sul riquadro **Portale di OMS**.  
4. Nella pagina **Panoramica** fare clic sul riquadro **Controllo integrità System Center Operations Manager**.
5. Nella pagina **Controllo integrità System Center Operations Manager** verificare le informazioni di riepilogo in uno dei pannelli dell'area di interesse e quindi fare clic su un'area specifica per visualizzare le raccomandazioni corrispondenti.
6. In una delle pagine relative alle aree di interesse è possibile visualizzare le raccomandazioni relative all'ambiente specifico, classificate in ordine di priorità. Fare clic su una raccomandazione in **Affected Objects** (Oggetti interessati) per visualizzare i dettagli relativi al motivo per cui è stata generata.<br><br> ![area di interesse](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. È possibile eseguire le azioni correttive suggerite in **Suggested Actions**(Azioni suggerite). Dopo la risoluzione dell'elemento, le valutazioni successive indicheranno che le azioni consigliate sono state effettuate e il punteggio relativo alla conformità aumenterà. Gli elementi corretti vengono visualizzati come **Passed Objects**.

## <a name="ignore-recommendations"></a>Ignorare le raccomandazioni

Per ignorare alcune raccomandazioni, è possibile creare un file di testo che Log Analytics usa per impedire la visualizzazione delle raccomandazioni nei risultati della valutazione.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Per identificare le raccomandazioni da ignorare
1. Nella pagina dell'area di lavoro di Log Analytics per l'area di lavoro selezionata del portale di Azure fare clic sul riquadro **Ricerca log**.
2. Usare la query seguente per elencare le raccomandazioni non riuscite per i computer nell'ambiente.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), la query precedente verrà sostituita dalla seguente.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Ecco lo screenshot che mostra la query di ricerca nei log: <br><br> ![ricerca log](./media/log-analytics-scom-assessment/scom-log-search.png)<br>

3. Scegliere le raccomandazioni da ignorare. Nella procedura successiva verranno usati i valori per ID raccomandazione.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Per creare e usare un file di testo IgnoreRecommendations.txt

1. Creare un file denominato IgnoreRecommendations.txt.
2. Incollare o digitare ciascun ID raccomandazione per ogni raccomandazione che Log Analytics dovrà ignorare in una riga separata e quindi salvare e chiudere il file.
3. Inserire il file nella cartella seguente in ogni computer in cui si vuole che Log Analytics ignori le raccomandazioni.
4. Nel server di gestione di Operations Manager *SystemDrive*:\Programmi\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Per verificare che le raccomandazioni vengano ignorate

1. Dopo l'esecuzione della valutazione successiva pianificata, per impostazione predefinita ogni sette giorni, le raccomandazioni specificate vengono contrassegnate come ignorate e non verranno visualizzate nel dashboard di controllo integrità.
2. È possibile usare le query di Ricerca log seguenti per elencare tutte le raccomandazioni ignorate.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), la query precedente verrà sostituita dalla seguente.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Se in seguito si decide che si vogliono vedere le raccomandazioni ignorate, rimuovere eventuali file IgnoreRecommendations.txt oppure rimuovere gli ID raccomandazione dagli stessi.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Domande frequenti sulla soluzione Controllo integrità System Center Operations Manager

*La soluzione Controllo integrità è stata aggiunta all'area di lavoro di Log Analytics. Non vengono tuttavia visualizzate le raccomandazioni. Perché?* Dopo avere aggiunto la soluzione, procedere come segue per visualizzare le raccomandazioni nel dashboard di Log Analytics.  

- [Impostare l'account RunAs per Controllo integrità System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
- [Configurare la regola di Controllo integrità System Center Operations Manager](#configure-the-health-check-rule)


*È possibile configurare la frequenza di esecuzione del controllo?* Sì. Vedere [Configurare la frequenza di esecuzione](#configure-the-run-frequency).

*Se viene individuato un altro server dopo l'aggiunta della soluzione Valutazione System Center Operations Manager, il server verrà controllato?* Sì, per impostazione predefinita verrà controllato ogni sette giorni a partire dal momento dell'individuazione.

*Qual è il nome del processo che esegue la raccolta di dati?* AdvisorAssessment.exe

*Dove viene eseguito il processo AdvisorAssessment.exe?* AdvisorAssessment.exe viene eseguito nel processo HealthService del server di gestione in cui è abilitata la regola di controllo integrità. Con questo processo, l'individuazione dell'intero ambiente avviene tramite la raccolta di dati remoti.

*Quanto tempo occorre per la raccolta di dati?* La raccolta di dati sul server richiede circa un'ora. Potrebbe richiedere più tempo in ambienti con molte istanze o database di Operations Manager.

*Cosa accade se l'intervallo di valutazione viene impostato su un valore inferiore a 1440 minuti?* La valutazione è preconfigurata per essere eseguita al massimo una volta al giorno. Se si sostituisce il valore dell'intervallo con un valore inferiore a 1440 minuti, la valutazione userà 1440 minuti come valore dell'intervallo.

*Come è possibile verificare se sono presenti errori relativi ai prerequisiti?* Se è stato eseguito il controllo integrità e non vengono visualizzati risultati, è probabile che si siano verificati errori per alcuni dei prerequisiti del controllo. Per visualizzare i prerequisiti con errori, è possibile eseguire le query `Operation Solution=SCOMAssessment` e `SCOMAssessmentRecommendation FocusArea=Prerequisites` in Ricerca log.

*È presente un messaggio `Failed to connect to the SQL Instance (….).` negli errori relativi ai prerequisiti. Qual è il problema?* AdvisorAssessment.exe, il processo che raccoglie i dati, viene eseguito nel processo HealthService del server di gestione. Nell'ambito del controllo integrità, il processo prova a connettersi all'istanza di SQL Server in cui è presente il database di Operations Manager. Questo errore può verificarsi quando le regole del firewall bloccano la connessione all'istanza di SQL Server.

*Quali tipi di dati vengono raccolti?* Vengono raccolti i tipi di dati seguenti: dati WMI, del Registro di sistema, del log eventi e di Operations Manager tramite Windows PowerShell, query SQL e agente di raccolta di informazioni file.

*Perché è necessario configurare un account RunAs?* Con Operations Manager vengono eseguite diverse query SQL. Per consentirne l'esecuzione è necessario usare un account RunAs con le necessarie autorizzazioni. Sono anche necessarie credenziali di amministratore locale per eseguire query su WMI.

*Perché vengono visualizzate solo le prime 10 raccomandazioni?* Invece di esaminare un lunghissimo elenco completo di attività, è consigliabile concentrare l'attenzione sulle raccomandazioni con priorità maggiore. Dopo la verifica delle raccomandazioni principali, verranno rese disponibili raccomandazioni aggiuntive. Se si preferisce visualizzare l'elenco dettagliato, usare Ricerca log per mostrare tutte le raccomandazioni.

*È possibile ignorare una raccomandazione?* Sì, vedere [Ignorare le raccomandazioni](#Ignore-recommendations).


## <a name="next-steps"></a>Passaggi successivi

- Vedere [Ricerche nei log](log-analytics-log-searches.md) per informazioni su come analizzare le raccomandazioni e i dati dettagliati di Controllo integrità System Center Operations Manager.
