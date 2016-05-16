<properties
	pageTitle="Gestire l'accesso a Log Analytics | Microsoft Azure"
	description="Gestire l'accesso a Log Analytics usando svariate attività amministrative per utenti, account, aree di lavoro di OMS e account Azure."
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

# Gestire l'accesso a Log Analytics

Per gestire l'accesso a Log Analytics, si useranno svariate attività amministrative per utenti, account, aree di lavoro di OMS e account Azure. Per creare una nuova area di lavoro in Operations Management Suite (OMS), scegliere un nome per l'area di lavoro, associarlo al proprio account e scegliere una località geografica. Un'area di lavoro è sostanzialmente un contenitore che include informazioni sull'account e semplici informazioni di configurazione per l'account stesso. Nell'organizzazione è possibile usare più aree di lavoro di OMS per gestire diversi set di dati raccolti dall'intera infrastruttura IT o da una parte di essa.

L'articolo [Introduzione a Log Analytics](log-analytics-get-started.md) illustra come rendersi attivi rapidamente e descrive nel dettaglio alcune delle azioni necessarie per gestire l'accesso a OMS.

Anche se non è necessario eseguire fin dall'inizio ogni attività di gestione, nelle sezioni seguenti verranno illustrate tutte le attività usate di frequente:

- Gestire utenti e account
- Aggiungere un gruppo a un'area di lavoro esistente
- Determinare il numero di aree di lavoro necessarie
- Collegare un'area di lavoro esistente a una sottoscrizione di Azure
- Aggiornare un'area di lavoro a un piano a pagamento
- Modificare un tipo di piano dati
- Aggiungere un'organizzazione di Azure Active Directory a un'area di lavoro esistente
- Controllare l'accesso alle risorse di Log Analytics di OMS
- Chiudere l'aea di lavoro di OMS

## Gestire utenti e account
Gestire account e utenti utilizzando la scheda**Account**nella pagina Impostazioni. Da questo punto è possibile eseguire le attività nelle sezioni seguenti:

