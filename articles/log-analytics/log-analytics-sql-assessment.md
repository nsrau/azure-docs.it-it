---
title: Ottimizzare l'ambiente SQL Server con Log Analytics di Azure|Documentazione Microsoft
description: "Con Log Analytics di Azure è possibile usare la soluzione SQL Assessment per valutare i rischi e l'integrità degli ambienti SQL Server a intervalli regolari."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2aed3315fe60ace46dfb4176dc13aa417257b0c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="optimize-your-sql-server-environment-with-the-sql-assessment-solution-in-log-analytics"></a>Ottimizzare l'ambiente SQL Server con la soluzione SQL Assessment in Log Analytics

![Simbolo di Valutazione SQL](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

È possibile usare la soluzione SQL Assessment per valutare i rischi e l'integrità degli ambienti server a intervalli regolari. Questo articolo consente di installare la soluzione in modo che si possano intraprendere azioni correttive per problemi potenziali.

La soluzione offre un elenco con priorità di raccomandazioni specifiche per l'infrastruttura distribuita dei server, classificate in sei aree di interesse che consentono di comprendere rapidamente il rischio e agire in maniera appropriata.

Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft in base a migliaia di visite dei clienti. Ogni raccomandazione offre informazioni aggiuntive sui motivi per cui un problema può essere rilevante per l'utente e su come implementare le modifiche suggerite.

Si possono scegliere le aree di interesse più importanti per l'organizzazione e tenere traccia dello stato di avanzamento verso la realizzazione di un ambiente integro ed esente da rischi.

Dopo aver aggiunto la soluzione e completato una valutazione, nel dashboard **SQL Assessment** per l'infrastruttura nell'ambiente verranno visualizzate le informazioni di riepilogo per l'area di interesse. Le sezioni seguenti descrivono come usare le informazioni visualizzate nel dashboard **SQL Assessment** , in cui sarà possibile intraprendere le azioni consigliate per l'infrastruttura dei server di SQL.

![immagine del riquadro  SQL Assessment](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![Immagine del dashboard SQL Assessment ](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
SQL Assessment è compatibile con le edizioni Standard, Developer ed Enterprise di tutte le versioni attualmente supportate di SQL Server.

Usare le informazioni seguenti per installare e configurare la soluzione.

* Nei server in cui è installato SQL Server devono essere installati gli agenti.
* La soluzione SQL Assessment richiede l'installazione di una versione di .NET Framework 4 supportata in ogni computer che contiene un agente OMS.
* Per installare la soluzione, l'utente deve essere amministratore o collaboratore della sottoscrizione di Azure se si usa il portale di Azure. L'utente deve anche essere membro del ruolo di amministratore o collaboratore dell'area di lavoro OMS nel portale di OMS.
* Quando si usa l'agente Operations Manager con SQL Assessment, è necessario usare un account RunAs di Operations Manager. Per altre informazioni, vedere di seguito [Account RunAs di Operations Manager per OMS](#operations-manager-run-as-accounts-for-oms) .

  > [!NOTE]
  > L'agente MMA non supporta gli account RunAs di Operations Manager.
  >
  >
* Aggiungere la soluzione SQL Assessment all'area di lavoro OMS usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). Non è richiesta alcuna ulteriore configurazione.

> [!NOTE]
> Dopo aver aggiunto la soluzione, il file AdvisorAssessment.exe viene aggiunto al server con agenti. I dati di configurazione vengono letti e quindi inviati al servizio OMS nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud.

## <a name="sql-assessment-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati di SQL Assessment
SQL Assessment raccoglie dati WMI, dati del Registro di sistema, dati sulle prestazioni e risultati delle visualizzazioni a gestione dinamica di SQL Server usando gli agenti abilitati.

La tabella seguente illustra i metodi di raccolta dati per gli agenti, se Operations Manager (SCOM) è obbligatorio e la frequenza con cui i dati vengono raccolti da un agente.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  | &#8226; |7 giorni |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Account RunAs di Operations Manager per OMS
Log Analytics in OMS usa l'agente e il gruppo di gestione di Operations Manager per raccogliere e inviare dati al servizio OMS. OMS si basa si Management Pack per i carichi di lavoro per offrire servizi a valore aggiunto. Ogni carico di lavoro richiede privilegi specifici per eseguire i Management Pack in un contesto di sicurezza diverso, ad esempio un account di dominio. È necessario fornire informazioni sulle credenziali configurando un account RunAs di Operations Manager.

Usare le informazioni seguenti per impostare l'account RunAs di Operations Manager per SQL Assessment.

### <a name="set-the-run-as-account-for-sql-assessment"></a>Impostare l'account RunAs per SQL Assessment
 Se si usa già il Management Pack di SQL Server, è consigliabile usare l'account RunAs corrispondente.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Per configurare l'account RunAs di SQL in Operations Console
> [!NOTE]
> Se si usa l'agente diretto OMS, anziché l'agente SCOM, il management pack viene sempre eseguito nel contesto di sicurezza dell'account di sistema locale. Ignorare i passaggi seguenti da 1 a 5 ed eseguire l'esempio di T-SQL o PowerShell, specificando NT AUTHORITY\SYSTEM come nome utente.
>
>

1. In Operations Manager aprire la console operatore e quindi fare clic su **Administration**.
2. In **Esegui come configurazione**, fare clic su **Profili** e aprire **Profilo RunAs di OMS SQL Assessment**.
3. Nella pagina **Esegui come account** fare clic su **Aggiungi**.
4. Selezionare un account RunAs Windows che contiene le credenziali necessarie per SQL Server oppure fare clic su **New** per crearne uno.

   > [!NOTE]
   > Il tipo dell'account RunAs deve essere Windows. L'account RunAs deve appartenere anche al gruppo Local Administrators in tutti i server Windows che ospitano istanze di SQL Server.
   >
   >
5. Fare clic su **Save**.
6. Modificare ed eseguire il seguente esempio T-SQL in ciascuna istanza di SQL Server per concedere le autorizzazioni minime richieste dall'account RunAs per eseguire la valutazione SQL. Tuttavia, non è necessario farlo se l'account RunAs fa già parte del ruolo server sysadmin nelle istanze di SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Per configurare l'account RunAs di SQL con Windows PowerShell
Aprire una finestra di PowerShell ed eseguire il seguente script dopo averlo aggiornato con le informazioni:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Informazioni sulla classificazione in ordine di priorità delle raccomandazioni
A ogni raccomandazione generata viene assegnato un valore di ponderazione che identifica l'importanza relativa della raccomandazione. Vengono visualizzate solo le dieci raccomandazioni più importanti.

### <a name="how-weights-are-calculated"></a>Come vengono calcolate le ponderazioni
Le ponderazioni sono valori aggregati che si basano su tre fattori chiave:

* La *probabilità* che un problema identificato causi inconvenienti. Una probabilità più elevata equivale a un punteggio complessivamente maggiore per la raccomandazione.
* L' *impatto* del problema per l'organizzazione se causa effettivamente un problema. Un impatto più elevato equivale a un punteggio complessivamente maggiore per la raccomandazione.
* Il *lavoro* richiesto per implementare la raccomandazione. Un lavoro richiesto più elevato equivale a un punteggio complessivamente inferiore per la raccomandazione.

La ponderazione per ogni raccomandazione è espressa come percentuale del punteggio totale disponibile per ogni area di interesse. Ad esempio, se una raccomandazione nell'area di interesse relativa a sicurezza e conformità ha un punteggio pari al 5%, l'implementazione della raccomandazione aumenterà del 5% il punteggio complessivo di quell'area.

### <a name="focus-areas"></a>Aree di interesse
**Sicurezza e conformità** : quest'area di interesse descrive raccomandazioni relative alle potenziali minacce e violazioni della sicurezza, ai criteri aziendali e ai requisiti di conformità tecnici, legali e normativi.

**Disponibilità e continuità aziendale** : quest'area di interesse descrive raccomandazioni relative alla disponibilità del servizio, alla resilienza dell'infrastruttura e alla protezione aziendale.

**Prestazioni e scalabilità** : quest'area di interesse descrive raccomandazioni utili per favorire la crescita dell'infrastruttura IT dell'organizzazione, assicurando che l'ambiente IT soddisfi gli attuali requisiti in termini di prestazioni e possa rispondere alle mutevoli esigenze dell'infrastruttura.

**Aggiornamento, migrazione e distribuzione**: quest'area di interesse mostra le raccomandazioni utili per aggiornare, eseguire la migrazione e distribuire SQL Server nell'infrastruttura esistente.

**Operazioni e monitoraggio**: quest'area di interesse mostra le raccomandazioni utili per semplificare le operazioni IT, implementare la manutenzione preventiva e ottimizzare le prestazioni.

**Gestione modifiche e configurazione**: quest'area di interesse mostra le raccomandazioni utili per proteggere le operazioni quotidiane, assicurare che le modifiche non influiscano negativamente sull'infrastruttura, stabilire procedure di controllo delle modifiche e rilevare e monitorare le configurazioni del sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>È consigliabile mirare a ottenere un punteggio del 100% in tutte le aree di interesse?
Non necessariamente. Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft attraverso migliaia di visite dei clienti. Non esistono tuttavia due infrastrutture di server uguali e raccomandazioni specifiche possono essere più o meno pertinenti per l'utente. Ad esempio, alcune raccomandazioni sulla sicurezza possono risultare meno pertinenti se le macchine virtuali non sono esposte a Internet. Alcune raccomandazioni sulla disponibilità possono risultare meno pertinenti per i servizi che forniscono creazione di report e raccolta dei dati ad hoc a bassa priorità. I problemi che possono essere importanti per un'azienda collaudata possono esserlo meno per una start-up. È consigliabile identificare quali sono le proprie aree di interesse prioritarie e quindi osservare il cambiamento dei punteggi nel tempo.

Ogni raccomandazione include informazioni aggiuntive sui motivi per cui potrebbe essere importante. È consigliabile usare queste informazioni aggiuntive per valutare se l'implementazione della raccomandazione è appropriata, a seconda della natura dei servizi IT e delle esigenze aziendali dell'organizzazione.

## <a name="use-assessment-focus-area-recommendations"></a>Usare le raccomandazioni relative all'area di interesse della valutazione
Prima di poter usare una soluzione di valutazione in OMS, è necessario averla installata. Per altre informazioni sull'installazione di soluzioni, vedere [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). Dopo l'installazione, sarà possibile visualizzare il riepilogo delle raccomandazioni usando il riquadro SQL Assessment della pagina Overview in OMS.

Visualizzare il riepilogo delle valutazioni relative alla conformità per l'infrastruttura, quindi visualizzare le raccomandazioni nel dettaglio.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Per visualizzare le raccomandazioni per un'area di interesse e applicare un'azione correttiva
1. Nella pagina **Panoramica** fare clic sul riquadro **SQL Assessment**.
2. Nella pagina **SQL Assessment** verificare le informazioni di riepilogo in uno dei pannelli dell'area di interesse, quindi fare clic su un'area specifica per visualizzare le raccomandazioni corrispondenti.
3. In una delle pagine relative alle aree di interesse è possibile visualizzare le raccomandazioni relative all'ambiente specifico, classificate in ordine di priorità. Fare clic su una raccomandazione in **Affected Objects** (Oggetti interessati) per visualizzare i dettagli relativi al motivo per cui è stata generata.  
    ![immagine delle raccomandazioni di SQL Assessment](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. È possibile eseguire le azioni correttive suggerite in **Suggested Actions**(Azioni suggerite). Dopo la risoluzione dell'elemento, le valutazioni successive indicheranno che le azioni consigliate sono state effettuate e il punteggio relativo alla conformità aumenterà. Gli elementi corretti vengono visualizzati come **Passed Objects**.

## <a name="ignore-recommendations"></a>Ignorare le raccomandazioni
Per ignorare delle raccomandazioni è possibile creare un file di testo che OMS userà per impedirne la visualizzazione nei risultati della valutazione.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Per identificare le raccomandazioni che verranno ignorate
1. Accedere all'area di lavoro e aprire Ricerca log. Usare la query seguente per elencare le raccomandazioni non riuscite per i computer nell'ambiente.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```

   Ecco lo screenshot che mostra la query di ricerca nei log: ![raccomandazioni con esito negativo](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)
2. Scegliere le raccomandazioni da ignorare. Nella procedura successiva verranno usati i valori per ID raccomandazione.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Per creare e usare un file di testo IgnoreRecommendations.txt
1. Creare un file denominato IgnoreRecommendations.txt.
2. Incollare o digitare ogni ID raccomandazione per ogni raccomandazione che OMS dovrà ignorare in una riga separata e quindi salvare e chiudere il file.
3. Inserire il file nella cartella seguente in ogni computer in cui si vuole che OMS ignori le raccomandazioni.
   * Nei computer con Microsoft Monitoring Agent, connesso direttamente o tramite Operations Manager, *SystemDrive*:\Programmi\Microsoft Monitoring Agent\Agent
   * Nel server di gestione Operations Manager *SystemDrive*: \Programmi\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Per verificare che le raccomandazioni vengano ignorate
1. Dopo la successiva esecuzione della valutazione pianificata, per impostazione predefinita ogni 7 giorni, le raccomandazioni specificate sono contrassegnate come ignorate e non verranno visualizzate nel dashboard di valutazione.
2. È possibile usare le query di Ricerca log seguenti per elencare tutte le raccomandazioni ignorate.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
3. Se in seguito si decide che si vogliono vedere le raccomandazioni ignorate, rimuovere eventuali file IgnoreRecommendations.txt oppure rimuovere gli ID raccomandazione dagli stessi.

## <a name="sql-assessment-solution-faq"></a>Domande frequenti sulla soluzione SQL Assessment
*Con che frequenza viene eseguita la valutazione?*

* La valutazione viene eseguita ogni 7 giorni.

*È possibile configurare la frequenza di esecuzione della valutazione?*

* Attualmente non è possibile.

*Se viene rilevato un altro server dopo l'aggiunta della soluzione SQL Assessment, il server verrà valutato?*

* Sì, a partire dal rilevamento verrà valutato ogni 7 giorni.

*Se un server viene rimosso, quando sarà rimosso dalla valutazione?*

* Se un server non invia dati per 3 settimane, verrà rimosso.

*Qual è il nome del processo che esegue la raccolta di dati?*

* AdvisorAssessment.exe

*Quanto tempo occorre per la raccolta di dati?*

* La raccolta di dati effettiva sul server richiede circa 1 ora. È possibile che sia necessario più tempo nei server in cui è presente un numero elevato di istanze o database SQL.

*Quali tipi di dati vengono raccolti?*

* Vengono raccolti i tipi di dati seguenti:
  * WMI
  * Registro
  * Contatori delle prestazioni
  * DMV (Dynamic Management View) di SQL.

*È possibile definire l'orario per la raccolta di dati?*

* Attualmente non è possibile.

*Perché è necessario configurare un account RunAs?*

* Per un server SQL vengono eseguite alcune SQL. Per permetterne l'esecuzione, è necessario usare un account RunAs con autorizzazioni di tipo VISUALIZZAZIONE STATO DEL SERVER per SQL.  Per eseguire query relative a WMI, sono inoltre necessarie credenziali di amministratore locale.

*Perché vengono visualizzate solo le prime 10 raccomandazioni?*

* Invece di esaminare un lunghissimo elenco completo di attività, è consigliabile concentrare l'attenzione sulle raccomandazioni con priorità maggiore. Dopo la verifica delle raccomandazioni principali, verranno rese disponibili raccomandazioni aggiuntive. Se si preferisce visualizzare l'elenco dettagliato, usare la ricerca log di OMS per mostrare tutte le raccomandazioni.

*È possibile ignorare una raccomandazione?*

* Sì, vedere la sezione [Ignorare le raccomandazioni](#ignore-recommendations) sopra.

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare raccomandazioni e dati dettagliati di SQL Assessment.
