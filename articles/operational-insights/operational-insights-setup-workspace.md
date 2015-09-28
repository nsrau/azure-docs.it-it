<properties
    pageTitle="Impostare l'area di lavoro e gestire le impostazioni"
    description="Informazioni su come impostare l'area di lavoro e gestire le impostazioni in Microsoft Azure Operational Insights"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/10/2015"
    ms.author="banders"/>

# Configurare l'area di lavoro e gestire le impostazioni

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Per creare una nuova area di lavoro di Microsoft Azure Operational Insights, scegliere un nome per l'area di lavoro, associarlo al proprio account e scegliere una posizione geografica. Un'area di lavoro di Operational Insights è sostanzialmente un contenitore che include informazioni sull'account e semplici informazioni di configurazione per l'account stesso. All'interno dell'organizzazione è possibile usare più aree di lavoro di Operational Insights per gestire diversi set di dati raccolti dall'intera infrastruttura IT o da una parte di essa.

Dopo aver creato l'area di lavoro, è possibile usarla per eseguire altre attività, come gestire Operational Insights, aggiungere soluzioni, connettere origini dati, aggiungere registri, selezionare account di archiviazione, visualizzare dati utilizzati nel dashboard e gestire le impostazioni per ciascuna area di lavoro.

L’articolo[Entra a far parte della squadra in pochi minuti](./operational-insights-onboard-in-minutes.md)illustra come rendersi attivi rapidamente e descrive nel dettaglio alcune delle azioni necessarie per completare la Guida introduttiva e gestire l'area di lavoro.

Tratteremo tutte le attività utilizzate di frequente nelle sezioni seguenti:

1. Aggiungere soluzioni
2. Connettere origini dati
3. Aggiungere e gestire i registri
4. Gestire utenti e account

![Procedura](./media/operational-insights-setup-workspace/steps.png)
## 1 Aggiungere soluzioni

Operational Insights di Microsoft Azure include funzionalità di valutazione della configurazione di base, in modo che sia necessario installare una soluzione per abilitarlo. È tuttavia possibile ottenere altre funzionalità aggiungendovi soluzioni dalle pagine Impostazioni o Raccolta soluzioni.

Dopo aver aggiunto una soluzione, i dati vengono raccolti dai server dell'infrastruttura e inviati al servizio Operational Insights. L'elaborazione da parte del servizio Operational Insights può richiedere da alcuni minuti a diverse ore. Dopo che il servizio ha elaborato i dati, è possibile visualizzarli in Operational Insights.

È possibile rimuovere facilmente una soluzione quando non è più necessaria. Quando si rimuove una soluzione, i relativi dati non vengono inviati a Operational Insights, pertanto la quantità di dati usati per la quota giornaliera si riduce.

### Soluzioni supportate da Microsoft Monitoring Agent

I server connessi direttamente a Microsoft Azure Operational Insights tramite Microsoft Monitoring Agent possono attualmente usare la maggior parte delle soluzioni disponibili, tra cui:

- [System Updates](operational-insights-updates.md)
- [Antimalware](operational-insights-antimalware.md)
- [Change Tracking](operational-insights-change-tracking.md)
- [SQL and Active Directory Assessment](operational-insights-assessment.md)

Le soluzioni seguenti, tuttavia , *non* sono supportate con Microsoft Monitoring Agent e richiedono System Center Operations Manager (SCOM).