![gestione utenti](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

### Aggiungere un utente a un'area di lavoro esistente

Per aggiungere un utente o un gruppo a un'area di lavoro di OMS, seguire questa procedura. L'utente o il gruppo potrà visualizzare e agire su tutti gli avvisi associati a questa area di lavoro.

>[AZURE.NOTE] Per aggiungere un utente o un gruppo dal proprio account aziendale Azure Active Directory, prima di tutto è necessario assicurarsi di avere associato il proprio account OMS al dominio di Active Directory. Vedere [Aggiungere un'organizzazione di Azure Active Directory a un'area di lavoro esistente](#add-an-azure-active-directory-organization-to-an-existing-workspace).

#### Per aggiungere un utente a un'area di lavoro esistente
1. In OMS fare clic sul riquadro **Impostazioni**.
2. Scegliere la scheda**Account**.
3. Nella sezione**Gestisci utenti**quindi scegliere il tipo di account da aggiungere:**Account aziendale**o**Account Microsoft**.
    - Se si sceglie l'Account Microsoft, digitare l'indirizzo di posta elettronica dell'utente associato all'Account Microsoft.
    - Se si sceglie l'Account dell'organizzazione, è possibile immettere parte del nome utente o del gruppo utenti o alias di posta elettronica e verrà visualizzato un elenco di utenti e gruppi. Selezionare un utente o gruppo.

    >[AZURE.NOTE] Per prestazioni ottimali, limitare il numero di gruppi di Active Directory associati a un singolo account OMS a due, ovvero uno per gli amministratori e uno per gli utenti. L'uso di più gruppi potrebbe compromettere le prestazioni di Log Analytics.

7. Scegliere il tipo di utente o gruppo da aggiungere:**Amministratore**o**Utente**.
8. Fare clic su **Aggiungi**.

  Se si aggiunge un account Microsoft, all'indirizzo di posta elettronica fornito viene inviato un invito ad aggiungere l'account. Dopo che l'utente avrà seguito le istruzioni contenute nell'invito a iscriversi a OMS, questi può visualizzare gli avvisi e le informazioni per l'account OMS e potrà visualizzare le informazioni utente nella finestra **Account** della pagina **Impostazioni**. Se si aggiunge un account aziendale, l'utente potrà immediatamente accedere a Log Analytics. ![Email di invito](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

### Modificare un tipo utente esistente

È possibile modificare il ruolo account per un utente associato all'account OMS. Per il ruolo sono disponibili le opzioni seguenti:

 - *Administrator*: può gestire gli utenti, visualizzare ed eseguire operazioni su tutti gli avvisi e aggiungere e rimuovere i server

 - *User*: può visualizzare ed eseguire operazioni su tutti gli avvisi e aggiungere e rimuovere i server

#### Per modificare un account
1. Nella scheda**Account** della pagina **Impostazioni** di OMS selezionare il ruolo dell'utente che si vuole modificare.
2. Fare clic su **OK**.

## Rimuovere un utente da un'area di lavoro di OMS

Per rimuovere un utente da un'area di lavoro di OMS, seguire questa procedura. Si noti che questo non comporta la chiusura dell'area di lavoro dell'utente, ma rimuove l'associazione tra l'utente e l'area di lavoro stessa. Se è associato a più aree di lavoro, un utente riuscirà comunque ad accedere a OMS.

### Per rimuovere un utente da un'area di lavoro

1. Nella scheda**Account** della pagina **Impostazioni** di OMS fare clic su Rimuovi accanto al nome utente che si vuole rimuovere.
2. Fare clic su **OK** per confermare che si desidera rimuovere l'utente.


## Aggiungere un gruppo a un'area di lavoro esistente

1.	Seguire i passaggi da 1 a 4 in "Per aggiungere un utente a un'area di lavoro esistente" sopra.
2.	In **Scegliere l'utente/gruppo** selezionare **Gruppo**. ![Aggiungere un gruppo a un'area di lavoro esistente](./media/log-analytics-manage-access/add-group.png)
3.	Immettere il nome visualizzato o l'indirizzo di posta elettronica per il gruppo che si vuole aggiungere.
4.	Selezionare il gruppo nei risultati dell'elenco e quindi fare clic su **Aggiungi**.

## Determinare il numero di aree di lavoro necessarie

Un'area di lavoro viene considerata come una risorsa di Azure all'interno del portale di gestione di Azure.

È possibile creare una nuova area di lavoro o stabilire un collegamento a un'area di lavoro esistente aperta in precedenza per l'uso con System Center Operations Manager, ma non ancora associata a una sottoscrizione di Azure (necessaria per la fatturazione).

Un'area di lavoro rappresenta il livello a cui i dati vengono raccolti, aggregati, analizzati e presentati nel portale di OMS. È possibile scegliere di disporre di più aree di lavoro per separare i dati di ambienti e sistemi diversi. Ogni gruppo di gestione di Operations Manager (e tutti i relativi agenti) o singoli agenti o macchine virtuali possono essere connessi a una sola area di lavoro.

A ogni area di lavoro possono essere associati più account utente, ciascuno dei quali (account Microsoft o account aziendale) può avere accesso a diverse aree di lavoro di OMS.

Per impostazione predefinita, l'account Microsoft o l'account aziendale usato per creare l'area di lavoro diventa l'amministratore di quest'ultima. L'amministratore può quindi invitare altri account Microsoft o scegliere utenti da Azure Active Directory.

## Collegare un'area di lavoro esistente a una sottoscrizione di Azure

È possibile creare un'area di lavoro dal sito Web [microsoft.com/oms](https://microsoft.com/oms). Per le aree di lavoro, tuttavia, esistono determinate limitazioni. La principale è il limite di 500 MB al giorno di caricamento dati se si usa un account gratuito. Per apportare modifiche a questa area di lavoro, è necessario **collegare l'area di lavoro esistente a una sottoscrizione di Azure**.

>[AZURE.IMPORTANT] Per collegare un'area di lavoro, è necessario che l'account di Azure abbia già accesso a tale area. In altri termini, è necessario che l'account usato per accedere al portale di Azure sia **lo stesso** account usato per accedere all'area di lavoro di OMS. Se non si tratta dello stesso account, vedere [Aggiungere un utente a un'area di lavoro esistente](#add-a-user-to-an-existing-workspace).

1.	Accedere al [portale di Azure](http://portal.azure.com).
2.	Cercare **Log Analytics (OMS)** e quindi selezionarlo.
3.	Verrà visualizzato un elenco delle aree di lavoro esistenti. Fare clic su **Aggiungi**. ![Elenco di aree di lavoro](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.	In **Area di lavoro di OMS** fare clic su **O collega esistente**. ![Collegare un'area di lavoro esistente](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.	Fare clic su **Configura le impostazioni necessarie**. ![Configurare le impostazioni necessarie](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.	Verrà visualizzato l'elenco delle aree di lavoro non ancora collegate all'account Azure. Selezionare un'area di lavoro. ![Selezionare le aree di lavoro](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.	Se necessario, è possibile modificare i valori per gli elementi seguenti:
    - Sottoscrizione
    - Gruppo di risorse
    - Percorso
    - Piano tariffario ![Modificare i valori](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.	Fare clic su **Create**. L'area di lavoro ora è collegata all'account Azure.

>[AZURE.NOTE] Se non viene visualizzata l'area di lavoro a cui ci si vuole collegare, la sottoscrizione di Azure non ha accesso all'area di lavoro di OMS creata usando il sito Web OMS. È necessario concedere l'accesso a questo account dall'area di lavoro di OMS usando il sito Web OMS. Per eseguire questa operazione, vedere [Aggiungere un utente a un'area di lavoro esistente](#add-a-user-to-an-existing-workspace).



## Aggiornare un'area di lavoro a un piano a pagamento

In OMS sono disponibili tre tipi di piano per l'area di lavoro: **Gratuito**, **Standard** e **Premium**. Si supponga di avere sottoscritto un piano di tipo *Free* e di avere raggiunto il limite massimo di 500 MB di dati. Per raccogliere dati oltre questo limite, è necessario aggiornare l'area di lavoro a un '**piano con pagamento in base al consumo**'. Il tipo di piano può essere cambiato in qualsiasi momento. Per altre informazioni sui prezzi di OMS, vedere [Dettagli sui prezzi](https://www.microsoft.com/it-IT/server-cloud/operations-management-suite/pricing.aspx).

>[AZURE.IMPORTANT] È possibile modificare i piani relativi alle aree di lavoro solo se queste sono *collegate* a una sottoscrizione di Azure. Se l'area di lavoro è stata creata in Azure o se è *già* stata collegata, è possibile ignorare questo messaggio. Se l'area di lavoro è stata creata con il [sito Web OMS](http://www.microsoft.com/oms), seguire la procedura riportata in [Collegare un'area di lavoro esistente a una sottoscrizione di Azure](#link-an-existing-workspace-to-an-azure-subscription).

### Uso di diritti dal componente aggiuntivo OMS per System Center

Il componente aggiuntivo OMS per System Center fornisce un diritto per il piano Premium di Log Analytics di OMS, illustrato in [Prezzi OMS](https://www.microsoft.com/it-IT/server-cloud/operations-management-suite/pricing.aspx).

Se si acquista il componente aggiuntivo OMS per System Center, il team degli account Microsoft o il rivenditore assocerà i componenti aggiuntivi OMS al contratto Enterprise che include gli acquisti di Azure. Il componente aggiuntivo OMS crea un diritto sul contratto e qualsiasi sottoscrizione di Azure può usare il diritto. Ciò consente, ad esempio, di avere più aree di lavoro di OMS che usano il diritto dal componente aggiuntivo OMS.

Per assicurarsi che l'utilizzo di un'area di lavoro OMS venga applicato ai diritti dal componente aggiuntivo OMS, sarà necessario:

1. Collegare l'area di lavoro di OMS a una sottoscrizione di Azure che fa parte del contratto Enterprise che include sia l'acquisto del componente aggiuntivo OMS che l'utilizzo della sottoscrizione di Azure
2. Selezionare il piano Premium per l'area di lavoro

Quando si esamina l'utilizzo nel portale di Azure o di OMS, i diritti del componente aggiuntivo OMS non verranno visualizzati. È tuttavia possibile visualizzare i diritti in Enterprise Portal.

Se è necessario modificare la sottoscrizione di Azure a cui è collegata l'area di lavoro di OMS, è possibile usare il cmdlet [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx) di Azure PowerShell.

### Uso dell'impegno di Azure da un contratto Enterprise

Se si sceglie di usare i prezzi autonomi per i componenti di OMS, si pagherà ogni singolo componente di OMS e l'utilizzo verrà visualizzato nella fattura di Azure.

Se è stata pagata in anticipo una determinata quantità di utilizzo di Azure nell'ambito del contratto Enterprise, l'utilizzo di OMS userà l'utilizzo pagato in anticipo. Per usare i prezzi impegno di Azure per Log Analytics di OMS, la sottoscrizione a cui è collegata l'area di lavoro di OMS deve fare parte del contratto Enterprise di Azure.

Se è necessario modificare la sottoscrizione di Azure a cui è collegata l'area di lavoro di OMS, è possibile usare il cmdlet [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx) di Azure PowerShell.



### Per sostituire un'area di lavoro con un piano a pagamento

1.	Accedere al [portale di Azure](http://portal.azure.com).
2.	Cercare **Log Analytics (OMS)** e quindi selezionarlo.
3.	Verrà visualizzato un elenco delle aree di lavoro esistenti. Selezionare un'area di lavoro. ![Elenco di aree di lavoro](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.	In **Impostazioni** fare clic su **Piano tariffario**. ![piano tariffario](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.	In **Piano tariffario** selezionare un piano dati e quindi fare clic su **Seleziona**. ![selezione piano](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.	Quando si aggiorna la visualizzazione nel portale di Azure, **Piano tariffario** risulterà aggiornato per il piano selezionato. ![Aggiornare il piano tariffario](./media/log-analytics-manage-access/manage-access-change-plan04.png)

Ora è possibile raccogliere dati oltre il limite del piano "Gratuito".


## Aggiungere un'organizzazione di Azure Active Directory a un'area di lavoro esistente

È possibile associare l'area di lavoro di Operational Insights (OMS) a un dominio di Azure Active Directory. Questo consente di aggiungere utenti da Active Directory direttamente nell'area di lavoro di OMS senza richiedere un account Microsoft separato.

### Per aggiungere un'organizzazione di Azure Active Directory a un'area di lavoro esistente

1. Nella pagina Impostazioni di OMS fare clic su **Account** e quindi su **Informazioni area di lavoro**.  
2. Rivedere le informazioni sugli account aziendali e quindi fare clic su **Aggiungi organizzazione**. ![Aggiungere l'organizzazione](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Immettere le informazioni sull'identità per l'amministratore del dominio di Azure Active Directory. Successivamente, verrà visualizzato un acknowledgment che informa che l'area di lavoro è collegata al dominio di Azure Active Directory. ![Acknowledgment dell'area di lavoro collegata](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## Controllare l'accesso alle risorse di Log Analytics di OMS

È possibile concedere agli utenti l'accesso all'area di lavoro di OMS da 2 posizioni:

- L'accesso al portale di OMS viene gestito nel portale di OMS ed è indipendente dal fatto che le persone abbiano accesso alle sottoscrizioni di Azure in cui si trovano le risorse.
- L'accesso a PowerShell e all'API REST viene gestito in Azure usando il controllo degli accessi in base al ruolo di Azure

Se a una persona è stato concesso l'accesso al portale di OMS, ma non alla sottoscrizione di Azure a cui è collegato, i riquadri delle soluzioni Automazione, Backup e Site Recovery non visualizzeranno nessun dato per l'utente quando accede al portale di OMS.

Per consentire agli utenti di visualizzare i dati in queste soluzioni, assicurarsi che abbiano almeno l'accesso **lettore** per l'account di Automazione, l'insieme di credenziali per il backup e l'insieme di credenziali di Site Recovery collegati all'area di lavoro di OMS.


## Chiudere l'aea di lavoro di OMS

Quando si chiude un'area di lavoro di OMS, tutti i dati relativi a tale area verranno eliminati dal servizio OMS non più di 30 giorni dopo la chiusura dell'area di lavoro stessa.

Se si è un amministratore e vi sono più utenti associati all'area di lavoro, l'associazione tra quest'ultima e gli utenti viene interrotta. Se gli utenti sono associati ad altre aree di lavoro, potranno continuare a usare tali aree di lavoro di OMS. Tuttavia, se non sono associati ad altre aree di lavoro, per usare OMS dovranno crearne una nuova.

### Per chiudere un'area di lavoro di OMS

1. Nella pagina **Impostazioni** nella scheda **Account** di OMS fare clic su **Chiudi area di lavoro**.

2. Selezionare uno dei motivi per la chiusura dell'area di lavoro o immettere un motivo diverso nella casella di testo.

3. Fare clic su **Close workspace**.

## Passaggi successivi

- Vedere [Connettere computer Windows a Log Analytics](log-analytics-windows-agents.md) per aggiungere agenti e raccogliere dati.
- [Aggiungere soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md) per aggiungere funzionalità e raccogliere i dati.
- [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md) se l'organizzazione usa un server proxy o un firewall per consentire agli agenti di comunicare con il servizio Log Analytics.

<!---HONumber=AcomDC_0504_2016-->