---
title: Visualizzare e gestire gli avvisi per il dispositivo StorSimple serie 8000 | Microsoft Docs
description: "Vengono descritte le condizioni di avviso StorSimple e la loro gravità, come configurare le notifiche di avviso e come usare il servizio Gestione dispositivi StorSimple per gestire gli avvisi."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/22/2017
ms.author: alkohli
ms.openlocfilehash: b7f9a2b7eb3dbf4cc97fac9a410359e068e67eb1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Usare il servizio Gestione dispositivi StorSimple per visualizzare e gestire gli avvisi di StorSimple

## <a name="overview"></a>Panoramica

Il pannello **Avvisi** del servizio Gestione dispositivi StorSimple fornisce un modo per esaminare e deselezionare gli avvisi correlati ai dispositivi StorSimple in tempo reale. Il pannello consente di monitorare in modo centralizzato i problemi di integrità dei dispositivi StorSimple e della soluzione Microsoft Azure StorSimple in generale.

In questa esercitazione vengono descritte le condizioni di avviso comuni e i livelli di gravità dell'avviso e viene illustrato come si configurano le notifiche di avviso. Sono inoltre incluse le tabelle di riferimento rapido degli avvisi che consentono di individuare rapidamente uno specifico avviso e rispondere in modo appropriato.

