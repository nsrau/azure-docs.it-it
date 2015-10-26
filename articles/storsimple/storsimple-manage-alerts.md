<properties 
   pageTitle="Consente di visualizzare e gestire gli avvisi di StorSimple | Microsoft Azure"
   description="Vengono descritte le condizioni di avviso StorSimple e la loro gravità, come configurare le notifiche di avviso e come utilizzare il servizio StorSimple Manager per gestire gli avvisi."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/15/2015"
   ms.author="v-sharos" />

# Utilizzare il servizio StorSimple Manager per visualizzare e gestire gli avvisi di StorSimple.

## Panoramica

La scheda **Avvisi** del servizio StorSimple Manager fornisce un modo per esaminare e deselezionare gli avvisi correlati ai dispositivi StorSimple in tempo reale. In questa scheda è possibile monitorare in modo centralizzato l'integrità dei dispositivi StorSimple e della soluzione Microsoft Azure StorSimple in generale.

In questa esercitazione vengono descritte le condizioni di avviso comuni e i livelli di gravità dell'avviso e viene illustrato come si configurano le notifiche di avviso. Sono inoltre incluse le tabelle di riferimento rapido degli avvisi che consentono di individuare rapidamente uno specifico avviso e rispondere in modo appropriato.

![Pagina degli avvisi](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## Condizioni di avviso comuni

Il dispositivo StorSimple genera avvisi in risposta a una varietà di condizioni. Di seguito sono indicati i tipi più comuni delle condizioni di avviso:

- **Problemi hardware**: questi avvisi offrono informazioni sull'integrità dell'hardware. Consentono di sapere se sono necessari aggiornamenti del firmware, se un'interfaccia di rete presenta problemi oppure se si è verificato un problema con una delle unità dati.

- **Problemi di connettività**: questi avvisi vengono generati in caso di difficoltà di trasferimento dei dati tra le origini dati. Possono verificarsi problemi di comunicazione durante il trasferimento dei dati da un account di archiviazione di Azure e viceversa oppure a causa della mancanza di connettività tra i dispositivi e il servizio StorSimple Manager. Possono verificarsi durante i processi di configurazione, backup o ripristino. I problemi di comunicazione sono tra i più difficili da risolvere poiché sono presenti numerosi punti di errore. Verificare sempre che la connettività di rete e l'accesso a Internet siano disponibili prima di proseguire con la risoluzione dei problemi più avanzati.

- **Problemi di prestazioni**: questi avvisi vengono generati quando le prestazioni del sistema non sono ottimali, ad esempio in presenza di un forte carico.

Inoltre, possono essere generati avvisi relativi a sicurezza, aggiornamenti o errori di processi.

## Livelli di gravità dell'avviso

Gli avvisi possono avere diversi livelli di gravità, in base all'impatto determinato dalla situazione di avviso e alla necessità di una risposta all'avviso. I livelli di gravità sono:

- **Critico**: questo avviso viene generato in risposta a una condizione che influenza negativamente le prestazioni del sistema. È richiesta un'azione per garantire che il servizio StorSimple non venga interrotto.

- **Avviso**: questa condizione potrebbe diventare critica se non viene risolta. È necessario analizzare la situazione ed eseguire qualsiasi azione necessaria per eliminare il problema.

- **Informazioni**: questo avviso contiene informazioni che possono essere utili per la verifica e la gestione del sistema.

## Configurare le impostazioni degli avvisi

È possibile scegliere se si desidera ricevere le notifiche delle condizioni di avviso tramite posta elettronica per ciascun dispositivo StorSimple. Inoltre, è possibile identificare altri destinatari delle notifiche di avviso immettendo i relativi indirizzi di posta elettronica nella casella **ALTRI DESTINATARI DI POSTA ELETTRONICA**, separandoli con un punto e virgola.

>[AZURE.NOTE]È possibile immettere un massimo di 20 indirizzi di posta elettronica per ogni dispositivo.

Dopo aver attivato la notifica di posta elettronica per un dispositivo, i membri dell'elenco delle notifiche riceveranno un messaggio di posta elettronica ogni volta che si verifica un avviso critico. I messaggi verranno inviati da * *storsimple-alerts-noreply@mail.windowsazure.com* e contengono una descrizione della condizione di avviso. I destinatari possono fare clic su **Annulla sottoscrizione** per essere rimossi dall'elenco delle notifiche di posta elettronica.

#### Per abilitare la notifica di posta elettronica degli avvisi per un dispositivo

1. Andare a **Dispositivi** > **Configura** per il dispositivo.

2. In **Impostazioni avvisi** impostare quanto segue:

    1. Nel campo **INVIA NOTIFICA DI POSTA ELETTRONICA**, selezionare **SÌ**.

    2. Nel campo **INVIA MESSAGGIO DI POSTA ELETTRONICA AGLI AMMINISTRATORI DEL SERVIZIO**, selezionare **SÌ** se si desidera che l'amministratore del servizio e tutti i co-amministratori ricevano le notifiche di avviso.

    3. Nel campo **ALTRI DESTINATARI DI POSTA ELETTRONICA** immettere gli indirizzi di posta elettronica di tutti gli altri destinatari che devono ricevere le notifiche di avviso. Immettere i nomi nel formato **someone@somewhere.com*. Utilizzare il punto e virgola per separare gli indirizzi di posta elettronica. È possibile configurare un massimo di 20 indirizzi di posta elettronica per ogni dispositivo.

    ![Pagina di configurazione della notifica degli avvisi](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig.png)

3. Per inviare una notifica di posta elettronica di prova, fare clic sull'icona della freccia accanto a **INVIA UN MESSAGGIO DI POSTA ELETTRONICA DI PROVA**. Il servizio StorSimple Manager visualizza i messaggi di stato e inoltra la notifica di prova.

4. Quando viene visualizzato il messaggio seguente, fare clic su **OK**.

    ![Messaggio di posta elettronica di prova della notifica di avviso inviato](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE]Se il messaggio di notifica di prova non può essere inviato, il servizio StorSimple Manager visualizza un messaggio appropriato. Ciò può verificarsi a causa del traffico o di altri problemi di rete. Fare clic su **OK**, attendere alcuni minuti e provare a inviare nuovamente il messaggio di notifica di prova.

## Visualizzare e tenere traccia degli avvisi

Il dashboard del servizio StorSimple Manager offre un rapido riepilogo del numero di avvisi dei dispositivi, suddivisi per livello di gravità.

![Dashboard degli avvisi](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Scegliere il livello di gravità per aprire la scheda **Avvisi**. I risultati includono solo gli avvisi che corrispondono al livello di gravità scelto.

![Report avvisi in base al tipo di avviso](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Fare clic su un avviso nell'elenco per visualizzare ulteriori dettagli sull'avviso, inclusi l'ora di segnalazione dell'ultimo avviso, il numero di occorrenze dell'avviso sul dispositivo e l'azione consigliata per risolvere l'avviso. Se si tratta di un avviso relativo all'hardware, viene inoltre identificato il componente hardware.

![Esempio di avviso hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Se si desidera tenere traccia della risoluzione degli avvisi in un file separato o se è necessario inviare le informazioni al supporto tecnico Microsoft, è possibile copiare i dettagli dell'avviso in un file di testo. Dopo aver risolto la condizione di avviso, è necessario cancellare l'avviso dal dispositivo, selezionando l'avviso nella scheda **Avvisi** e facendo clic su **Cancella**. Per cancellare più avvisi, premere il tasto CTRL mentre si selezionano gli avvisi e quindi fare clic su **Cancella**. Si noti che alcuni avvisi vengono automaticamente cancellati quando il problema viene risolto oppure quando il sistema aggiorna l'avviso con nuove informazioni.

Quando si fa clic su **Cancella** si ha la possibilità di fornire commenti sull'avviso e i passaggi eseguiti per risolvere il problema. Alcuni eventi verranno cancellati dal sistema se un altro evento viene attivato con nuove informazioni. In tal caso, viene inviato il messaggio seguente.

![Cancellare il messaggio di avviso](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## Ordinare e rivedere gli avvisi

Può risultare più efficiente eseguire i report sugli avvisi in modo da poterli esaminare e cancellare in gruppi. Inoltre, nella scheda **Avvisi** possono essere visualizzati 250 avvisi al massimo. Se è stato superato il numero di avvisi, non tutti gli avvisi verranno inclusi nella visualizzazione predefinita. È possibile combinare i campi seguenti per personalizzare quali avvisi vengono visualizzati:

- **Stato**: è possibile visualizzare gli avvisi di tipo **Attivo** o **Deselezionato**. Gli avvisi attivi vengono ancora attivati nel sistema, mentre gli avvisi deselezionati sono stati cancellati manualmente da un amministratore o a livello di programmazione, in quanto la condizione di avviso è stata aggiornata dal sistema con nuove informazioni.

- **Gravità**: è possibile visualizzare gli avvisi di tutti i livelli di gravità (critico, avviso, informazioni) o solo di una determinata gravità, ad esempio solo gli avvisi critici.

- **Origine**: è possibile visualizzare gli avvisi generati da tutte le origini o limitare gli avvisi a quelli che provengono dal servizio oppure da uno o tutti i dispositivi.

- **Intervallo di tempo**: specificando le date e i timestamp in **Da** e **A**, è possibile esaminare gli avvisi generati durante il periodo di tempo specificato.

## Riferimento rapido degli avvisi

Nelle tabelle seguenti sono elencati alcuni degli avvisi di Microsoft Azure StorSimple che potrebbero verificarsi, nonché consigli e informazioni aggiuntive eventualmente disponibili. Gli avvisi del dispositivo StorSimple rientrano in una delle seguenti categorie:

- [Avvisi di connettività cloud](#cloud-connectivity-alerts)

- [Avvisi di cluster](#cluster-alerts)

- [Avvisi di ripristino di emergenza](#disaster-recovery-alerts)

- [Avvisi di hardware](#hardware-alerts)

- [Avvisi di errore di processo](#job-failure-alerts)

- [Avvisi di prestazioni](#performance-alerts)

- [Avvisi di sicurezza](#security-alerts)

- [Avvisi del pacchetto per il supporto](#support-package-alerts)

- [Avvisi di prova](#test-alerts)

- [Avvisi di aggiornamento](#update-alerts)

### Avvisi di connettività cloud

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|Non è possibile stabilire la connessione a <*nome credenziali cloud*>.|Impossibile connettersi all'account di archiviazione.|Potrebbe essersi verificato un problema di connettività con il dispositivo. Eseguire il `Test-HcsmConnection` cmdlet dall'interfaccia di Windows PowerShell per StorSimple nel dispositivo per identificare e risolvere il problema. Se le impostazioni sono corrette, il problema potrebbe essere relativo alle credenziali dell'account di archiviazione per cui è stato generato l'avviso. In questo caso, utilizzare il `Test-HcsStorageAccountCredential` cmdlet per determinare se sono presenti problemi che possono essere risolti.<ul><li>Controllare le impostazioni di rete.</li><li>Controllare le credenziali dell'account di archiviazione.</li></ul>|
|Nessun heartbeat ricevuto dal dispositivo negli ultimi <*numero*> minuti.|Impossibile connettersi al dispositivo.|Potrebbe essersi verificato un problema di connettività con il dispositivo. Utilizzare il `Test-HcsmConnection` cmdlet dall'interfaccia di Windows PowerShell per StorSimple nel dispositivo per identificare e risolvere il problema o contattare l'amministratore di rete.|

### StorSimple comportamento quando si verifica un errore di connettività cloud

Cosa accade se si verifica un errore di connettività cloud per il dispositivo StorSimple in esecuzione nell'ambiente di produzione?

Se la connettività cloud non riesce sul dispositivo StorSimple di produzione, quindi a seconda dello stato del dispositivo, può verificarsi quanto segue:

- **Per i dati locali nel dispositivo**: non vi sarà alcuna interruzione del servizio e letture continueranno a essere serviti. Tuttavia, come il numero di IOs attesa aumenta e supera un limite, le operazioni di lettura è possibile avviare esito negativo. 

	A seconda della quantità di dati nei livelli locale del dispositivo, le operazioni di scrittura anche continuerà a verificarsi per prima alcune ore dopo l'interruzione della connettività cloud. Le operazioni di scrittura verrà quindi rallentano e alla fine iniziano ad avere esito negativo se viene interrotta la connettività cloud per diverse ore.

 
- **Per i dati nel cloud**: per la maggior parte degli errori di connettività cloud, viene restituito un errore. Una volta la connettività viene ripristinata, senza che sia necessario portare il volume in linea viene ripresi IOs. In rari casi, potrebbe essere necessario riportare in linea il volume dal portale di Azure l'intervento dell'utente.
 
- **Per gli snapshot cloud in corso**: l'operazione verrà ripetuta alcune volte all'interno di 4-5 ore e se non viene ripristinata la connettività, gli snapshot cloud avrà esito negativo.


### Avvisi di cluster

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|Failover del dispositivo su <*nome dispositivo*>.|Il dispositivo è in modalità di manutenzione.|Failover del dispositivo a causa dell'ingresso o dell'uscita dalla modalità manutenzione. Si tratta di un comportamento normale e non è richiesto alcun intervento. Dopo avere confermato la ricezione dell'avviso, cancellarlo dalla pagina degli avvisi.|
|Failover del dispositivo su <*nome dispositivo*>.|Il software o il firmware del dispositivo è stato appena aggiornato.|Failover del cluster a causa di un aggiornamento. Si tratta di un comportamento normale e non è richiesto alcun intervento. Dopo avere confermato la ricezione dell'avviso, cancellarlo dalla pagina degli avvisi.|
|Failover del dispositivo su <*nome dispositivo*>.|Il controller è stato arrestato o riavviato.|Failover del dispositivo a causa dell'arresto o del riavvio del controller attivo da parte di un amministratore. Non è richiesto alcun intervento. Dopo avere confermato la ricezione dell'avviso, cancellarlo dalla pagina degli avvisi.|
|Failover del dispositivo su <*nome dispositivo*>.|Failover pianificato.|Verificare che si tratta di un failover pianificato. Dopo aver eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi.|
|Failover del dispositivo su <*nome dispositivo*>.|Failover non pianificato.|StorSimple è progettato per eseguire il ripristino automatico dai failover non pianificati. Se viene visualizzato un numero elevato di questi avvisi, contattare il supporto tecnico Microsoft.|
|Failover del dispositivo su <*nome dispositivo*>.|Causa diversa/sconosciuta.|Se viene visualizzato un numero elevato di questi avvisi, contattare il supporto tecnico Microsoft. Dopo aver risolto il problema, cancellare questo avviso dalla pagina degli avvisi.|

### Avvisi di ripristino di emergenza

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|Non è possibile ripristinare tutte le impostazioni per questo servizio. I dati di configurazione del dispositivo si trovano in uno stato incoerente per alcuni dispositivi.|Incoerenza dei dati rilevata dopo il ripristino di emergenza.|I dati crittografati del servizio non sono sincronizzati con quelli sul dispositivo. Autorizzare il dispositivo <*nome dispositivo*> da StorSimple Manager per avviare il processo di sincronizzazione. Utilizzare l'interfaccia di Windows PowerShell per StorSimple per eseguire il `Restore-HcsmEncryptedServiceData` sul dispositivo <*nome dispositivo*> cmdlet, fornire la vecchia password come input per questo cmdlet per ripristinare il profilo di sicurezza. Eseguire quindi il `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet per aggiornare la chiave DEK del servizio. Dopo aver eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi.|
|Failover del servizio in un data center secondario a causa di un errore imprevisto.|Causa diversa/sconosciuta.|Per continuare, è necessario verificare le impostazioni di configurazione nel servizio StorSimple Manager. Dopo aver eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi. Per ulteriori informazioni su StorSimple Manager, vedere il [servizio StorSimple Manager di utilizzare per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).|

### Avvisi di hardware

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|Lo stato del componente hardware <*ID componente*> è segnalato come <*status*>.||Le condizioni temporanee possono talvolta provocare questi avvisi. In tal caso, l'avviso viene cancellato automaticamente dopo un periodo di tempo. Se il problema persiste, contattare il supporto tecnico Microsoft.|
|Malfunzionamento del controller passivo.|Il controller passivo (secondario) non funziona.|Il dispositivo è operativo, ma uno dei controller non funziona correttamente. Provare a riavviare il controller. Se il problema persiste, contattare il supporto tecnico Microsoft.|

### Avvisi di errore di processo

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|Backup di <*ID gruppo di volumi di origine*> non riuscita.|Il processo di backup non è riuscito.|Alcuni problemi di connettività potrebbero impedire il corretto completamento dell'operazione di backup. Se non si rilevano problemi di connettività, è possibile che sia stato raggiunto il numero massimo di backup consentiti. Eliminare i backup non più necessari e ripetere l'operazione. Dopo aver eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi.|
|Clonazione di <*ID elemento backup di origine*> a <*numeri di serie volume di destinazione*> non riuscita.|Il processo di clonazione non è riuscito.|Aggiornare l'elenco dei backup per verificare che il backup sia ancora valido. Se il backup è valido è possibile che problemi di connettività cloud impediscano il corretto completamento dell'operazione di clonazione. Se non si rilevano problemi di connettività, è possibile che sia stato raggiunto il limite di archiviazione. Eliminare i backup non più necessari e ripetere l'operazione. Dopo avere eseguito l'operazione appropriata per risolvere il problema, cancellare questo avviso dalla pagina degli avvisi.|
|Ripristino di <*ID elemento backup di origine*> non riuscita.|Il processo di ripristino non è riuscito.|Aggiornare l'elenco dei backup per verificare che il backup sia ancora valido. Se il backup è valido è possibile che problemi di connettività cloud impediscano il corretto completamento dell'operazione di ripristino. Se non si rilevano problemi di connettività, è possibile che sia stato raggiunto il limite di archiviazione. Eliminare i backup non più necessari e ripetere l'operazione. Dopo avere eseguito l'operazione appropriata per risolvere il problema, cancellare questo avviso dalla pagina degli avvisi.|

### Avvisi di prestazioni

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|Il carico del dispositivo ha superato <*threshold*>.|Tempi di risposta più lenti del previsto.|Il dispositivo segnala un carico di input/output eccessivo e potrebbe non funzionare come previsto. Verificare i carichi di lavoro collegati al dispositivo e determinare se alcuni di essi non sono più necessari o possono essere spostati in un altro dispositivo.|

### Avvisi di sicurezza

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|La sessione del supporto tecnico Microsoft è stata avviata.|Sessione di supporto per l'accesso di terze parti.|Verificare che l'accesso sia autorizzato. Dopo aver eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi.|
|Password per <*elemento*> scadrà tra <*periodo di tempo*>.||Modificare la password prima della scadenza.|
|Informazioni sulla configurazione di protezione mancanti per <*ID elemento*>.||Non è possibile usare i volumi associati a questo contenitore del volume per replicare la configurazione StorSimple. Per garantire che i dati vengano archiviati in modo sicuro, è consigliabile eliminare il contenitore del volume e gli eventuali volumi ad esso associati. Dopo aver eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi.|
|<*numero*> tentativi di accesso non riusciti per <*ID elemento*>.|Più tentativi di accesso non riusciti.|È possibile che il dispositivo sia vittima di un attacco o che un utente autorizzato stia tentando di connettersi con una password non corretta.<ul><li>Contattare gli utenti autorizzati e verificare che questi tentativi provengano da un'origine legittima. Se viene rilevato un numero elevato di tentativi di accesso, provare a disabilitare la gestione remota e a contattare l'amministratore di rete. Dopo avere eseguito l'operazione appropriata, cancellare questo avviso dalla pagina degli avvisi.</li><li>Verificare che le istanze di Gestione snapshot siano configurate con la password corretta. Dopo aver eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi.</li></ul>|
|Si sono verificati uno o più errori durante la modifica della chiave DEK del servizio.||Si sono verificati errori durante la modifica della chiave DEK del servizio. Dopo avere risolto le condizioni di errore, eseguire il `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet dall'interfaccia di Windows PowerShell per StorSimple nel dispositivo per aggiornare il servizio. Se il problema persiste, contattare il supporto tecnico Microsoft. Dopo aver risolto il problema, cancellare questo avviso dalla pagina degli avvisi.|

### Avvisi del pacchetto per il supporto

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|Creazione del pacchetto per il supporto non riuscita.|StorSimple: impossibile generare il pacchetto.|Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft. Dopo aver risolto il problema, cancellare questo avviso dalla pagina degli avvisi.|

### Avvisi di prova

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|Si tratta di un messaggio di prova inviato dal dispositivo StorSimple. L'amministratore di StorSimple ha aggiunto l'utente come destinatario delle notifiche di avviso per il dispositivo: <*nome dispositivo*>.|Messaggio di posta elettronica di prova della notifica di avviso.|Se si ritiene di aver ricevuto per errore questo messaggio, contattare l'amministratore di StorSimple.|

### Avvisi di aggiornamento

|Testo dell'avviso|Evento|Ulteriori informazioni/Azioni consigliate|
|:---|:---|:---|
|Hotfix installato.|L'aggiornamento del software/firmware è stato completato.|L'installazione dell'hotfix nel dispositivo è stata completata.|
|Aggiornamenti manuali disponibili.|Notifica di aggiornamenti disponibili.|Utilizzare l'interfaccia di Windows PowerShell per StorSimple nel dispositivo per installare gli aggiornamenti.|
|Nuovi aggiornamenti disponibili.|Notifica di aggiornamenti disponibili.|È possibile installare questi aggiornamenti dalla pagina **Manutenzione** o tramite l'interfaccia di Windows PowerShell per StorSimple nel dispositivo.|
|Impossibile installare gli aggiornamenti.|Gli aggiornamenti non sono stati installati correttamente.|Non è stato possibile installare gli aggiornamenti. È possibile installare questi aggiornamenti dalla pagina **Manutenzione** o tramite l'interfaccia di Windows PowerShell per StorSimple nel dispositivo. Se il problema persiste, contattare il supporto tecnico Microsoft.|
|Non è possibile verificare automaticamente la disponibilità di aggiornamenti.|Verifica automatica non riuscita.|È possibile verificare manualmente la disponibilità di aggiornamenti dalla pagina **Manutenzione**.|
|È disponibile un nuovo aggiornamento dell'agente di Windows.|Notifica di aggiornamento disponibile.|Scaricare la versione più recente dell'agente di Windows Update e installala dall'interfaccia di Windows PowerShell.|
|La versione del componente <*ID componente*> del firmware non corrisponde a quella dell'hardware.|Gli aggiornamenti del firmware non sono stati installati correttamente.|Contattare il supporto tecnico Microsoft.|

## Passaggi successivi

- Ulteriori informazioni sugli [Errori di StorSimple](storsimple-troubleshoot-operational-device.md).
- Ulteriori informazioni sull’[utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=Oct15_HO3-->