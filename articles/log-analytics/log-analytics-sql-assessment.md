<properties
	pageTitle="Ottimizzare l'ambiente con la soluzione SQL Assessment in Log Analytics | Microsoft Azure"
	description="È possibile usare la soluzione SQL Assessment per valutare i rischi e l'integrità degli ambienti server a intervalli regolari."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Ottimizzare l'ambiente con la soluzione SQL Assessment in Log Analytics


È possibile usare la soluzione SQL Assessment per valutare i rischi e l'integrità degli ambienti server a intervalli regolari. Questo articolo consente di installare la soluzione in modo che si possano intraprendere azioni correttive per problemi potenziali.

La soluzione offre un elenco con priorità di raccomandazioni specifiche per l'infrastruttura distribuita dei server, classificate in sei aree di interesse che consentono di comprendere rapidamente il rischio e agire in maniera appropriata.

Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft in base a migliaia di visite dei clienti. Ogni raccomandazione offre informazioni aggiuntive sui motivi per cui un problema può essere rilevante per l'utente e su come implementare le modifiche suggerite.

Si possono scegliere le aree di interesse più importanti per l'organizzazione e tenere traccia dello stato di avanzamento verso la realizzazione di un ambiente integro ed esente da rischi.

Dopo aver aggiunto la soluzione e completato una valutazione, nel dashboard **SQL Assessment** per l'infrastruttura nell'ambiente verranno visualizzate le informazioni di riepilogo per l'area di interesse. Le sezioni seguenti descrivono come usare le informazioni visualizzate nel dashboard **SQL Assessment**, in cui sarà possibile intraprendere le azioni consigliate per l'infrastruttura dei server di SQL.