- [Capacity Management](operational-insights-capacity.md)
- [Gestione degli avvisi](operational-insights-alerts.md)
- [Configuration Assessment](operational-insights-solutions.md#configuration-assessment)

Fare riferimento a [Considerazioni su Operations Manager con Operational Insights](operational-insights-operations-manager.md) per istruzioni sull'utilizzo di queste soluzioni con Operations Manager.

La raccolta di log di IIS è supportata nei computer con i sistemi operativi seguenti:

- Windows Server 2012
- Windows Server 2012 R2

### Per aggiungere soluzioni tramite la pagina Impostazioni

- Selezionare le soluzioni che si desidera aggiungere e quindi fare clic su**Aggiungi soluzioni selezionate**. Non tutte le soluzioni disponibili vengono visualizzate. Se si desidera aggiungere soluzioni che non sono elencate, utilizzare la procedura successiva.![aggiungere soluzioni](./media/operational-insights-setup-workspace/settings-add-sol.png)

### Per aggiungere una soluzione utilizzando la Raccolta soluzioni

1. Nella pagina Panoramica di Operational Insights fare clic sul riquadro **Solution Gallery**. ![immagine dell'icona delle soluzioni](./media/operational-insights-setup-workspace/sol-gallery.png)
2. Nella pagina Solutions Gallery di Operational Insights è possibile ottenere informazioni su ogni soluzione disponibile. Fare clic sul nome della soluzione da aggiungere a Operational Insights.
3. Nella pagina relativa alla soluzione scelta vengono visualizzate informazioni dettagliate sulla soluzione visualizzata. Fare clic su **Add**.
4. Nella pagina di conferma, fare clic su **Accept** per accettare le condizioni per l'utilizzo e l'informativa sulla privacy.
5. Nella pagina Overview in Operational Insights viene visualizzato un nuovo riquadro per la soluzione aggiunta ed è possibile iniziare a usarla dopo che il servizio Operational Insights ha elaborato i dati.

### Per rimuovere una soluzione utilizzando la raccolta di soluzioni

1. Nella pagina Overview di Operational Insights fare clic sul riquadro **Solutions Gallery**.
2. Nella pagina Solutions Gallery di Operational Insights fare clic su **Remove** sotto la soluzione da rimuovere.
3. Nella pagina di conferma, fare clic su **Yes** per rimuovere la soluzione

## 2 Connettere origini dati

Esistono tre modi per connettere le origini dati:

- Connettere i computer direttamente a Operational Insights Vedere [Connettere i computer direttamente a Operational Insights](./operational-insights-direct-agent.md) per maggiori informazioni. ![collegare direttamente](./media/operational-insights-setup-workspace/attach-directly.png)
- Collegare gruppi di gestione Operations Manager. Vedere [Connettersi a Operational Insights da System Center Operations Manager](./operational-insights-connect-scom.md) per maggiori informazioni. ![collegare Operations Manager](./media/operational-insights-setup-workspace/attach-om.png)
- Collegare un account di archiviazione Azure. Vedere [Analizzare i dati dai server in Microsoft Azure](./operational-insights-analyze-data-azure.md) per maggiori informazioni. ![collegamento di Azure](./media/operational-insights-setup-workspace/attach-azure.png)

## 3 Aggiungere e gestire i registri

Prima di aggiungere i log, è necessario disporre di una soluzione che utilizzerà i dati di log installati. È possibile aggiungere nuovi log per raccogliere eventi e scegliere i livelli o la gravità degli eventi da raccogliere per i log. È possibile raccogliere:

- Registri eventi di Windows
- Log di IIS
- Altri log aggiunti

![aggiungere log](./media/operational-insights-setup-workspace/collect-logs.png)

### Formato di file dei log di IIS

L'unico formato di log di IIS supportato attualmente è W3C. Si tratta tuttavia del formato più comune ed è il formato predefinito in IIS 7 e IIS 8. Se si accede a NCSA o si usa il formato nativo di IIS, Operational Insights non rileverà quindi tali log. Anche se si usa il formato W3C, non tutti i campi vengono registrati per impostazione predefinita. Per altre informazioni su questo formato, vedere [Selezionare i campi W3C da registrare (IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx).


> [AZURE.TIP]Per assicurare un'esperienza di ricerca registro ottimale, è consigliabile selezionare la registrazione di tutti i campi per ogni sito Web usando **Registrazione** in IIS. È anche consigliabile cambiare la pianificazione **Aggiornamento file di registro per i nuovi log** e impostarla su **Ogni ora**, in modo che i file più piccoli vengano caricati sul cloud, risparmiando larghezza di banda.


### Per raccogliere registri eventi di Windows da Operations Manager o dagli agenti connessi direttamente

1. Nella**Panoramica**fare clic sul riquadro**Impostazioni**e quindi fare clic sulla scheda**Registri**.
2. Digitare il nome del registro eventi da cui si vogliono raccogliere informazioni. In caso di dubbi sul nome da usare, selezionare le proprietà del registro eventi di Windows nel **Visualizzatore eventi**, copiare il nome nel campo **NomeCompleto**, quindi incollarlo nella casella **Raccogli eventi dai seguenti registri di eventi**.
3. Fare clic su **+** per aggiungere il log.
4. Selezionare i livelli o le gravità degli eventi da raccogliere per il log. Gli eventi **Controllo riuscito** e **Controllo non riuscito** non sono supportati in questa versione.
5. Ripetere i passaggi precedenti per ogni log da cui si vogliono raccogliere informazioni, quindi fare clic su **Save**.
6. Gli eventi dovrebbero essere visualizzati in Operational Insights entro pochi minuti e sarà quindi possibile eseguire ricerche nei dati.

### Per raccogliere i log di IIS da Operations Manager o dagli agenti connessi direttamente

1. Nella**Panoramica**fare clic sul riquadro**Impostazioni**e quindi fare clic sulla scheda**Registri**.
2. In **Log** sotto **Log eventi**, selezionare **Raccogli log da Operations Manager**.


### Per raccogliere i log di IIS e/o eventi di Windows da Diagnostica di Azure
Questa opzione è configurabile dal portale di gestione di Azure, non dal portale di Operational Insights. Nella propria area di lavoro passare alla scheda **Archiviazione** e abilitare la raccolta di log da quell'account di archiviazione.

### Dopo aver configurato la raccolta di log
Dopo aver configurato la raccolta di log, i criteri di raccolta dei log vengono inviati agli agenti o tramite i gruppi di gestione per gli agenti e il servizio inizia la raccolta di eventi.

È possibile accedere ad alcuni guasti iniziali degli eventi di registro raccolti dai server monitorati visualizzando la pagina**Utilizzo**.

![immagine del riquadro della pagina utilizzo](./media/operational-insights-setup-workspace/usage.png)


## 4 Gestire utenti e account
Gestire account e utenti utilizzando la scheda**Account**nella pagina Impostazioni. Da questo punto è possibile eseguire le attività seguenti:

![scheda account](./media/operational-insights-setup-workspace/manage-users.png)

## Aggiungere un utente a un'area di lavoro esistente


Per aggiungere un utente o un gruppo a un'area di lavoro di Operational Insights, seguire questa procedura. L'utente o il gruppo potrà visualizzare e agire su tutti gli avvisi associati a questa area di lavoro.

>[AZURE.NOTE]Se si desidera aggiungere un utente o un gruppo dal proprio account aziendale di Azure Active Directory, è necessario assicurarsi innanzitutto di aver associato il proprio account di Operational Insights al dominio Active Directory. Vedere [Aggiungere un'organizzazione di Azure Active Directory a un'area di lavoro esistente](#).

### Per aggiungere un utente a un'area di lavoro esistente
1. In Operational Insights, fare clic sul riquadro**Impostazioni**.
2. Scegliere la scheda**Account**.
3. Nella sezione**Gestisci utenti**quindi scegliere il tipo di account da aggiungere:**Account aziendale**o**Account Microsoft**.
    - Se si sceglie l'Account Microsoft, digitare l'indirizzo di posta elettronica dell'utente associato all'Account Microsoft.
    - Se si sceglie l'Account dell'organizzazione, è possibile immettere parte del nome utente o del gruppo utenti o alias di posta elettronica e verrà visualizzato un elenco di utenti e gruppi. Selezionare un utente o gruppo.
        >[AZURE.NOTE]Per prestazioni ottimali, limitare il numero di gruppi di Active Directory associati a un singolo account di Operational Insights a due, ovvero uno per gli amministratori e uno per gli utenti. L'uso di più gruppi potrebbe compromettere le prestazioni di Operational Insights.
7. Scegliere il tipo di utente o gruppo da aggiungere:**Amministratore**o**Utente**.  
8. Fare clic su **Aggiungi**.

  Se si aggiunge un account Microsoft, all'indirizzo di posta elettronica fornito viene inviato un invito ad aggiungere l'account. Dopo che l'utente avrà seguito le istruzioni contenute nell'invito a iscriversi a Operational Insights, questi può visualizzare gli avvisi e le informazioni per l'account di Operational Insights e potrà visualizzare le informazioni utente nella finestra **Account** della pagina **Impostazioni**. Se si aggiunge un account aziendale, l'utente potrà immediatamente accedere a Operational Insights. ![invito](./media/operational-insights-setup-workspace/manage-users04.png)


### Numero di aree di lavoro necessarie
Un'area di lavoro viene considerata come una risorsa di Azure all'interno del portale di gestione di Azure.

È possibile creare una nuova area di lavoro o stabilire un collegamento a un'area di lavoro esistente aperta in precedenza per l'uso con System Center Operations Manager, ma non ancora associata a una sottoscrizione di Azure (necessaria per la fatturazione). Un'area di lavoro rappresenta il livello a cui i dati vengono raccolti, aggregati, analizzati e presentati nel portale di Operational Insights. È possibile scegliere di disporre di più aree di lavoro per separare i dati di ambienti e sistemi diversi. Ogni gruppo di gestione di Operations Manager (e tutti i relativi agenti) o singoli agenti o macchine virtuali possono essere connessi a una sola area di lavoro.

A ogni area di lavoro possono essere associati più account utente, ciascuno dei quali (account Microsoft o account aziendale) può avere accesso a diverse aree di lavoro di Operational Insights. Per impostazione predefinita, l'account Microsoft o l'account aziendale usato per creare l'area di lavoro diventa l'amministratore di quest'ultima. L'amministratore può quindi invitare altri account Microsoft o scegliere utenti da Azure Active Directory.

## Collegare un'area di lavoro esistente a una sottoscrizione di Azure

È possibile creare un'area di lavoro da[microsoft.com/oms](https://microsoft.com/oms). Per le aree di lavoro, tuttavia, esistono determinate limitazioni. La principale è il limite di 500 MB al giorno di caricamento dati se si usa un account gratuito. Per apportare modifiche a questa area di lavoro, è necessario **collegare l'area di lavoro esistente a una sottoscrizione di Azure**.

>[AZURE.IMPORTANT]Per collegare un'area di lavoro, è necessario che l'account di Azure abbia già accesso a tale area. In altri termini, è necessario che l'account usato per accedere al portale di Azure sia **lo stesso** account usato per accedere all'area di lavoro di Operational Insights. Se non si tratta dello stesso account, vedere [Aggiungere un utente a un'area di lavoro esistente](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. Accedere al portale di gestione di Azure.
2. Fare clic su **+ Nuovo** nella parte inferiore sinistra del portale.
3. Fare clic su **Servizi app**, scorrere fino a **Operational Insights** e selezionarlo.
4. Fare clic su **Creazione rapida**.
5. Nell'elenco **Account** sono riportate le aree di lavoro esistenti *non ancora* collegate alla sottoscrizione di Azure. Selezionare un account.

  >[AZURE.NOTE]Se non viene visualizzata l'area di lavoro a cui ci si vuole collegare, significa che la sottoscrizione di Azure non ha accesso all'area di lavoro di Operational Insights. È necessario concedere l'accesso a questo account dall'area di lavoro di Operational Insights. Per eseguire questa operazione, vedere [Aggiungere un utente a un'area di lavoro esistente](#add-a-user-to-an-existing-workspace).

  ![collegamento account](./media/operational-insights-setup-workspace/link-account.png) <p> 6. Compilare i campi rimanenti e selezionare **Crea area di lavoro**.

## Eseguire l'aggiornamento dell'area di lavoro a un piano a pagamento

In Operational Insights sono disponibili tre tipi di piano per l'area di lavoro: **Gratuito**, **Standard** e **Premium**. Si supponga di avere sottoscritto un piano di tipo *Free* e di avere raggiunto il limite massimo di 500 MB di dati. Per raccogliere dati oltre questo limite, è necessario aggiornare l'area di lavoro a un '**piano con pagamento in base al consumo**'. Il tipo di piano può essere cambiato in qualsiasi momento. Per altre informazioni sui prezzi di Operational Insights, vedere l'articolo relativo ai [dettagli sui prezzi](https://azure.microsoft.com/it-IT/pricing/details/operational-insights/)

>[AZURE.IMPORTANT]È possibile modificare i piani relativi alle aree di lavoro solo se queste sono *collegate* a una sottoscrizione di Azure. Se l'area di lavoro è stata creata in Azure o se è *già* stata collegata, è possibile ignorare questo messaggio. Se l'area di lavoro è stata creata da [opinsights.azure.com](http://opinsights.azure.com), seguire la procedura riportata in [Collegare un'area di lavoro esistente a una sottoscrizione di Azure](#link-an-existing-workspace-to-an-Azure-subscription).

### Cambiare il tipo di piano

Nel portale di gestione di Azure passare all'area di lavoro di Operational Insights per cui si desidera eseguire l'aggiornamento:

![scala](./media/operational-insights-setup-workspace/find-workspace.png)

Selezionare l'area di lavoro e selezionare **SCALE** dalle schede nella parte superiore dello schermo

![selezione scala](./media/operational-insights-setup-workspace/select-scale.png)

Al termine scegliere il piano a cui si desidera eseguire l'aggiornamento e fare clic su **SAVE**. Le modifiche verranno riflesse nel portale e sarà possibile raccogliere dati oltre il limite del piano "Free".

![selezione piano](./media/operational-insights-setup-workspace/plan-select.png)

## Aggiungere un'organizzazione di Azure Active Directory a un'area di lavoro esistente

È possibile associare l'area di lavoro di Operational Insights a un dominio Azure Active Directory. Questo consente di aggiungere utenti da Active Directory direttamente nell'area di lavoro di Operational Insights senza richiedere un account Microsoft separato.

### Per aggiungere un'organizzazione di Azure Active Directory a un'area di lavoro esistente

1. Nella pagina Impostazioni di Operational Insights fare clic su **Account** e fare clic su **Aggiungi Organizzazione**. ![invito](./media/operational-insights-setup-workspace/add-org.png)
2. Rivedere le informazioni sugli account aziendali e quindi fare clic su **Next**.
3. Immettere le informazioni sull'identità per l'amministratore del dominio di Azure Active Directory e quindi fare clic su **Sign in**.
4. Fare clic su **Grant access** per consentire a Operational Insights di usare le informazioni sull'identità nel dominio Active Directory. ![collegato](./media/operational-insights-setup-workspace/ad-existing01.png)


## Modificare un tipo utente esistente

È possibile modificare il ruolo account per un utente associato all'account di Operational Insights account. Per il ruolo sono disponibili le opzioni seguenti:

 - *Administrator*: può gestire gli utenti, visualizzare ed eseguire operazioni su tutti gli avvisi e aggiungere e rimuovere i server

 - *User*: può visualizzare ed eseguire operazioni su tutti gli avvisi e aggiungere e rimuovere i server

### Per modificare un account
1. Nella pagina **Impostazioni**nella scheda**Account**in Operational Insights, selezionare il ruolo dell'utente che si desidera modificare.
2. Fare clic su **OK**.

## Rimuovere un utente da un'area di lavoro di Operational Insights

Per rimuovere un utente da un'area di lavoro di Operational Insights, seguire questa procedura. Si noti che questo non comporta la chiusura dell'area di lavoro dell'utente, ma rimuove l'associazione tra l'utente e l'area di lavoro stessa. Se è associato a più aree di lavoro, un utente sarà comunque in grado di accedere a Operational Insights.

### Per rimuovere un utente da un'area di lavoro

1. Nella pagina**Impostazioni**nella scheda**Account**di Operational Insights, fare clic su Rimuovi accanto al nome utente che si desidera rimuovere.
2. Fare clic su **OK** per confermare che si desidera rimuovere l'utente.

## Chiudere l'area di lavoro di Operational Insights

Quando si chiude un'area di lavoro di Operational Insights, tutti i dati relativi a tale area verranno eliminati dal servizio di Operational Insights non più di 30 giorni dopo la chiusura dell'area di lavoro stessa.

Se si è un amministratore e vi sono più utenti associati all'area di lavoro, l'associazione tra quest'ultima e gli utenti viene interrotta. Se gli utenti sono associati ad altre aree di lavoro, potranno continuare a usare tali aree di lavoro di Operational Insights. Tuttavia, se non sono associati ad altre aree di lavoro, per usare Operational Insights dovranno crearne una nuova.

### Per chiudere un'area di lavoro di Operational Insights

1. Nella pagina **Impostazioni** nella scheda **Account** di Operational Insights, fare clic su **Chiudi area di lavoro**.

2. Selezionare uno dei motivi per la chiusura dell'area di lavoro o immettere un motivo diverso nella casella di testo.

3. Fare clic su **Close workspace**.

## Risorse aggiuntive
- [Requisiti per il formato dei log di IIS in Azure Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- Per informazioni sulle altre origini dati e sui tipi di log di cui la community richiede l'implementazione, vedere il [forum relativo a commenti e suggerimenti](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

<!---HONumber=Sept15_HO3-->