![Pagina degli avvisi](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Condizioni di avviso comuni

Il dispositivo StorSimple genera avvisi in risposta a una varietà di condizioni. Di seguito sono indicati i tipi più comuni delle condizioni di avviso:

* **Problemi hardware** : questi avvisi offrono informazioni sull'integrità dell'hardware. Consentono di sapere se sono necessari aggiornamenti del firmware, se un'interfaccia di rete presenta problemi oppure se si è verificato un problema con una delle unità dati.
* **Problemi di connettività** : questi avvisi vengono generati in caso di difficoltà di trasferimento dei dati. Possono verificarsi problemi di comunicazione durante il trasferimento dei dati da un account di archiviazione di Azure e viceversa oppure a causa della mancanza di connettività tra i dispositivi e il servizio Gestione dispositivi StorSimple. I problemi di comunicazione sono tra i più difficili da risolvere poiché sono presenti numerosi punti di errore. Verificare sempre che la connettività di rete e l'accesso a Internet siano disponibili prima di proseguire con la risoluzione dei problemi più avanzati. Per informazioni sulla risoluzione dei problemi, vedere [Risoluzione dei problemi con il cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemi di prestazioni** : questi avvisi vengono generati quando le prestazioni del sistema non sono ottimali, ad esempio in presenza di un forte carico.

Inoltre, possono essere generati avvisi relativi a sicurezza, aggiornamenti o errori di processi.

## <a name="alert-severity-levels"></a>Livelli di gravità dell'avviso

Gli avvisi possono avere diversi livelli di gravità, in base all'impatto determinato dalla situazione di avviso e alla necessità di una risposta all'avviso. I livelli di gravità sono:

* **Critico** : questo avviso viene generato in risposta a una condizione che influenza negativamente le prestazioni del sistema. È richiesta un'azione per garantire che il servizio StorSimple non venga interrotto.
* **Avviso** : questa condizione potrebbe diventare critica se non viene risolta. È necessario analizzare la situazione ed eseguire qualsiasi azione necessaria per eliminare il problema.
* **Informazioni** : questo avviso contiene informazioni che possono essere utili per la verifica e la gestione del sistema.

## <a name="configure-alert-settings"></a>Configurare le impostazioni degli avvisi

È possibile scegliere se si desidera ricevere le notifiche delle condizioni di avviso tramite posta elettronica per ciascun dispositivo StorSimple. È anche possibile identificare altri destinatari delle notifiche di avviso immettendo i relativi indirizzi di posta elettronica nella casella **Altri destinatari di posta elettronica** , separandoli con un punto e virgola.

> [!NOTE]
> È possibile immettere un massimo di 20 indirizzi di posta elettronica per ogni dispositivo.

Dopo aver attivato la notifica di posta elettronica per un dispositivo, i membri dell'elenco delle notifiche riceveranno un messaggio di posta elettronica ogni volta che si verifica un avviso critico. I messaggi verranno inviati da *storsimple-alerts-noreply@mail.windowsazure.com* e conterranno una descrizione della condizione di avviso. I destinatari possono fare clic su **Annulla sottoscrizione** per essere rimossi dall'elenco delle notifiche e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Per abilitare la notifica di posta elettronica degli avvisi per un dispositivo
1. Passare al servizio Gestione dispositivi StorSimple. Nell'elenco dei dispositivi, selezionare e fare clic sul dispositivo da configurare.
2. Passare a **Impostazioni** > **Generale** per il dispositivo.

   ![Pannello Avvisi](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Nel pannello **Impostazioni generali** passare alla sezione **Impostazioni avvisi** e impostare le opzioni seguenti:
   
   1. Nel campo **Invia notifica tramite posta elettronica** selezionare **SÌ**.
   2. Nel campo **Invia messaggio di posta elettronica agli amministratori del servizio** selezionare **SÌ** se si vuole che l'amministratore del servizio e tutti i co-amministratori ricevano le notifiche di avviso.
   3. Nel campo **Altri destinatari di posta elettronica** immettere gli indirizzi di posta elettronica di tutti gli altri destinatari che devono ricevere le notifiche di avviso. Immettere i nomi nel formato *someone@somewhere.com*. Utilizzare il punto e virgola per separare gli indirizzi di posta elettronica. È possibile configurare un massimo di 20 indirizzi di posta elettronica per ogni dispositivo. 
      
3. Per inviare una notifica di posta elettronica di prova, fare clic su **Invia messaggio di posta elettronica di prova**. Il servizio Gestione dispositivi StorSimple visualizza i messaggi di stato e li inoltra alla 	notifica test.

    ![Impostazione avviso](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Viene visualizzata una notifica quando il messaggio di posta elettronica di prova viene inviato. 
   
    ![Messaggio di posta elettronica di prova della notifica di avviso inviato](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Se non è possibile inviare il messaggio di notifica di prova, il servizio Gestione dispositivi StorSimple visualizza un messaggio di errore appropriato. Attendere alcuni minuti e provare a inviare nuovamente il messaggio di notifica di prova. 

5. Dopo aver completato la configurazione, fare clic su **Salva**. Alla richiesta di conferma fare clic su **Sì**.

     ![Messaggio di posta elettronica di prova della notifica di avviso inviato](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Visualizzare e tenere traccia degli avvisi

Il pannello di riepilogo del servizio Gestione dispositivi StorSimple offre un rapido riepilogo del numero di avvisi sui dispositivi, organizzati per livello di gravità.

![Dashboard degli avvisi](./media/storsimple-8000-manage-alerts/device-summary4.png)

Scegliere il livello di gravità per aprire il pannello **Avvisi** . I risultati includono solo gli avvisi che corrispondono al livello di gravità scelto.

Fare clic su un avviso nell'elenco per visualizzare ulteriori dettagli sull'avviso, inclusi l'ora di segnalazione dell'ultimo avviso, il numero di occorrenze dell'avviso sul dispositivo e l'azione consigliata per risolvere l'avviso. Se si tratta di un avviso relativo all'hardware, viene inoltre identificato il componente hardware.

![Esempio di avviso hardware](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Se è necessario inviare le informazioni al supporto tecnico Microsoft, è possibile copiare i dettagli dell'avviso in un file di testo. Dopo avere seguito i suggerimenti e risolto la condizione di avviso in locale, è necessario cancellare l'avviso dal dispositivo, selezionando l'avviso nel pannello **Avvisi** e facendo clic su **Cancella**. Per cancellare più avvisi, fare clic su qualsiasi colonna tranne **Avviso**, selezionare tutti gli avvisi da cancellare e fare clic su **Cancella**. Si noti che alcuni avvisi vengono automaticamente cancellati quando il problema viene risolto oppure quando il sistema aggiorna l'avviso con nuove informazioni.

Quando si fa clic su **Cancella**si ha la possibilità di fornire commenti sull'avviso e i passaggi eseguiti per risolvere il problema. Alcuni eventi verranno cancellati dal sistema se un altro evento viene attivato con nuove informazioni. In tal caso, viene inviato il messaggio seguente.

![Cancellare il messaggio di avviso](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Ordinare e rivedere gli avvisi

Può risultare più efficiente eseguire i report sugli avvisi in modo da poterli esaminare e cancellare in gruppi. Inoltre, nel pannello **Avvisi** possono essere visualizzati 250 avvisi al massimo. Se è stato superato il numero di avvisi, non tutti gli avvisi verranno inclusi nella visualizzazione predefinita. È possibile combinare i campi seguenti per personalizzare quali avvisi vengono visualizzati:

* **Stato**: è possibile visualizzare gli avvisi di tipo **Attivo** o **Cancellato**. Gli avvisi attivi vengono ancora attivati nel sistema, mentre gli avvisi deselezionati sono stati cancellati manualmente da un amministratore o a livello di programmazione, in quanto la condizione di avviso è stata aggiornata dal sistema con nuove informazioni.
* **Gravità** : è possibile visualizzare gli avvisi di tutti i livelli di gravità (critico, avviso, informazioni) o solo di una determinata gravità, ad esempio solo gli avvisi critici.
* **Origine** : è possibile visualizzare gli avvisi generati da tutte le origini o limitare gli avvisi a quelli che provengono dal servizio oppure da uno o tutti i dispositivi.
* **Intervallo di tempo**: specificando le date e i timestamp in **Da** e **A**, è possibile esaminare gli avvisi generati durante il periodo di tempo specificato.

![Elenco degli avvisi](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Riferimento rapido degli avvisi

Nelle tabelle seguenti sono elencati alcuni degli avvisi di Microsoft Azure StorSimple che potrebbero verificarsi, nonché consigli e informazioni aggiuntive eventualmente disponibili. Gli avvisi del dispositivo StorSimple rientrano in una delle seguenti categorie:

* [Avvisi di connettività cloud](#cloud-connectivity-alerts)
* [Avvisi di cluster](#cluster-alerts)
* [Avvisi di ripristino di emergenza](#disaster-recovery-alerts)
* [Avvisi di hardware](#hardware-alerts)
* [Avvisi di errore di processo](#job-failure-alerts)
* [Avvisi sul volume aggiunto in locale](#locally-pinned-volume-alerts)
* [Avvisi di rete](#networking-alerts)
* [Avvisi di prestazioni](#performance-alerts)
* [Avvisi di sicurezza](#security-alerts)
* [Avvisi del pacchetto per il supporto](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Avvisi di connettività cloud

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Non è possibile stabilire la connessione a <*nome credenziali cloud*>. |Impossibile connettersi all'account di archiviazione. |Potrebbe essersi verificato un problema di connettività con il dispositivo. Eseguire il cmdlet `Test-HcsmConnection` dall'interfaccia di Windows PowerShell per StorSimple nel dispositivo per identificare e risolvere il problema. Se le impostazioni sono corrette, il problema potrebbe essere relativo alle credenziali dell'account di archiviazione per cui è stato generato l'avviso. In questo caso, usare il cmdlet `Test-HcsStorageAccountCredential` per determinare se sono presenti problemi che possono essere risolti.<ul><li>Verificare le impostazioni di rete.</li><li>Verificare le credenziali dell'account di archiviazione.</li></ul> |
| Nessun heartbeat ricevuto dal dispositivo negli ultimi <*numero*> minuti. |Impossibile connettersi al dispositivo. |Potrebbe essersi verificato un problema di connettività con il dispositivo. Usare il cmdlet `Test-HcsmConnection` dall'interfaccia di Windows PowerShell per StorSimple nel dispositivo per identificare e risolvere il problema o contattare l'amministratore di rete. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple comportamento quando si verifica un errore di connettività cloud

Cosa accade se si verifica un errore di connettività cloud per il dispositivo StorSimple in esecuzione nell'ambiente di produzione?

Se la connettività cloud non riesce sul dispositivo StorSimple di produzione, quindi a seconda dello stato del dispositivo, può verificarsi quanto segue:

* **Per i dati locali nel dispositivo**: per un certo periodo, non ci sarà alcuna interruzione del servizio e le letture continueranno a essere gestite. Tuttavia, come il numero di IOs attesa aumenta e supera un limite, le operazioni di lettura è possibile avviare esito negativo.

    A seconda della quantità di dati nel dispositivo, anche le operazioni di scrittura continueranno a essere eseguite per alcune ore dopo l'interruzione della connettività cloud. Le operazioni di scrittura verrà quindi rallentano e alla fine iniziano ad avere esito negativo se viene interrotta la connettività cloud per diverse ore. Nel dispositivo è presente un archivio temporaneo per i dati di cui è necessario effettuare il push nel cloud. Quest'area viene scaricata quando i dati vengono inviati. Se si verifica un errore di connettività, non verrà effettuato il push nel cloud dei dati in quest'area di archiviazione e l'I/O avrà esito negativo.
* **Per i dati nel cloud**: per la maggior parte dei problemi di connettività cloud, viene restituito un errore. Una volta la connettività viene ripristinata, senza che sia necessario portare il volume in linea viene ripresi IOs. In rari casi, l'intervento dell'utente potrebbe essere necessario per riportare in linea il volume dal portale di Azure.
* **Per gli snapshot cloud in corso**: l'operazione viene ripetuta alcune volte nell'arco di 4-5 ore e, se non viene ripristinata la connettività, gli snapshot cloud avranno esito negativo.

### <a name="cluster-alerts"></a>Avvisi di cluster

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Failover del dispositivo su <*nome dispositivo*>. |Il dispositivo è in modalità di manutenzione. |Failover del dispositivo a causa dell'ingresso o dell'uscita dalla modalità manutenzione. Si tratta di un comportamento normale e non è richiesto alcun intervento. Dopo avere confermato la ricezione dell'avviso, cancellarlo dalla pagina degli avvisi. |
| Failover del dispositivo su <*nome dispositivo*>. |Il software o il firmware del dispositivo è stato appena aggiornato. |Failover del cluster a causa di un aggiornamento. Si tratta di un comportamento normale e non è richiesto alcun intervento. Dopo avere confermato la ricezione dell'avviso, cancellarlo dalla pagina degli avvisi. |
| Failover del dispositivo su <*nome dispositivo*>. |Il controller è stato arrestato o riavviato. |Failover del dispositivo a causa dell'arresto o del riavvio del controller attivo da parte di un amministratore. Non è richiesto alcun intervento. Dopo avere confermato la ricezione dell'avviso, cancellarlo dalla pagina degli avvisi. |
| Failover del dispositivo su <*nome dispositivo*>. |Failover pianificato. |Verificare che si tratta di un failover pianificato. Dopo avere eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi. |
| Failover del dispositivo su <*nome dispositivo*>. |Failover non pianificato. |StorSimple è progettato per eseguire il ripristino automatico dai failover non pianificati. Se viene visualizzato un numero elevato di questi avvisi, contattare il supporto tecnico Microsoft. |
| Failover del dispositivo su <*nome dispositivo*>. |Causa diversa/sconosciuta. |Se viene visualizzato un numero elevato di questi avvisi, contattare il supporto tecnico Microsoft. Dopo avere risolto il problema, cancellare questo avviso dalla pagina degli avvisi. |
| Un servizio critico del dispositivo è segnalato come non riuscito. |Errore del servizio percorso dati. |Contattare il supporto tecnico Microsoft per ottenere assistenza. |
| Lo stato dell'indirizzo IP virtuale per l'interfaccia di rete <*DATA #*> è segnalato come non riuscito. |Causa diversa/sconosciuta. |Le condizioni temporanee possono talvolta provocare questi avvisi. In tal caso, l'avviso viene cancellato automaticamente dopo un periodo di tempo. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| Lo stato dell'indirizzo IP virtuale per l'interfaccia di rete <*DATA #*> è segnalato come non riuscito. |Impossibile connettere l'indirizzo IP per nome interfaccia: <*DATA #*>. <IP address> Nella rete è stato rilevato un indirizzo IP duplicato. |Assicurarsi che l'indirizzo IP duplicato venga rimosso dalla rete o riconfigurare l'interfaccia con un altro indirizzo IP. |

### <a name="disaster-recovery-alerts"></a>Avvisi di ripristino di emergenza

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Non è possibile ripristinare tutte le impostazioni per questo servizio. I dati di configurazione del dispositivo si trovano in uno stato incoerente per alcuni dispositivi. |Incoerenza dei dati rilevata dopo il ripristino di emergenza. |I dati crittografati del servizio non sono sincronizzati con quelli sul dispositivo. Autorizzare il dispositivo <*nome dispositivo*> da Gestione dispositivi StorSimple per avviare il processo di sincronizzazione. Usare l'interfaccia di Windows PowerShell per StorSimple per eseguire il cmdlet `Restore-HcsmEncryptedServiceData` sul dispositivo <*nome dispositivo*>, fornire la vecchia password come input per questo cmdlet per ripristinare il profilo di sicurezza. Eseguire quindi il cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` per aggiornare la chiave DEK del servizio. Dopo avere eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi. |

### <a name="hardware-alerts"></a>Avvisi di hardware

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Lo stato del componente hardware <*ID componente*> è segnalato come <*stato*>. | |Le condizioni temporanee possono talvolta provocare questi avvisi. In tal caso, l'avviso viene cancellato automaticamente dopo un periodo di tempo. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| Malfunzionamento del controller passivo. |Il controller passivo (secondario) non funziona. |Il dispositivo è operativo, ma uno dei controller non funziona correttamente. Provare a riavviare il controller. Se il problema persiste, contattare il supporto tecnico Microsoft. |

### <a name="job-failure-alerts"></a>Avvisi di errore di processo

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Backup di <*ID gruppo di volumi di origine*> non riuscito. |Il processo di backup non è riuscito. |Alcuni problemi di connettività potrebbero impedire il corretto completamento dell'operazione di backup. Se non si rilevano problemi di connettività, è possibile che sia stato raggiunto il numero massimo di backup consentiti. Eliminare i backup non più necessari e ripetere l'operazione. Dopo avere eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi. |
| Clonazione di <*ID elemento backup di origine*> a <*numeri di serie volume di destinazione*> non riuscita. |Il processo di clonazione non è riuscito. |Aggiornare l'elenco dei backup per verificare che il backup sia ancora valido. Se il backup è valido è possibile che problemi di connettività cloud impediscano il corretto completamento dell'operazione di clonazione. Se non si rilevano problemi di connettività, è possibile che sia stato raggiunto il limite di archiviazione. Eliminare i backup non più necessari e ripetere l'operazione. Dopo avere eseguito l'operazione appropriata per risolvere il problema, cancellare questo avviso dalla pagina degli avvisi. |
| Ripristino di <*ID elementi di backup di origine*> non riuscito. |Il processo di ripristino non è riuscito. |Aggiornare l'elenco dei backup per verificare che il backup sia ancora valido. Se il backup è valido è possibile che problemi di connettività cloud impediscano il corretto completamento dell'operazione di ripristino. Se non si rilevano problemi di connettività, è possibile che sia stato raggiunto il limite di archiviazione. Eliminare i backup non più necessari e ripetere l'operazione. Dopo avere eseguito l'operazione appropriata per risolvere il problema, cancellare questo avviso dalla pagina degli avvisi. |

### <a name="locally-pinned-volume-alerts"></a>Avvisi sul volume aggiunto in locale

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Creazione del volume locale <*nome volume*> non riuscita. |Il processo di creazione del volume non è riuscito. <*Messaggio di errore corrispondente al codice di errore non riuscito*>. |Alcuni problemi di connettività potrebbero impedire il corretto completamento dell'operazione di creazione dello spazio. Per i volumi aggiunti in locale viene effettuato il thick provisioning e il processo di creazione dello spazio comporta la distribuzione dei volumi a livelli nel cloud. Se non esistono problemi di connettività, è possibile che lo spazio locale sul dispositivo sia stato esaurito. Determinare se lo spazio sul dispositivo è sufficiente prima di riprovare questa operazione. |
| Espansione del volume locale <*nome volume*> non riuscita. |Il processo di modifica del volume non è riuscito. <*Messaggio di errore corrispondente al codice di errore non riuscito*>. |Alcuni problemi di connettività potrebbero impedire il corretto completamento dell'operazione di espansione del volume. Per i volumi aggiunti in locale viene effettuato il thick provisioning e il processo di estensione dello spazio esistente comporta la distribuzione dei volumi a livelli nel cloud. Se non esistono problemi di connettività, è possibile che lo spazio locale sul dispositivo sia stato esaurito. Determinare se lo spazio sul dispositivo è sufficiente prima di riprovare questa operazione. |
| Conversione del volume <*nome volume*> non riuscita. |Il processo di conversione del volume per convertire il tipo di volume da aggiunto in locale a volume a livelli non è riuscito. |La conversione del volume dal tipo aggiunto in locale a quello a livelli non può essere completata. Assicurarsi che non esistano problemi di connettività che impediscono il completamento dell'operazione. Per informazioni sulla risoluzione dei problemi, vedere [Risoluzione dei problemi con il cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Il volume originale aggiunto in locale ora è contrassegnato come volume a livelli perché alcuni dati del volume aggiunto in locale sono stati distribuiti nel cloud durante la conversione. Il volume a livelli risultante occupa ancora spazio locale sul dispositivo che non può essere recuperato per i futuri volumi locali.<br>Risolvere eventuali problemi di connettività, cancellare l'avviso e riconvertire il volume nel tipo di volume originale aggiunto in locale per assicurarsi che tutti i dati siano resi nuovamente disponibili in locale. |
| Conversione del volume <*nome volume*> non riuscita. |Il processo di conversione del volume per convertire il tipo di volume da a livelli ad aggiunto in locale non è riuscito. |La conversione del volume dal tipo a livelli a quello aggiunto in locale non può essere completata. Assicurarsi che non esistano problemi di connettività che impediscono il completamento dell'operazione. Per informazioni sulla risoluzione dei problemi, vedere [Risoluzione dei problemi con il cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Il volume a livelli originale ora contrassegnato come volume aggiunto in locale come parte del processo di conversione continua a disporre di dati nel cloud, mentre lo spazio con thick provisioning sul dispositivo per questo volume non può più essere recuperato per i volumi locali futuri.<br>Risolvere eventuali problemi di connettività, cancellare l'avviso e riconvertire questo volume nel tipo di volume originale a livelli per assicurarsi che lo spazio locale di cui è stato eseguito il thick provisioning sul dispositivo possa essere recuperato. |
| Consumo di spazio locale quasi esaurito per gli snapshot locali di <*nome gruppo di volumi*> |Gli snapshot locali per i criteri di backup potrebbero esaurire velocemente lo spazio ed essere invalidati per evitare errori di scrittura nell'host. |I frequenti snapshot locali insieme a una varianza dati elevata nei volumi associati a questo gruppo di criteri di backup causano il rapido consumo dello spazio locale sul dispositivo. Eliminare gli snapshot locali che non sono più necessari. Aggiornare anche le pianificazioni degli snapshot locali per questi criteri di backup in modo che gli snapshot locali vengano creati meno di frequente ed essere così certi che gli snapshot cloud vengano creati periodicamente. Se queste azioni non vengono eseguite, lo spazio per questi snapshot potrebbe esaurirsi in poco tempo e il sistema li eliminerà automaticamente per assicurare che le scritture nell'host continuino a essere elaborate senza problemi. |
| Snapshot locali per <*nome gruppo di volumi*> invalidati. |Gli snapshot locali per <*nome gruppo di volumi*> sono stati invalidati e quindi eliminati perché hanno superato la quantità di spazio locale sul dispositivo. |Per essere certi che questo problema non si verifichi ancora in futuro, esaminare le pianificazioni degli snapshot locali per questi criteri di backup ed eliminare gli snapshot locali che non sono più necessari. I frequenti snapshot locali insieme a una varianza dati elevata nei volumi associati a questo gruppo di criteri di backup possono causare il rapido consumo dello spazio locale sul dispositivo. |
| Ripristino di <*ID elementi di backup di origine*> non riuscito. |Il processo di ripristino non è riuscito. |Se in questi criteri di backup è presente un volume aggiunto in locale o una combinazione di volumi aggiunti in locale e a livelli, aggiornare l'elenco dei backup per verificare che il backup sia ancora valido. Se il backup è valido è possibile che problemi di connettività cloud impediscano il corretto completamento dell'operazione di ripristino. I volumi aggiunti in locale ripristinati nell'ambito di questo gruppo di snapshot non contengono tutti i dati scaricati nel dispositivo e, se in questo gruppo di snapshot è presente una combinazione di volumi a livelli e aggiunti in locale, non saranno sincronizzati tra loro. Per completare l'operazione di ripristino, portare offline i volumi di questo gruppo nell'host e ripetere l'operazione di ripristino. Si noti che eventuali modifiche eseguite sui dati del volume durante il processo di ripristino andranno perse. |

### <a name="networking-alerts"></a>Avvisi di rete

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Impossibile avviare i servizi StorSimple. |Errore di percorso dati |Se il problema persiste, contattare il supporto tecnico Microsoft. |
| Indirizzo IP duplicato rilevato per "Data0". | |Il sistema ha rilevato un conflitto per l'indirizzo IP "10.0.0.1". La risorsa di rete "Data0" sul dispositivo *<device1>* è offline. Assicurarsi che questo indirizzo IP non venga usato da nessuna altra entità nella rete. Per risolvere i problemi di rete, vedere [Risoluzione dei problemi con il cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Per risolvere il problema, contattare l'amministratore di rete. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| L'indirizzo IPv4 (o IPv6) per "Data0" è offline. | |La risorsa di rete "Data0" con indirizzo IP "10.0.0.1." e prefisso di lunghezza "22" sul dispositivo *<device1>* è offline. Verificare che le porte di commutazione a cui questa interfaccia è connessa siano operative. Per risolvere i problemi di rete, vedere [Risoluzione dei problemi con il cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Impossibile connettersi al servizio di autenticazione. |Errore di percorso dati |L'URL usato per l'autenticazione non è raggiungibile. Verificare che nelle regole del firewall siano inclusi i modelli di URL specificati per il dispositivo StorSimple. Per altre informazioni sui modelli di URL disponibili nel portale di Azure, visitare https://aka.ms/ss-8000-network-reqs. Se si usa Azure per enti pubblici, passare ai modelli di URL disponibili in https://aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Avvisi di prestazioni

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Il carico del dispositivo ha superato <*soglia*>. |Tempi di risposta più lenti del previsto. |Il dispositivo segnala un carico di input/output eccessivo e potrebbe non funzionare come previsto. Verificare i carichi di lavoro collegati al dispositivo e determinare se alcuni di loro non sono più necessari o possono essere spostati in un altro dispositivo.| Impossibile avviare i servizi StorSimple. |Errore di percorso dati |Se il problema persiste, contattare il supporto tecnico Microsoft. |Per comprendere lo stato corrente, vedere [Usare il servizio Gestione dispositivi StorSimple per monitorare il dispositivo](storsimple-monitor-device.md) |

### <a name="security-alerts"></a>Avvisi di sicurezza

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| La sessione del supporto tecnico Microsoft è stata avviata. |Sessione di supporto per l'accesso di terze parti. |Verificare che l'accesso sia autorizzato. Dopo avere eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi. |
| La password per <*elemento*> scadrà tra <*periodo di tempo*>. |La scadenza della password è prossima. |Modificare la password prima della scadenza. |
| Informazioni sulla configurazione di sicurezza mancanti per <*ID elemento*>. | |Non è possibile usare i volumi associati a questo contenitore del volume per replicare la configurazione StorSimple. Per garantire che i dati vengano archiviati in modo sicuro, è consigliabile eliminare il contenitore del volume e gli eventuali volumi ad esso associati. Dopo avere eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi. |
| <*numero*&gt; di tentativi di accesso non riusciti per &lt;*ID elemento*&gt;. |Più tentativi di accesso non riusciti. |È possibile che il dispositivo sia vittima di un attacco o che un utente autorizzato stia tentando di connettersi con una password non corretta.<ul><li>Contattare gli utenti autorizzati e verificare che questi tentativi provengano da un'origine legittima. Se viene rilevato un numero elevato di tentativi di accesso, provare a disabilitare la gestione remota e a contattare l'amministratore di rete. Dopo avere eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi.</li><li>Verificare che le istanze di Gestione snapshot siano configurate con la password corretta. Dopo avere eseguito l'azione appropriata, cancellare questo avviso dalla pagina degli avvisi.</li></ul>Per altre informazioni, vedere [Modifica della password scaduta di un dispositivo](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Si sono verificati uno o più errori durante la modifica della chiave DEK del servizio. | |Si sono verificati errori durante la modifica della chiave DEK del servizio. Dopo avere risolto le condizioni di errore, eseguire il cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` dall'interfaccia di Windows PowerShell per StorSimple nel dispositivo per aggiornare il servizio. Se questo problema persiste, contattare il supporto tecnico Microsoft. Dopo avere risolto il problema, cancellare questo avviso dalla pagina degli avvisi. |

### <a name="support-package-alerts"></a>Avvisi del pacchetto per il supporto

| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Creazione del pacchetto per il supporto non riuscita. |StorSimple: impossibile generare il pacchetto. |Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft. Dopo avere risolto il problema, cancellare questo avviso dalla pagina degli avvisi. |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Errori di StorSimple e risoluzione dei problemi relativi a un dispositivo operativo](storsimple-troubleshoot-operational-device.md).