![immagine del riquadro SQL Assessment](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![Immagine del dashboard SQL Assessment](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## Installazione e configurazione della soluzione
SQL Assessment è compatibile con le edizioni Standard, Developer ed Enterprise di tutte le versioni attualmente supportate di SQL Server.

Usare le informazioni seguenti per installare e configurare la soluzione.

- La soluzione SQL Assessment richiede l'installazione di .NET Framework 4 in ogni computer che contiene un agente OMS.
- Quando si usa l'agente Operations Manager con SQL Assessment, è necessario usare un account RunAs di Operations Manager. Vedere di seguito [Account RunAs di Operations Manager per OMS](#operations-manager-run-as-accounts-for-oms) per altre informazioni.

    >[AZURE.NOTE] L'agente MMA non supporta gli account RunAs di Operations Manager.

- Aggiungere la soluzione SQL Assessment all'area di lavoro OMS usando la procedura descritta nell'articolo [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Aggiungere le soluzioni di Log Analytics dalla Raccolta soluzioni). Non è richiesta alcuna ulteriore configurazione.

>[AZURE.NOTE] Dopo aver aggiunto la soluzione, il file AdvisorAssessment.exe viene aggiunto al server con agenti. I dati di configurazione vengono letti e quindi inviati al servizio OMS nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud.

## Informazioni dettagliate sulla raccolta dei dati di SQL Assessment

La tabella seguente illustra i metodi di raccolta dati per gli agenti, se Operations Manager (SCOM) è obbligatorio e la frequenza con cui i dati vengono raccolti da un agente.

| piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati tramite il gruppo di gestione | frequenza della raccolta |
|---|---|---|---|---|---|---|
|Windows|![Sì](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Sì](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![No](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![No](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Sì](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 giorni|

## Account RunAs di Operations Manager per OMS

Log Analytics in OMS usa l'agente e il gruppo di gestione di Operations Manager per raccogliere e inviare dati al servizio OMS. OMS si basa si Management Pack per i carichi di lavoro per offrire servizi a valore aggiunto. Ogni carico di lavoro richiede privilegi specifici per eseguire i Management Pack in un contesto di sicurezza diverso, ad esempio un account di dominio. È necessario fornire informazioni sulle credenziali configurando un account RunAs di Operations Manager.

Usare le informazioni seguenti per impostare l'account RunAs di Operations Manager per SQL Assessment.

### Impostare l'account RunAs per SQL Assessment

 Se si usa già il Management Pack di SQL Server, è consigliabile usare l'account RunAs corrispondente.

#### Per configurare l'account RunAs di SQL in Operations Console

1. In Operations Manager aprire la console operatore e quindi fare clic su **Administration**.

2. In **Run As Configuration** (Esegui come configurazione) fare clic su **Profiles** (Profili) e aprire **OMS SQL Assessment Run As Profile** (Profilo RunAs di OMS SQL Assessment).

3. Nella pagina **Run As Account** fare clic su **Add**.

4. Selezionare un account RunAs Windows che contiene le credenziali necessarie per SQL Server oppure fare clic su **New** per crearne uno.
	>[AZURE.NOTE] Il tipo dell'account RunAs deve essere Windows. L'account RunAs deve appartenere anche al gruppo Local Administrators in tutti i server Windows che ospitano istanze di SQL Server.

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
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Understanding how recommendations are prioritized

Every recommendation made is given a weighting value that identifies the relative importance of the recommendation. Only the ten most important recommendations are shown.

### How weights are calculated

Weightings are aggregate values based on three key factors:

- The *probability* that an issue identified will cause problems. A higher probability equates to a larger overall score for the recommendation.

- The *impact* of the issue on your organization if it does cause a problem. A higher impact equates to a larger overall score for the recommendation.

- The *effort* required to implement the recommendation. A higher effort equates to a smaller overall score for the recommendation.

The weighting for each recommendation is expressed as a percentage of the total score available for each focus area. For example, if a recommendation in the Security and Compliance focus area has a score of 5%, implementing that recommendation will increase your overall Security and Compliance score by 5%.

### Focus areas

**Security and Compliance** - This focus area shows recommendations for potential security threats and breaches, corporate policies, and technical, legal and regulatory compliance requirements.

**Availability and Business Continuity** - This focus area shows recommendations for service availability, resiliency of your infrastructure, and business protection.

**Performance and Scalability** - This focus area shows recommendations to help your organization's IT infrastructure grow, ensure that your IT environment meets current performance requirements, and is able to respond to changing infrastructure needs.

**Upgrade, Migration and Deployment** - This focus area shows recommendations to help you upgrade, migrate, and deploy SQL Server to your existing infrastructure.

**Operations and Monitoring** - This focus area shows recommendations to help streamline your IT operations, implement preventative maintenance, and maximize performance.

**Change and Configuration Management** - This focus area shows recommendations to help protect day-to-day operations, ensure that changes don't negatively affect your infrastructure, establish change control procedures, and to track and audit system configurations.

### Should you aim to score 100% in every focus area?

Not necessarily. The recommendations are based on the knowledge and experiences gained by Microsoft engineers across thousands of customer visits. However, no two server infrastructures are the same, and specific recommendations may be more or less relevant to you. For example, some security recommendations might be less relevant if your virtual machines are not exposed to the Internet. Some availability recommendations may be less relevant for services that provide low priority ad hoc data collection and reporting. Issues that are important to a mature business may be less important to a start-up. You may want to identify which focus areas are your priorities and then look at how your scores change over time.

Every recommendation includes guidance about why it is important. You should use this guidance to evaluate whether implementing the recommendation is appropriate for you, given the nature of your IT services and the business needs of your organization.

## Use assessment focus area recommendations

Before you can use an assessment solution in OMS, you must have the solution installed. To read more about installing solutions, see [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md). After it is installed, you can view the summary of recommendations by using the SQL Assessment tile on the Overview page in OMS.

View the summarized compliance assessments for your infrastructure and then drill-into recommendations.

### To view recommendations for a focus area and take corrective action

1. On the **Overview** page, click the **SQL Assessment** tile.
2. On the **SQL Assessment** page, review the summary information in one of the focus area blades and then click one to view recommendations for that focus area.
3. On any of the focus area pages, you can view the prioritized recommendations made for your environment. Click a recommendation under **Affected Objects** to view details about why the recommendation is made.  
    ![image of SQL Assessment recommendations](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. You can take corrective actions suggested in **Suggested Actions**. When the item has been addressed, later assessments will record that recommended actions were taken and your compliance score will increase. Corrected items appear as **Passed Objects**.

## Ignore recommendations

If you have recommendations that you want to ignore, you can create a text file that OMS will use to prevent recommendations from appearing in your assessment results.

### To identify recommendations that you will ignore

1.	Sign in to your workspace and open Log Search. Use the following query to list recommendations that have failed for computers in your environment.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Here's a screen shot showing the Log Search query:
    ![failed recommendations](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	Scegliere le raccomandazioni da ignorare. Nella procedura successiva verranno usati i valori per ID raccomandazione.


### Per creare e usare un file di testo IgnoreRecommendations.txt

1.	Creare un file denominato IgnoreRecommendations.txt.
2.	Incollare o digitare ogni ID raccomandazione per ogni raccomandazione che OMS dovrà ignorare in una riga separata e quindi salvare e chiudere il file.
3.	Inserire il file nella cartella seguente in ogni computer in cui si vuole che OMS ignori le raccomandazioni.
    - Nei computer con Microsoft Monitoring Agent connesso direttamente o tramite Operations Manager - *SystemDrive*: \\Programmi\\Microsoft Monitoring Agent\\Agent
    - Nel server di gestione Operations Manager - *SystemDrive*: \\Programmi\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### Per verificare che le raccomandazioni vengano ignorate

1.	Dopo la successiva esecuzione della valutazione pianificata, per impostazione predefinita ogni 7 giorni, le raccomandazioni specificate sono contrassegnate come ignorate e non verranno visualizzate nel dashboard di valutazione.
2.	È possibile usare le query di Ricerca log seguenti per elencare tutte le raccomandazioni ignorate.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	Se in seguito si decide che si vogliono vedere le raccomandazioni ignorate, rimuovere eventuali file IgnoreRecommendations.txt oppure rimuovere gli ID raccomandazione dagli stessi.

## Domande frequenti sulla soluzione SQL Assessment

*Con che frequenza viene eseguita la valutazione?*
- La valutazione viene eseguita ogni 7 giorni.

*È possibile configurare la frequenza di esecuzione della valutazione?*
- Attualmente non è possibile.

*Se viene rilevato un altro server dopo l'aggiunta della soluzione SQL Assessment, il server verrà valutato?*
- Sì, a partire dal rilevamento verrà valutato ogni 7 giorni.

*Se un server viene rimosso, quando sarà rimosso dalla valutazione?*
- Se un server non invia dati per 3 settimane, verrà rimosso.

*Qual è il nome del processo che esegue la raccolta di dati?*
- AdvisorAssessment.exe

*Quanto tempo occorre per la raccolta di dati?*
- La raccolta di dati effettiva sul server richiede circa 1 ora. È possibile che sia necessario più tempo nei server in cui è presente un numero elevato di istanze o database SQL.

*Quali tipi di dati vengono raccolti?*
- Vengono raccolti i tipi di dati seguenti:
    - WMI
    - Registro
    - Contatori delle prestazioni
    - DMV (Dynamic Management View) di SQL.

*È possibile definire l'orario per la raccolta di dati?*
- Attualmente non è possibile.

*Perché è necessario configurare un account RunAs?*
- Per un server SQL vengono eseguite alcune SQL. Per permetterne l'esecuzione, è necessario usare un account RunAs con autorizzazioni di tipo VISUALIZZAZIONE STATO DEL SERVER per SQL. Per eseguire query relative a WMI, sono inoltre necessarie credenziali di amministratore locale.

*Perché vengono visualizzate solo le prime 10 raccomandazioni?*
- Invece di esaminare un lunghissimo elenco completo di attività, è consigliabile concentrare l'attenzione sulle raccomandazioni con priorità maggiore. Dopo la verifica delle raccomandazioni principali, verranno rese disponibili raccomandazioni aggiuntive. Se si preferisce visualizzare l'elenco dettagliato, usare la ricerca log di OMS per mostrare tutte le raccomandazioni.

*È possibile ignorare una raccomandazione?*
- Sì. Vedere la sezione [Ignorare le raccomandazioni](#ignore-recommendations) riportata sopra.



## Passaggi successivi

- [Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare le raccomandazioni e i dati dettagliati di SQL Assessment.

<!---HONumber=AcomDC_0504_2016-->