---
title: Visualizzare e gestire gli avvisi dell'array virtuale StorSimple | Microsoft Docs
description: Vengono descritte le condizioni di avviso dell'array virtuale StorSimple e la loro gravità. Viene inoltre illustrato come usare il servizio StorSimple Manager per gestire gli avvisi.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli

---
# Usare il servizio StorSimple Manager per visualizzare e gestire gli avvisi per l'array virtuale StorSimple
## Panoramica
La scheda **Avvisi** del servizio StorSimple Manager fornisce un modo per esaminare e cancellare in tempo reale gli avvisi correlati all'array virtuale StorSimple. In questa scheda è possibile monitorare in modo centralizzato i problemi di integrità degli array virtuali StorSimple (noti anche come dispositivi virtuali locali StorSimple) e della soluzione Microsoft Azure StorSimple complessiva.

Questa esercitazione descrive come configurare le notifiche di avviso, le condizioni di avviso comuni e i livelli di gravità degli avvisi. Descrive inoltre come visualizzare e tenere traccia degli avvisi. Sono inoltre incluse le tabelle di riferimento rapido degli avvisi che consentono di individuare rapidamente uno specifico avviso e rispondere in modo appropriato.

![Pagina degli avvisi](./media/storsimple-ova-manage-alerts/alerts1.png)

## Configurare le impostazioni degli avvisi
È possibile scegliere se ricevere le notifiche delle condizioni di avviso tramite posta elettronica per ciascuno dei dispositivi virtuali StorSimple. È anche possibile identificare altri destinatari delle notifiche di avviso immettendo i relativi indirizzi di posta elettronica nella casella **Altri destinatari di posta elettronica**, separandoli con un punto e virgola.

> [!NOTE]
> È possibile immettere un massimo di 20 indirizzi di posta elettronica per ogni dispositivo virtuale.
> 
> 

Dopo aver attivato la notifica di posta elettronica per un dispositivo virtuale, i membri dell'elenco delle notifiche riceveranno un messaggio di posta elettronica ogni volta che si verifica un avviso critico. I messaggi verranno inviati da *storsimple-alerts-noreply@mail.windowsazure.com* e conterranno una descrizione della condizione di avviso. I destinatari possono fare clic su **Annulla sottoscrizione** per essere rimossi dall'elenco delle notifiche di posta elettronica.

#### Per abilitare la notifica di posta elettronica degli avvisi per un dispositivo virtuale
1. Passare a **Dispositivi** > **Configurazione** per il dispositivo virtuale. Passare alla sezione **Impostazione avvisi**.
   
    ![impostazioni degli avvisi](./media/storsimple-ova-manage-alerts/alerts2.png)
2. In **Impostazioni avvisi** impostare quanto segue:
   
   1. Nel campo **Invia notifica di posta elettronica** selezionare **Sì**.
   2. Nel campo **Invia messaggio di posta elettronica agli amministratori del servizio** selezionare **Sì** se si vuole che l'amministratore del servizio e tutti i co-amministratori ricevano le notifiche di avviso.
   3. Nel campo **Altri destinatari di posta elettronica** immettere gli indirizzi di posta elettronica di tutti gli altri destinatari che devono ricevere le notifiche di avviso. Immettere i nomi nel formato *someone@somewhere.com*. Utilizzare il punto e virgola per separare gli indirizzi di posta elettronica. È possibile configurare un massimo di 20 indirizzi di posta elettronica per ogni dispositivo virtuale.
      
       ![configurazione delle notifiche degli avvisi](./media/storsimple-ova-manage-alerts/alerts3.png)
3. Fare clic su **Salva** nella parte inferiore della pagina per salvare la configurazione.
4. Per inviare una notifica di posta elettronica di prova, fare clic sull'icona della freccia accanto a **Invia un messaggio di posta elettronica di prova**. Il servizio StorSimple Manager visualizza i messaggi di stato e inoltra la notifica di prova.
5. Quando viene visualizzato il messaggio seguente, fare clic su **OK**.
   
    ![Messaggio di posta elettronica di prova della notifica di avviso inviato](./media/storsimple-ova-manage-alerts/alerts4.png)
   
   > [!NOTE]
   > Se il messaggio di notifica di prova non può essere inviato, il servizio StorSimple Manager visualizza un messaggio appropriato. Fare clic su **OK**, attendere alcuni minuti e provare a inviare nuovamente il messaggio di notifica di prova.
   > 
   > 
   
    Il messaggio di notifica di prova sarà simile a quello visualizzato di seguito.
   
    ![Messaggio di posta elettronica di avviso di prova](./media/storsimple-ova-manage-alerts/alerts5.png)

