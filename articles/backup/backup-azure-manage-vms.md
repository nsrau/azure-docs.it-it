
<properties
	pageTitle="Gestire e monitorare i backup della macchina virtuale di Azure distribuite con Resource Manager | Microsoft Azure"
	description="Informazioni su come gestire e monitorare i backup delle macchine virtuali distribuite con Resource Manager"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/03/2016"
	ms.author="jimpark; markgal;"/>

# Gestire e monitorare i backup delle macchine virtuali di Azure

> [AZURE.SELECTOR]
- [Gestire i backup delle macchine virtuali di Azure](backup-azure-manage-vms.md)
- [Gestire i backup delle macchine virtuali classiche](backup-azure-manage-vms-classic.md)

Questo articolo fornisce informazioni aggiuntive su come gestire i backup delle macchine virtuali (VM) e descrive le informazioni di backup disponibili nel dashboard del portale. Le informazioni aggiuntive incluse in questo articolo si applicano all'uso di macchine virtuali con gli insiemi di credenziali di Servizi di ripristino. Questo articolo non illustra la creazione di macchine virtuali, né come proteggerle. Per una panoramica della protezione di macchine virtuali distribuite tramite Azure Resource Manager con un insieme di credenziali di Servizi di ripristino, vedere [Primi passi: eseguire il backup di VM di Azure Resource Manager in un insieme di credenziali dei servizi di ripristino](backup-azure-vms-first-look-arm.md).

## Accedere agli insiemi di credenziali e alle macchine virtuali protette

Nel portale di Azure il dashboard dell'insieme di credenziali di Servizi di ripristino consente di accedere alle informazioni relative all'insieme di credenziali, quali:

- Snapshot di backup più recente, che corrisponde anche al punto di ripristino più recente <br>
- Criteri di backup <br>
- Dimensioni totali di tutti gli snapshot di backup <br>
- Numero di macchine virtuali protette con l'insieme di credenziali <br>

Molte attività di gestione per un backup della macchina virtuale iniziano con l'apertura dell'insieme di credenziali nel dashboard. Tuttavia, poiché gli insiemi di credenziali possono essere usati per proteggere più elementi, o più macchine virtuali, per visualizzare i dettagli di una determinata macchina virtuale è necessario aprire il dashboard dell'elemento dell'insieme di credenziali. La procedura seguente illustra come aprire il *dashboard dell'insieme di credenziali* e quindi passare al *dashboard dell'elemento dell'insieme di credenziali*. Entrambe le procedure includono "suggerimenti" che indicano come aggiungere l'insieme di credenziali e l'elemento dell'insieme di credenziali al dashboard di Azure tramite il comando Aggiungi al dashboard. L'aggiunta al dashboard è un modo per creare un collegamento all'insieme di credenziali o a un elemento. Dal collegamento è anche possibile eseguire comandi comuni.

>[AZURE.TIP] Se sono aperti più dashboard e pannelli, è possibile usare il dispositivo di scorrimento blu nella parte inferiore della finestra per scorrere la visualizzazione nel dashboard di Azure in modo bidirezionale.

![Visualizzazione completa con il dispositivo di scorrimento](./media/backup-azure-manage-vms/bottom-slider.png)

