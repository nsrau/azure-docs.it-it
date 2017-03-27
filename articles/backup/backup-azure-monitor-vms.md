---
title: Monitorare i backup delle macchine virtuali distribuite con Resource Manager | Documentazione Microsoft
description: Monitorare gli eventi e gli avvisi dei backup delle macchine virtuali distribuite con Resource Manager. Inviare messaggi di posta elettronica in base agli avvisi.
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: markgal;trinadhk;giridham;
translationtype: Human Translation
ms.sourcegitcommit: e12d533ac5befe020c0aad4aa64ca9ed50349c3d
ms.openlocfilehash: aefc65d42e3c23e35793be8af1751bb48dfaa84f
ms.lasthandoff: 02/17/2017


---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Monitorare gli avvisi per i backup della macchina virtuale di Azure
Gli avvisi sono risposte del servizio che informano che è stata raggiunta o superata la soglia di un evento. Sapere quando i problemi hanno inizio può essere determinante per contenere i costi aziendali. Gli avvisi in genere non seguono una pianificazione e quindi è utile sapere appena possibile quando un avviso viene generato. Quando ad esempio un processo di backup o ripristino non riesce, l'avviso verrà generato entro cinque minuti dall'errore. Nel dashboard dell'insieme di credenziali il riquadro Avvisi di backup visualizza gli eventi di livello critico e avviso. Nelle impostazioni di Avvisi di backup è possibile visualizzare tutti gli eventi. Ma cosa fare se un avviso viene generato mentre si lavora a un problema diverso? Non sapere quando l'avviso viene generato può essere un piccolo inconveniente o può compromettere i dati. Per verificare che chi di dovere sia informato dell'avviso e sappia quando viene generato, configurare il servizio per l'invio tramite posta elettronica di notifiche di avviso. Per informazioni dettagliate sulla configurazione di notifiche di posta elettronica, vedere [Configurare le notifiche](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Dove è possibile trovare informazioni sugli avvisi?
Per visualizzare informazioni sull'evento che ha generato un avviso, è necessario aprire il pannello Avvisi di backup. È possibile aprire il pannello Avvisi di backup in due modi: dal riquadro Avvisi di backup nel dashboard dell'insieme di credenziali o dal pannello Avvisi ed eventi.

Per aprire il pannello Avvisi di backup dal riquadro Avvisi di backup:

* Nel riquadro **Avvisi di backup** nel dashboard dell'insieme di credenziali fare clic su **Critico** o **Avviso** per visualizzare gli eventi operativi per il livello di gravità in questione.

    ![Riquadro Avvisi di backup](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Per aprire il pannello Avvisi di backup dal riquadro Avvisi ed eventi:

1. Nel dashboard dell'insieme di credenziali fare clic su **Tutte le impostazioni**. ![Pulsante Tutte le impostazioni](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Nel pannello **Impostazioni** fare clic su **Avvisi ed eventi**. ![Pulsante Avvisi ed eventi](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Nel pannello **Avvisi ed eventi** fare clic su **Avvisi di backup**. ![Pulsante Avvisi di backup](./media/backup-azure-monitor-vms/backup-alerts.png)

    Il pannello **Avvisi di backup** si apre e visualizza gli avvisi filtrati.

    ![Riquadro Avvisi di backup](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Per visualizzare informazioni dettagliate su un avviso specifico, nell'elenco di eventi fare clic sull'avviso per aprire il pannello **Dettagli** .

    ![Dettagli dell'evento](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Per personalizzare gli attributi visualizzati nell'elenco, vedere [Visualizzare altri attributi degli eventi](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Configurare le notifiche
 È possibile configurare il servizio per l'invio di notifiche di posta elettronica per gli avvisi generati nell'ultima ora o quando si verificano tipi specifici di eventi.

Per configurare le notifiche di posta elettronica per gli avvisi

1. Scegliere **Configurare le notifiche**

    ![Menu Avvisi di backup](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Si apre il pannello Configura notifiche.

    ![Pannello Configura notifiche](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Nel pannello Configura notifiche per Notifiche tramite posta elettronica fare clic su **Sì**.

    Le finestre di dialogo Destinatari e Gravità presentano un asterisco perché tali informazioni sono obbligatorie. Specificare almeno un indirizzo di posta elettronica e selezionare almeno un valore per Gravità.
3. Nella finestra di dialogo **Destinatari (indirizzo di posta elettronica)** digitare gli indirizzi di posta elettronica delle persone che ricevono le notifiche. Usare il formato: username@domainname.com. Separare più indirizzi di posta elettronica con un punto e virgola (;).
4. Nell'area **Notifica** scegliere **Per ogni avviso** per inviare la notifica quando viene generato l'avviso specificato o **Riepilogo orario** per inviare un riepilogo dell'ora passata.
5. Nella finestra di dialogo **Gravità** scegliere uno o più livelli che dovranno attivare la notifica di posta elettronica.
6. Fare clic su **Save**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Quali tipi di avviso sono disponibili per il backup di VM IaaS di Azure?
   | Livello avviso | Avvisi inviati |
   | --- | --- |
   | Critico |Errore di backup, errore di ripristino |
   | Avviso |None |
   | Informazioni |None |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Esistono situazioni in cui il messaggio e-mail non viene inviato anche se sono configurate le notifiche?
Esistono situazioni in cui un avviso non viene inviato anche se le notifiche sono state configurate correttamente. Le notifiche di posta elettronica non vengono inviate nei casi seguenti per ridurre la frequenza degli avvisi:

* Se le notifiche sono configurate per il riepilogo orario e un avviso viene generato e risolto entro l'ora.
* Il processo viene annullato.
* Viene attivato un processo di backup che non riesce ed è in corso un altro processo di backup.
* Viene avviato un processo di backup pianificato per una macchina virtuale basata su Resource Manager, ma la macchina virtuale non esiste più.

## <a name="customize-your-view-of-events"></a>Personalizzare la visualizzazione degli eventi
L'impostazione **Log di controllo** ha un set predefinito di filtri e colonne che mostrano le informazioni sugli eventi operativi. È possibile personalizzare la visualizzazione in modo che, quando il pannello **Eventi** si apre, mostri le informazioni desiderate.

1. Nel [dashboard dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard) cercare e fare clic su **Log di controllo** per aprire il pannello **Eventi**.

    ![Log di controllo](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    Verrà visualizzato il pannello **Eventi** con gli eventi operativi filtrati solo per l'insieme di credenziali corrente.

    ![Filtro di Log di controllo](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    Il pannello mostra l'elenco di eventi critici, di errore, di avviso e informativi che si sono verificati nell'ultima settimana. L'intervallo di tempo è un valore predefinito specificato in **Filtro**. Il pannello **Eventi** mostra anche un grafico a barre che tiene traccia degli eventi che si sono verificati. Se non si vuole visualizzare il grafico a barre, scegliere **Nascondi grafico** dal menu **Eventi** per disattivare il grafico. La visualizzazione predefinita di Eventi mostra l'operazione, il livello, lo stato, la risorsa e le informazioni sull'ora. Per informazioni sull'esposizione di altri attributi degli eventi, vedere la sezione [Visualizzare altri attributi degli eventi](backup-azure-monitor-vms.md#view-additional-event-attributes).
2. Per altre informazioni su un evento operativo, nella colonna **Operazione** fare clic su un evento operativo per aprirne il pannello. Il pannello contiene informazioni dettagliate sugli eventi. Gli eventi vengono raggruppati in base all'ID correlazione e a un elenco degli eventi che si sono verificati nell'intervallo di tempo.

    ![Dettagli operazione](./media/backup-azure-monitor-vms/audit-logs-details-window.png)
3. Per visualizzare informazioni dettagliate su un evento specifico, nell'elenco di eventi fare clic sull'evento per aprire il pannello **Dettagli** .

    ![Dettagli dell'evento](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    I dettagli delle informazioni a livello di evento riflettono le informazioni registrate. Se si preferisce visualizzare tutte queste informazioni su ogni evento e si vuole aggiungere tutti questi dettagli al pannello **Eventi** , vedere la sezione [Visualizzare altri attributi degli eventi](backup-azure-monitor-vms.md#view-additional-event-attributes).

## <a name="customize-the-event-filter"></a>Personalizzare il filtro degli eventi
Usare **Filtro** per modificare o scegliere le informazioni visualizzate in un determinato pannello. Per filtrare le informazioni sugli eventi:

1. Nel [dashboard dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard) cercare e fare clic su **Log di controllo** per aprire il pannello **Eventi**.

    ![Log di controllo](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    Verrà visualizzato il pannello **Eventi** con gli eventi operativi filtrati solo per l'insieme di credenziali corrente.

    ![Filtro di Log di controllo](./media/backup-azure-monitor-vms/audit-logs-filter.png)
2. Scegliere **Filtro** dal menu **Eventi** per aprire tale pannello.

    ![Aprire il pannello del filtro](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)
3. Nel pannello **Filtro** regolare i filtri **Livello**, **Intervallo di tempo** e **Chiamante**. Gli altri filtri non sono disponibili perché sono stati impostati per fornire le informazioni correnti per l'insieme di credenziali di Servizi di ripristino.

    ![Dettagli della query sui Log di controllo](./media/backup-azure-monitor-vms/filter-blade.png)

    È possibile specificare il **Livello** dell'evento: Critico, Errore, Avviso o Informativo. È possibile scegliere qualsiasi combinazione di livelli dell'evento, ma è necessario avere selezionato almeno un livello. Attivare o disattivare il livello. Il filtro **Intervallo di tempo** consente di specificare il periodo di tempo per l'acquisizione degli eventi. Se si usa un intervallo di tempo personalizzato, è possibile impostare l'ora di inizio e di fine.
4. Quando si è pronti per effettuare una query sui log operazioni usando il filtro, fare clic su **Aggiorna**. I risultati vengono visualizzati nel pannello **Eventi** .

    ![Dettagli operazione](./media/backup-azure-monitor-vms/edited-list-of-events.png)

### <a name="view-additional-event-attributes"></a>Visualizzare altri attributi degli eventi
Usando il pulsante **Colonne**, è possibile visualizzare altri attributi degli eventi nell'elenco del pannello **Eventi**. L'elenco predefinito di eventi visualizza informazioni relative a operazione, livello, stato, risorsa e ora. Per abilitare altri attributi:

1. Nel pannello **Eventi** fare clic su **Colonne**.

    ![Aprire le colonne](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    Si apre il pannello **Scegli colonne** .

    ![Pannello delle colonne](./media/backup-azure-monitor-vms/columns-blade.png)
2. Per selezionare l'attributo, fare clic sulla casella di controllo. La casella di controllo di un attributo può essere attivata e disattivata.
3. Fare clic su **Reimposta** per reimpostare l'elenco di attributi nel pannello **Eventi**. Dopo avere aggiunto o rimosso gli attributi dall'elenco, usare **Reimposta** per visualizzare il nuovo elenco di attributi degli eventi.
4. Fare clic su **Aggiorna** per aggiornare i dati negli attributi degli eventi. La tabella seguente fornisce informazioni su ogni attributo.

| Nome colonna | Descrizione |
| --- | --- |
| Operazione |Nome dell'operazione |
| Level |Livello dell'operazione. I valori possono essere: Informativo, Avviso, Errore o Critico. |
| Stato |Stato descrittivo dell'operazione. |
| Risorsa |URL che identifica la risorsa, detto anche ID risorsa. |
| Time |Ora, calcolata dall'ora corrente, in cui si è verificato l'evento. |
| Chiamante |Chi o che cosa ha chiamato o attivato l'evento. Può essere il sistema o un utente. |
| Timestamp |Ora in cui l'elenco è stato attivato. |
| Gruppo di risorse |Gruppo di risorse associato. |
| Tipo di risorsa |Tipo di risorsa interna usata da Resource Manager. |
| ID sottoscrizione |ID sottoscrizione associato. |
| Categoria |Categoria dell'evento. |
| ID correlazione |ID comune per gli eventi correlati. |

## <a name="use-powershell-to-customize-alerts"></a>Usare PowerShell per personalizzare gli avvisi
Nel portale è possibile ottenere notifiche di avviso personalizzate per i processi. A questo scopo, definire le regole di avviso basate su PowerShell negli eventi dei log operativi. Usare *PowerShell versione 1.3.0 o successiva*.

Per definire una notifica di avviso personalizzata per gli errori di backup, usare un comando simile allo script seguente:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.RecoveryServices/recoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/Microsoft.RecoveryServices/vaults/trinadhVault -Actions $actionEmail
```

**ResourceId** : è possibile ottenere ResourceId dai log di controllo. ResourceId è un URL disponibile nella colonna Risorsa dei log operazioni.

**OperationName**: OperationName presenta il formato "Microsoft.RecoveryServices/recoveryServicesVault/*NomeEvento*" dove *NomeEvento* può essere:<br/>

* Register <br/>
* Unregister  <br/>
* ConfigureProtection  <br/>
* Backup  <br/>
* Restore  <br/>
* StopProtection  <br/>
* DeleteBackupData  <br/>
* CreateProtectionPolicy  <br/>
* DeleteProtectionPolicy  <br/>
* UpdateProtectionPolicy  <br/>

**Status** : i valori supportati sono Started, Succeeded o Failed.

**ResourceGroup** : si tratta del gruppo di risorse a cui appartiene la risorsa. È possibile aggiungere la colonna Gruppo di risorse ai log generati. Gruppo di risorse è uno dei tipi di informazioni sugli eventi disponibili.

**Name** : nome della regola di avviso.

**CustomEmail** : specificare l'indirizzo di posta elettronica del cliente a cui dovrà essere inviata una notifica di avviso.

**SendToServiceOwners** : questa opzione invia le notifiche dell'avviso a tutti gli amministratori e i coamministratori della sottoscrizione. Può essere usata nel cmdlet **New AzureRmAlertRuleEmail** .

### <a name="limitations-on-alerts"></a>Limitazioni per gli avvisi
Gli avvisi basati su eventi sono soggetti alle limitazioni seguenti:

1. Gli avvisi vengono attivati in tutte le macchine virtuali nell'insieme di credenziali di Servizi di ripristino. Non è possibile personalizzare l'avviso per un subset di macchine virtuali in un insieme di credenziali di Servizi di ripristino.
2. Questa funzionalità è in anteprima. [Altre informazioni](../monitoring-and-diagnostics/insights-powershell-samples.md#create-alert-rules)
3. Gli avvisi vengono inviati da "alerts-noreply@mail.windowsazure.com". Attualmente non è possibile modificare il mittente del messaggio di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi
I log eventi offrono un ottimo supporto per i controlli e le relazioni finali sulle operazioni di backup. Vengono registrate le operazioni seguenti:

* Register 
* Unregister 
* Configura protezione
* Backup (sia backup pianificato che su richiesta )
* Restore 
* Arresta protezione
* Elimina dati di backup
* Aggiungi criteri
* Elimina criteri
* Aggiorna criteri
* Annulla processo

Per una spiegazione approfondita di eventi, operazioni e log di controllo nei servizi di Azure, vedere l'articolo [Visualizzare eventi e log di controllo](../monitoring-and-diagnostics/insights-debugging-with-events.md).

Per informazioni su come ricreare una macchina virtuale da un punto di ripristino, vedere [Ripristinare macchine virtuali in Azure](backup-azure-restore-vms.md). Per informazioni sulla protezione delle macchine virtuali, vedere [Primo approccio: Proteggere le VM di Azure con un insieme di credenziali dei servizi di ripristino](backup-azure-vms-first-look-arm.md). Per informazioni sulle attività di gestione per i backup di VM, vedere l'articolo [Gestire e monitorare i backup delle macchine virtuali di Azure](backup-azure-manage-vms.md).