## Condizioni di avviso comuni
L'array virtuale StorSimple genera avvisi in risposta a una varietà di condizioni. Di seguito sono indicati i tipi più comuni delle condizioni di avviso:

* **Problemi di connettività**: questi avvisi vengono generati in caso di difficoltà di trasferimento dei dati. Possono verificarsi problemi di comunicazione durante il trasferimento dei dati da un account di archiviazione di Azure e viceversa oppure a causa della mancanza di connettività tra i dispositivi virtuali e il servizio StorSimple Manager. I problemi di comunicazione sono tra i più difficili da risolvere poiché sono presenti numerosi punti di errore. Verificare sempre che la connettività di rete e l'accesso a Internet siano disponibili prima di proseguire con la risoluzione dei problemi più avanzati. Per altre informazioni sulle impostazioni delle porte e del firewall, vedere [Requisiti di sistema dell'array virtuale StorSimple (anteprima)](storsimple-ova-system-requirements.md). Per informazioni sulla risoluzione dei problemi, vedere [Risoluzione dei problemi con il cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemi di prestazioni**: questi avvisi vengono generati quando le prestazioni del sistema non sono ottimali, ad esempio in presenza di un forte carico.

Inoltre, possono essere generati avvisi relativi a sicurezza, aggiornamenti o errori di processi.

## Livelli di gravità dell'avviso
Gli avvisi possono avere diversi livelli di gravità, in base all'impatto determinato dalla situazione di avviso e alla necessità di una risposta all'avviso. I livelli di gravità sono:

* **Critico**: questo avviso viene generato in risposta a una condizione che influenza negativamente le prestazioni del sistema. È richiesta un'azione per garantire che il servizio StorSimple non venga interrotto.
* **Avviso**: questa condizione potrebbe diventare critica se non viene risolta. È necessario analizzare la situazione ed eseguire qualsiasi azione necessaria per eliminare il problema.
* **Informazioni**: questo avviso contiene informazioni che possono essere utili per la verifica e la gestione del sistema.

## Visualizzare e tenere traccia degli avvisi
Il dashboard del servizio StorSimple Manager offre un rapido riepilogo del numero di avvisi dei dispositivi virtuali, organizzati per livello di gravità.

![Dashboard degli avvisi](./media/storsimple-ova-manage-alerts/alerts6.png)

Scegliere il livello di gravità per aprire la scheda **Avvisi**. I risultati includono solo gli avvisi che corrispondono al livello di gravità scelto.

![Report avvisi in base al tipo di avviso](./media/storsimple-ova-manage-alerts/alerts7.png)

Fare clic su un avviso nell'elenco per visualizzare ulteriori dettagli sull'avviso, inclusi l'ora di segnalazione dell'ultimo avviso, il numero di occorrenze dell'avviso sul dispositivo e l'azione consigliata per risolvere l'avviso.

Se è necessario inviare le informazioni al supporto tecnico Microsoft, è possibile copiare i dettagli dell'avviso in un file di testo. Dopo avere seguito i suggerimenti e risolto la condizione di avviso in locale, è necessario cancellare l'avviso selezionandolo nella scheda **Avvisi** e facendo clic su **Cancella**. Per cancellare più avvisi, fare clic su qualsiasi colonna ad eccezione di **Avviso**, selezionare tutti gli avvisi da cancellare e fare clic su **Cancella**. Si noti che alcuni avvisi vengono automaticamente cancellati quando il problema viene risolto oppure quando il sistema aggiorna l'avviso con nuove informazioni.

Quando si fa clic su **Cancella** si ha la possibilità di fornire commenti sull'avviso e i passaggi eseguiti per risolvere il problema.

![commenti degli avvisi](./media/storsimple-ova-manage-alerts/clear-alert.png)

Fare clic sull'icona con il segno di spunta ![icona del segno di spunta](./media/storsimple-ova-manage-alerts/check-icon.png) per salvare i commenti.

Alcuni eventi verranno cancellati dal sistema se un altro evento viene attivato con nuove informazioni. In tal caso, viene inviato il messaggio seguente.

![Cancellare il messaggio di avviso](./media/storsimple-ova-manage-alerts/alerts8.png)

## Ordinare e rivedere gli avvisi
Nella scheda **Avvisi** può essere visualizzato un massimo di 250 avvisi. Se è stato superato il numero di avvisi, non tutti gli avvisi verranno inclusi nella visualizzazione predefinita. È possibile combinare i campi seguenti per personalizzare quali avvisi vengono visualizzati:

* **Stato**: è possibile visualizzare gli avvisi di tipo **Attivo** o **Deselezionato**. Gli avvisi attivi vengono ancora attivati nel sistema, mentre gli avvisi deselezionati sono stati cancellati manualmente da un amministratore o a livello di programmazione, in quanto la condizione di avviso è stata aggiornata dal sistema con nuove informazioni.
* **Gravità**: è possibile visualizzare gli avvisi di tutti i livelli di gravità (critico, avviso, informazioni) o solo di una determinata gravità, ad esempio solo gli avvisi critici.
* **Origine**: è possibile visualizzare gli avvisi generati da tutte le origini. In alternativa, è possibile visualizzare soltanto quelli che provengono dal servizio oppure da uno o tutti i dispositivi virtuali.
* **Intervallo di tempo**: specificando le date e i timestamp in **Da** e **A**, è possibile esaminare gli avvisi generati durante il periodo di tempo specificato.

## Riferimento rapido degli avvisi
Nelle tabelle seguenti sono elencati alcuni degli avvisi di Microsoft Azure StorSimple che potrebbero verificarsi, nonché consigli e informazioni aggiuntive eventualmente disponibili. Gli avvisi del dispositivo virtuale StorSimple rientrano in una delle seguenti categorie:

* [Avvisi di connettività cloud](#cloud-connectivity-alerts)
* [Avvisi di configurazione](#configuration-alerts)
* [Avvisi di errore di processo](#job-failure-alerts)
* [Avvisi di prestazioni](#performance-alerts)
* [Avvisi di sicurezza](#security-alerts)
* [Avvisi di aggiornamento](#update-alerts)

### Avvisi di connettività cloud
| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Il dispositivo *<device name>* non è connesso al cloud. |Il dispositivo denominato non può connettersi al cloud. |Impossibile connettersi al cloud. L'inconveniente potrebbe essere causato da uno dei motivi seguenti:<ul><li>Può essere presente un problema con le impostazioni di rete sul dispositivo.</li><li>Può essere presente un problema con le credenziali dell'account di archiviazione.</li></ul>Per altre informazioni sulle risoluzione dei problemi di connettività, vedere l'[interfaccia utente Web locale](storsimple-ova-web-ui-admin.md) del dispositivo. |

### Avvisi di configurazione
| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Configurazione del servizio virtuale locale non supportata. |Rallentamento delle prestazioni. |La configurazione corrente può influire negativamente sulle prestazioni. Assicurarsi che il server soddisfi i requisiti minimi di configurazione. Per altre informazioni, vedere [Requisiti di sistema dell'array virtuale StorSimple](storsimple-ova-system-requirements.md). |
| Lo spazio su disco di cui è stato effettuato il provisioning in <*nome dispositivo*> è quasi esaurito. |Avviso relativo allo spazio su disco. |Sta per esaurirsi lo spazio su disco con provisioning. Per liberare spazio, provare a spostare i carichi di lavoro su un altro volume oppure a condividere o eliminare dei dati. |

### Avvisi di errore di processo
| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Non è stato possibile completare il backup di <*nome dispositivo*>. |Il processo di backup non è riuscito. |Non è stato possibile creare un backup. Considerare uno degli aspetti seguenti:<ul><li>I problemi di connettività potrebbero impedire il completamento dell'operazione di backup. Assicurarsi che non siano presenti problemi di connettività. Per altre informazioni sulla risoluzione dei problemi di connettività, accedere all'[interfaccia utente Web locale](storsimple-ova-web-ui-admin.md) del dispositivo virtuale.</li><li>È stato raggiunto il limite di archiviazione disponibile. Provare a eliminare i backup che non sono più necessari per liberare spazio.</li></ul> Risolvere i problemi, cancellare l'avviso e ripetere l'operazione. |
| Non è stato possibile completare il ripristino di <*nome dispositivo*>. |Il processo di ripristino non è riuscito. |Impossibile ripristinare dal backup. Considerare uno degli aspetti seguenti:<ul><li>L'elenco di backup potrebbe non essere valido. Aggiornare l'elenco per verificare che sia ancora valido.</li><li>I problemi di connettività potrebbero impedire il completamento dell'operazione di ripristino. Verificare che non siano presenti problemi di connettività.</li><li>È stato raggiunto il limite di archiviazione disponibile. Provare a eliminare i backup che non sono più necessari per liberare spazio.</li></ul>Risolvere i problemi, cancellare l'avviso e ripetere l'operazione di ripristino. |
| Non è stato possibile completare la creazione del clone di <*nome dispositivo*>. |Il processo di clonazione non è riuscito. |Impossibile creare un clone. Considerare uno degli aspetti seguenti:<ul><li>L'elenco di backup potrebbe non essere valido. Aggiornare l'elenco per verificare che sia ancora valido.</li><li>I problemi di connettività potrebbero impedire il completamento dell'operazione di clonazione. Verificare che non siano presenti problemi di connettività.</li><li>È stato raggiunto il limite di archiviazione disponibile. Provare a eliminare i backup che non sono più necessari per liberare spazio.</li></ul>Risolvere i problemi, cancellare l'avviso e ripetere l'operazione. |

### Avvisi di prestazioni
| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Si stanno verificando ritardi imprevisti nel trasferimento dei dati. |Trasferimento dati lento. |Gli errori di limitazione si verificano quando si superano gli obiettivi di scalabilità di un servizio di archiviazione. In questo modo il servizio di archiviazione assicura che nessun client o tenant possa usare il servizio a spese di altri. Per altre informazioni sulla risoluzione dei problemi relativi all'account di archiviazione di Azure, vedere [Monitorare, diagnosticare e risolvere i problemi dell'Archiviazione di Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md). |
| Lo spazio su disco riservato in locale in <*nome dispositivo*> è quasi esaurito. |Tempo di risposta lento. |Il 10% delle dimensioni totali di cui è stato effettuato il provisioning per <*nome dispositivo*> è riservato nel dispositivo locale e al momento lo spazio riservato è quasi esaurito. Il carico di lavoro in <*nome dispositivo*> sta generando un'elevata varianza o è possibile che di recente sia stata eseguita la migrazione di una grande quantità di dati causando una riduzione delle prestazioni. Questo può comportare una riduzione delle prestazioni. Per risolvere questo problema, provare a eseguire una delle azioni seguenti:<ul><li>Aumentare la larghezza di banda del cloud in questo dispositivo.</li><li>Ridurre o spostare i carichi di lavoro in un altro volume o in un'altra condivisione.</li></ul> |

### Avvisi di sicurezza
| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| La password per <*nome dispositivo*> scadrà tra <*numero*> giorni. |Avviso relativo alla password. |La password scadrà tra <numero> giorni. Provare a modificare la password. Per altre informazioni, vedere [Modificare la password amministratore del dispositivo array virtuale StorSimple](storsimple-ova-change-device-admin-password.md). |

### Avvisi di aggiornamento
| Testo dell'avviso | Evento | Ulteriori informazioni/Azioni consigliate |
|:--- |:--- |:--- |
| Sono disponibili nuovi aggiornamenti per il dispositivo. |Sono disponibili aggiornamenti per l'array virtuale StorSimple. |È possibile installare i nuovi aggiornamenti dalla pagina **Manutenzione**. |
| Non è stato possibile verificare la disponibilità di aggiornamenti in <*nome dispositivo*>. |Errore durante l'aggiornamento. |Errore durante l'installazione di nuovi aggiornamenti. È possibile installare manualmente gli aggiornamenti. Se il problema persiste, contattare il [supporto Microsoft](storsimple-contact-microsoft-support.md). |

## Passaggi successivi
* [Informazioni sull'array virtuale StorSimple](storsimple-ova-overview.md).

<!---HONumber=AcomDC_0622_2016-->