### Aprire un insieme di credenziali di Servizi di ripristino nel dashboard:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Scegliere **Sfoglia** dal menu dell'hub e nell'elenco di risorse digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insieme di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.

    ![Elenco di insiemi di credenziali dei Servizi di ripristino](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

    >[AZURE.TIP] Se si aggiunge un insieme di credenziali al dashboard di Azure, sarà immediatamente accessibile quando si apre il portale di Azure. Per aggiungere un insieme di credenziali al dashboard, nell'elenco di insiemi di credenziali fare clic con il pulsante destro del mouse sull'insieme desiderato e scegliere **Aggiungi al dashboard**.

3. Nell'elenco di insiemi di credenziali selezionare quello per cui si vuole aprire il dashboard. Quando si seleziona l'insieme di credenziali, vengono aperti il dashboard corrispondente e il relativo pannello **Impostazioni**. Nella figura seguente è evidenziato il dashboard **Contoso-vault**.

    ![Aprire il dashboard dell'insieme di credenziali e il pannello Impostazioni](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### Aprire il dashboard di un elemento dell'insieme di credenziali

Nella procedura precedente è stato aperto il dashboard dell'insieme di credenziali. Per aprire il dashboard di un elemento dell'insieme di credenziali:

1. Nel riquadro **Elementi di backup** del dashboard dell'insieme di credenziali fare clic su **Macchine virtuali di Azure**.

    ![Aprire il riquadro Elementi di backup](./media/backup-azure-manage-vms/contoso-vault.png)

    Il pannello **Macchine virtuali di Azure** elenca l'ultimo processo di backup per ogni elemento. In questo esempio è presente una sola macchina virtuale, demovm-markgal, protetta da questo insieme di credenziali.

    ![Riquadro Elementi di backup](./media/backup-azure-manage-vms/backup-items-blade.png)

    >[AZURE.TIP] Per facilitare l'accesso, è possibile aggiungere un elemento dell'insieme di credenziali al dashboard di Azure. Per aggiungere un elemento dell'insieme di credenziali, nell'elenco di insiemi di credenziali fare clic con il pulsante destro del mouse sull'elemento desiderato e scegliere **Aggiungi al dashboard**.

2. Nel pannello **Elementi di backup** fare clic sull'elemento per aprire il dashboard dell'elemento dell'insieme di credenziali.

    ![Riquadro Elementi di backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Verranno visualizzati il dashboard dell'elemento dell'insieme di credenziali e il relativo pannello **Impostazioni**.

    ![Dashboard degli elementi di backup con il pannello Impostazioni](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    Dal dashboard dell'elemento dell'insieme di credenziali è possibile eseguire molte attività di gestione importanti, ad esempio:

    - Modificare i criteri o creare nuovi criteri di backup<br>
	- Visualizzare i punti di ripristino e vedere il relativo stato di coerenza <br>
	- Eseguire il backup su richiesta di una macchina virtuale <br>
	- Arrestare la protezione delle macchine virtuali <br>
	- Riprendere la protezione delle macchine virtuali <br>
	- Eliminare i dati di un backup o un punto di ripristino <br>
	- [Ripristinare un backup o un punto di ripristino](./backup-azure-arm-restore-vms.md#restore-a-recovery-point) <br>

Per le procedure seguenti, il punto di partenza è il dashboard dell'elemento dell'insieme di credenziali.

## Modificare i criteri o creare nuovi criteri di backup

1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard) fare clic su **Tutte le impostazioni** per aprire il pannello **Impostazioni**.

    ![Pannello Criteri di backup](./media/backup-azure-manage-vms/all-settings-button.png)

2. Nel pannello **Impostazioni** fare clic su **Criteri di backup** per aprire il pannello corrispondente.

    Nel pannello vengono visualizzati i dettagli relativi a intervallo di conservazione e frequenza di backup.

    ![Pannello Criteri di backup](./media/backup-azure-manage-vms/backup-policy-blade.png)

3. Dal menu **Scegliere i criteri di backup**:
    - Per modificare i criteri, selezionare criteri diversi e fare clic su **Salva**. I nuovi criteri verranno applicati immediatamente all'insieme di credenziali. <br>
    - Per creare nuovi criteri, selezionare **Crea nuovo**.

    ![Backup di una macchina virtuale](./media/backup-azure-manage-vms/backup-policy-create-new.png)

    Per istruzioni sulla creazione di criteri di backup, vedere [Definizione di un criterio di backup](backup-azure-manage-vms.md#defining-a-backup-policy).


## Backup su richiesta di una macchina virtuale
È possibile eseguire il backup su richiesta di una macchina virtuale dopo averla configurata per la protezione. Se il backup iniziale della macchina virtuale è in sospeso, il backup su richiesta creerà una copia completa della macchina virtuale nell'insieme di credenziali di Servizi di ripristino. Se il backup iniziale viene completato, un backup su richiesta invierà solo le modifiche dello snapshot precedente all'insieme di credenziali di Servizi di ripristino, ovvero è sempre incrementale.

>[AZURE.NOTE] L'intervallo di conservazione per un backup su richiesta è il valore di conservazione specificato nei criteri per il punto di backup giornaliero. Se il punto di backup giornaliero non è selezionato, verrà usato quello settimanale.

Per attivare un backup su richiesta di una macchina virtuale:

1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard), fare clic su **Esegui backup ora**.

    ![Pulsante Esegui backup ora](./media/backup-azure-manage-vms/backup-now-button.png)

    Il portale richiede di confermare l'avvio del processo di backup su richiesta. Per avviarlo, fare clic su **Sì**.

    ![Pulsante Esegui backup ora](./media/backup-azure-manage-vms/backup-now-check.png)

    Il processo di backup crea un nuovo punto di ripristino. L'intervallo di conservazione del punto di ripristino creato è uguale a quello specificato nei criteri associati alla macchina virtuale. Per tenere traccia dell'avanzamento del processo, nel dashboard dell'insieme di credenziali fare clic sul riquadro **Processi di backup**.


## Arrestare la protezione delle macchine virtuali
Se si sceglie di arrestare la protezione di una macchina virtuale, viene chiesto se si vogliono mantenere i punti di ripristino. Esistono due modi per arrestare la protezione delle macchine virtuali in due modi: arrestare tutti i processi di backup futuri ed eliminare tutti i punti di ripristino o arrestare tutti i processi di backup futuri, ma lasciare i punti di ripristino. Al mantenimento dei punti di ripristino nella risorsa di archiviazione è associato un costo. Tuttavia, la possibilità di mantenere i punti di ripristino per ripristinare la macchina virtuale in un secondo momento, se necessario, costituisce un vantaggio. Per i dettagli relativi ai prezzi per queste macchine virtuali, fare clic [qui](https://azure.microsoft.com/pricing/details/backup/). Se si sceglie di eliminare tutti i punti di ripristino, non sarà possibile ripristinare la macchina virtuale.

Per arrestare la protezione per una macchina virtuale:

1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard) fare clic su **Arresta backup**.

    ![Pulsante Arresta backup](./media/backup-azure-manage-vms/stop-backup-button.png)

    Verrà visualizzato il pannello Arresta backup.

    ![Pannello Arresta backup](./media/backup-azure-manage-vms/stop-backup-blade.png)

2. Nel pannello **Arresta backup** scegliere se mantenere o eliminare i dati di backup. La casella delle informazioni include i dettagli sulla scelta effettuata.

    ![Arresta protezione](./media/backup-azure-manage-vms/retain-or-delete-option.png)

3. Se si è scelto di mantenere i dati di backup, andare al passaggio 4. Se si è scelto di eliminare i dati di backup, confermare l'arresto dei processi di backup ed eliminare i punti di ripristino, digitando il nome dell'elemento.

    ![Arresta verifica](./media/backup-azure-manage-vms/item-verification-box.png)

    Se non si è certi del nome dell'elemento, passare il puntatore sul punto esclamativo per visualizzarlo. Il nome dell'elemento si trova anche in **Arresta backup** nella parte superiore del pannello.

4. Facoltativamente specificare un **Motivo** o un **Commento**.

5. Per arrestare il processo di backup per l'elemento corrente, fare clic su ![Pulsante Arresta backup](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Un messaggio di notifica informa che i processi di backup sono stati arrestati.

    ![Conferma arresto protezione](./media/backup-azure-manage-vms/stop-message.png)


## Riprendere la protezione di una macchina virtuale
Se è stata scelta l'opzione **Conserva i dati di backup** quando è stata arrestata la protezione della macchina virtuale, si potrà riprendere la protezione. Se è stata scelta l'opzione **Elimina i dati di backup**, non si potrà riprendere la protezione della macchina virtuale.

Per riprendere la protezione della macchina virtuale

1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard) fare clic su **Riprendi backup**.

    ![Riprendere la protezione](./media/backup-azure-manage-vms/resume-backup-button.png)

    Verrà visualizzato il pannello Criteri di backup.

    >[AZURE.NOTE] Quando si riattiva la protezione della macchina virtuale, è possibile scegliere un criterio diverso rispetto ai criteri con cui la macchina virtuale è stata protetta inizialmente.

2. Seguire i passaggi in [Modificare i criteri o creare nuovi criteri di backup](backup-azure-manage-vms.md#change-policies-or-create-a-new-backup-policy) per assegnare i criteri alla macchina virtuale.

    Una volta applicati i criteri di backup alla macchina virtuale, verrà visualizzato il messaggio seguente.

    ![Macchina virtuale protetta correttamente](./media/backup-azure-manage-vms/success-message.png)

## Eliminare i dati di backup
È possibile eliminare i dati di backup associati a una macchina virtuale durante il processo **Arresta backup** o in qualsiasi momento dopo aver arrestato i backup. Se risulta utile, è possibile consentire l'arresto del processo di backup per una macchina virtuale e attendere giorni o settimane prima di decidere se eliminare i punti di ripristino. A differenza del recupero dei punti di ripristino, quando si eliminano i dati di backup, non è possibile scegliere di eliminare punti di ripristino specifici. Se si sceglie di eliminare i dati, vengono eliminati tutti i punti di ripristino associati all'elemento.

Nella procedura seguente si presuppone che il processo di backup per la macchina virtuale sia stato arrestato o disabilitato. Le opzioni **Riprendi backup** ed **Elimina backup** saranno disponibili nel dashboard dell'elemento dell'insieme di credenziali solo dopo avere disabilitato il processo di backup.

![Pulsanti Riprendi ed Elimina](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Per eliminare i dati di backup in una macchina virtuale con il *backup disabilitato*:

1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard) fare clic su **Elimina backup**.

    ![Tipo macchina virtuale](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    Verrà visualizzato il pannello **Elimina dati di backup**.

    ![Tipo macchina virtuale](./media/backup-azure-manage-vms/delete-backup-blade.png)

2. È necessario digitare il nome dell'articolo per confermare l'eliminazione dei punti di ripristino.

    ![Arresta verifica](./media/backup-azure-manage-vms/item-verification-box.png)

    Se non si è certi del nome dell'elemento, passare il puntatore sul punto esclamativo per visualizzarlo. Il nome dell'elemento si trova anche in **Elimina dati backup** nella parte superiore del pannello.

3. Facoltativamente specificare un **Motivo** o un **Commento**.

4. Per eliminare i dati di backup per l'elemento corrente, fare clic su ![Pulsante Arresta backup](./media/backup-azure-manage-vms/delete-button.png)

    Un messaggio di notifica informa che i dati di backup sono stati eliminati.

## Operazioni di controllo
È possibile esaminare i log operazioni ed eventi per vedere le operazioni di gestione eseguite nell'insieme di credenziali di Servizi di ripristino. I log operazioni offrono un ottimo supporto per i controlli e le relazioni finali sulle operazioni di backup. Per visualizzare i log per tutte le operazioni *nella sottoscrizione*, si può usare la funzionalità Log di controllo . Per altre informazioni sui log eventi, operazioni e di controllo, vedere l'articolo [Visualizzare eventi e log di controllo](../azure-portal/insights-debugging-with-events.md). Usare l'impostazione **Log di controllo** per visualizzare i log eventi e operazioni specifici di un insieme di credenziali di Servizi di ripristino o a un elemento dell'insieme di credenziali.

Le operazioni seguenti vengono registrate in Log di controllo:

- Registra
- Annulla registrazione
- Configura protezione
- Backup (sia backup pianificato che su richiesta )
- Ripristino
- Arresta protezione
- Elimina dati di backup
- Aggiungi criteri
- Elimina criteri
- Aggiorna criteri
- Annulla processo

Per visualizzare i log eventi per un insieme di credenziali di Servizi di ripristino:

1. Nel [dashboard dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard) individuare e fare clic su **Log di controllo** per aprire il pannello **Eventi**.

    ![Log di controllo](./media/backup-azure-manage-vms/audit-logs.png)

    Verrà visualizzato il pannello Eventi con gli eventi operativi filtrati solo per l'insieme di credenziali corrente. Il pannello mostra l'elenco di eventi critici, di errore, di avviso e informativi che si sono verificati nell'ultima settimana. L'intervallo di tempo è un valore predefinito specificato nelle impostazioni di **Filtro**. Il pannello **Eventi** mostra anche un grafico a barre che tiene traccia degli eventi che si sono verificati. Se non si vuole visualizzare il grafico a barre, scegliere **Mostra grafico** dal menu **Eventi** per disattivare il grafico.

    ![Filtro di Log di controllo](./media/backup-azure-manage-vms/audit-logs-filter.png)

2. Per altre informazioni su una particolare operazione, nell'elenco delle operazioni fare clic su tale operazione per aprire il pannello corrispondente. Il pannello contiene informazioni dettagliate sull'operazione e un elenco di eventi che si sono verificati durante l'intervallo di tempo.

    ![Dettagli operazione](./media/backup-azure-manage-vms/audit-logs-details-window.png)

3. Per visualizzare informazioni dettagliate su un evento specifico, nell'elenco di eventi fare clic sull'operazione per aprire il pannello dei dettagli.

    ![Dettagli degli eventi](./media/backup-azure-manage-vms/audit-logs-details-window-deep.png)

    I dettagli delle informazioni a livello di evento riflettono le informazioni registrate. Nella parte restante di questa procedura viene illustrato come modificare o cambiare le informazioni disponibili.

4. Per modificare l'elenco dei filtri disponibili, scegliere **Filtro** dal menu **Eventi** per aprire il pannello corrispondente.

    ![Aprire il pannello del filtro](./media/backup-azure-manage-vms/audi-logs-filter-button.png)

5. Nel pannello **Filtro** regolare i filtri **Livello**, **Intervallo di tempo** e **Chiamante**. Gli altri filtri non sono disponibili perché sono stati impostati per fornire le informazioni correnti per l'insieme di credenziali di Servizi di ripristino.

    ![Dettagli della query sui Log di controllo](./media/backup-azure-manage-vms/filter-blade.png)

    È possibile specificare il **Livello** dell'evento: Critico, Errore, Avviso o Informativo. È possibile scegliere qualsiasi combinazione di livelli dell'evento, ma è necessario avere selezionato almeno un livello. Attivare o disattivare il livello. Il filtro **Intervallo di tempo** consente di specificare il periodo di tempo per l'acquisizione degli eventi. Se si usa un intervallo di tempo personalizzato, è possibile impostare l'ora di inizio e di fine.

6. Quando si è pronti per eseguire una query sul log operazioni tramite il filtro, fare clic su **Aggiorna**. I risultati vengono visualizzati nel pannello **Eventi**.

    ![Dettagli operazione](./media/backup-azure-manage-vms/edited-list-of-events.png)


## Notifiche di avviso
Nel portale è possibile ottenere notifiche di avviso personalizzate per i processi. A questo scopo, definire le regole di avviso basate su PowerShell negli eventi dei log operativi. Usare *PowerShell versione 1.3.0 o successiva*.

Per definire una notifica di avviso personalizzata per gli errori di backup, usare un comando simile al seguente:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: è possibile ottenere queste informazioni dai Log di controllo. Il ResourceId è disponibile nella colonna Risorsa dei Log operazioni.

**OperationName**: sarà indicato nel formato Microsoft.RecoveryServices/recoveryServicesVault/<EventName>", dove EventName può essere uno dei seguenti: Register, Unregister, ConfigureProtection, Backup, Restore, StopProtection, DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy, UpdateProtectionPolicy.

**Status**: i valori supportati sono Started, Succeeded e Failed.

**ResourceGroup**: si tratta del gruppo di risorse a cui appartiene la risorsa. È possibile aggiungere la colonna Gruppo di risorse ai log generati. Gruppo di risorse è uno dei tipi di informazioni sugli eventi disponibili.

**Name**: nome della regola di avviso.

**CustomEmail**: specificare l'indirizzo di posta elettronica del cliente a cui dovrà essere inviata le notifica di avviso.

**SendToServiceOwners**: questa opzione invia la notifica dell'avviso a tutti gli amministratori e i coamministratori della sottoscrizione. Può essere usata nel cmdlet **New AzureRmAlertRuleEmail**.

### Limitazioni per gli avvisi
Gli avvisi basati su eventi sono soggetti alle limitazioni seguenti:

1. Gli avvisi vengono attivati in tutte le macchine virtuali nell'insieme di credenziali di Servizi di ripristino. Non è possibile personalizzare l'avviso per un set specifico di macchine virtuali in un insieme di credenziali di Servizi di ripristino.
2. Questa funzionalità è in anteprima. [Altre informazioni](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. Gli avvisi generati vengono inviati da "alerts-noreply@mail.windowsazure.com". Attualmente non è possibile modificare il mittente del messaggio di posta elettronica.

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## Passaggi successivi

Per informazioni su come ricreare una macchina virtuale da un punto di ripristino, vedere [Ripristinare macchine virtuali in Azure](backup-azure-restore-vms.md). Se occorrono informazioni sulla protezione delle macchine virtuali, vedere [Primi passi: eseguire il backup di VM di Azure Resource Manager in un insieme di credenziali dei servizi di ripristino](backup-azure-vms-first-look-arm.md).

<!---HONumber=AcomDC_0608_2016-->