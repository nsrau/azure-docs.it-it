Le macchine virtuali (VM) di Azure a volte possono riavviarsi senza un motivo apparente, senza che sia stata avviata l'operazione di riavvio. Questo articolo elenca le azioni e gli eventi che possono provocare il riavvio delle macchine virtuali e fornisce informazioni su come evitare problemi di riavvio imprevisto o ridurre le conseguenze di tali problemi.

<a id="configure-the-vms-for-high-availability" class="xliff"></a>

## Configurare le macchine virtuali per la disponibilità elevata
Il modo migliore per proteggere un'applicazione in esecuzione in Azure dalle conseguenze di riavvii e tempi di inattività delle macchine virtuali è quello di configurare le macchine virtuali per la disponibilità elevata.

Per garantire questo livello di ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia sempre disponibile e soddisfi per almeno il 99,95% i requisiti del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Per altre informazioni sui set di disponibilità, vedere gli articoli seguenti:

- [Gestire la disponibilità delle macchine virtuali](../articles/virtual-machines/windows/manage-availability.md)
- [Configurare la disponibilità delle macchine virtuali](../articles/virtual-machines/windows/classic/configure-availability.md)

<a id="resource-health-information" class="xliff"></a>

## Informazioni su Integrità risorse 
Integrità risorse di Azure è un nuovo servizio che espone l'integrità delle singole risorse di Azure e offre consigli pratici per risolvere i problemi. In un ambiente cloud in cui non è possibile accedere direttamente ai server o agli elementi dell'infrastruttura, l'obiettivo di Integrità risorse è quello di ridurre il tempo impiegato per la risoluzione dei problemi, in particolare lo scopo è ridurre il tempo impiegato per determinare se il problema è interno all'applicazione o dovuto a un evento specifico della piattaforma Azure. Per altre informazioni, vedere [Panoramica su Integrità risorse di Azure](../articles/resource-health/resource-health-overview.md).

<a id="actions-and-events-that-can-cause-the-vm-to-reboot" class="xliff"></a>

## Azioni e gli eventi che possono generare il riavvio della macchina virtuale

<a id="planned-maintenance" class="xliff"></a>

### Manutenzione pianificata
Microsoft Azure esegue periodicamente aggiornamenti a livello globale per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host sottostante alle macchine virtuali. Molti di questi aggiornamenti, inclusi gli aggiornamenti con mantenimento della memoria, vengono eseguiti senza alcun impatto sulle macchine virtuali o sui servizi cloud.

Altri aggiornamenti, invece, richiedono il riavvio. In questi casi, le macchine virtuali vengono arrestate durante l'applicazione delle patch all'infrastruttura e quindi riavviate.

Per informazioni sulla manutenzione pianificata di Azure e su come può influire sulla disponibilità delle macchine virtuali Linux, vedere gli articoli elencati qui. Gli articoli forniscono informazioni di base sul processo di manutenzione pianificata di Azure e su come pianificare la manutenzione per ridurre ulteriormente l'impatto.

- [Manutenzione pianificata per le macchine virtuali in Azure](../articles/virtual-machines/windows/planned-maintenance.md)
- [Come pianificare la manutenzione nelle VM di Azure](../articles/virtual-machines/windows/planned-maintenance-schedule.md)

<a id="memory-preserving-updates" class="xliff"></a>

### Aggiornamenti con mantenimento della memoria   
Per questa classe di aggiornamenti in Microsoft Azure, gli utenti non notano alcun impatto sulle macchine virtuali in esecuzione. Molti di questi aggiornamenti sono componenti o servizi che possono essere aggiornati senza interferire con l'istanza in esecuzione. Alcuni sono aggiornamenti dell'infrastruttura della piattaforma nel sistema operativo host, che possono essere applicati senza riavviare le macchine virtuali.

Questi aggiornamenti con mantenimento della memoria vengono eseguiti con una tecnologia che consente la migrazione sul posto in tempo reale. Quando viene aggiornata, la VM viene messa *in pausa*. Questo stato mantiene la memoria in RAM mentre il sistema operativo host sottostante riceve gli aggiornamenti e le patch necessari. La macchina virtuale viene ripresa dopo un periodo di pausa massimo di 30 secondi. Dopo che la VM è stata ripresa, l'orologio viene sincronizzato automaticamente.

Grazie al breve periodo di pausa, la distribuzione degli aggiornamenti tramite questo meccanismo riduce considerevolmente l'impatto sulle VM. Non si possono tuttavia distribuire tutti gli aggiornamenti in questo modo. 

Gli aggiornamenti a istanza multipla (per le macchine virtuali in un set di disponibilità) vengono applicati su un dominio di aggiornamento alla volta.

> [!NOTE]
> Con questo metodo di aggiornamento, i computer Linux con versioni precedenti del kernel sono interessati da un kernel panic. Per evitare questo problema, aggiornare il kernel alla versione 3.10.0-327.10.1 o successiva. Per altre informazioni, vedere [Kernel panic di una macchina virtuale Linux di Azure basata su un kernel 3.10 dopo l'aggiornamento di nodo host](https://support.microsoft.com/help/3212236).     
    
<a id="user-initiated-reboot-or-shutdown-actions" class="xliff"></a>

### Azioni di arresto o riavvio avviate dall'utente
 
Se si esegue un riavvio dal portale di Azure, da Azure PowerShell, dall'interfaccia della riga di comando o dall'API RESET, è possibile trovare l'evento nel [log attività di Azure](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Se si esegue l'azione dal sistema operativo della VM, è possibile trovare l'evento nei registri di sistema.

Il riavvio della macchina virtuale si verifica solitamente anche quando si eseguono più azioni di modifica della configurazione. In genere, viene visualizzato un messaggio di avviso in cui si specifica che l'esecuzione di una determinata azione comporterà il riavvio della macchina virtuale. Questi tipi di azione includono, ad esempio, operazioni di ridimensionamento della macchina virtuale, la modifica della password dell'account amministrativo o l'impostazione di un indirizzo IP statico.

<a id="azure-security-center-and-windows-update" class="xliff"></a>

### Centro sicurezza di Azure e Windows Update
Il Centro sicurezza di Azure monitora ogni giorno le macchine virtuali Windows e Linux alla ricerca di eventuali aggiornamenti mancanti del sistema operativo. Il Centro sicurezza recupera un elenco di aggiornamenti di sicurezza e critici disponibili da Windows Update o Windows Server Update Services (WSUS), in base al servizio configurato nella macchina virtuale Windows. Il Centro sicurezza cerca gli aggiornamenti più recenti anche per i sistemi Linux. Se nella macchina virtuale non è stato eseguito un aggiornamento del sistema, il Centro sicurezza ne consiglia l'applicazione. L'applicazione di questi aggiornamenti di sistema è controllata tramite il Centro sicurezza nel portale di Azure. Dopo l'applicazione di alcuni aggiornamenti, potrebbe essere necessario il riavvio della macchina virtuale. Per altre informazioni, vedere [Applicare gli aggiornamenti del sistema nel Centro sicurezza di Azure](../articles/security-center/security-center-apply-system-updates.md).

Come per i server locali, Azure non effettua il push degli aggiornamenti da Windows Update alle macchine virtuali di Windows Azure perché questi computer sono pensati per essere gestiti dagli utenti. Tuttavia si consiglia di lasciare abilitata l'impostazione automatica di Windows Update. Con l'installazione automatica degli aggiornamenti da Windows Update, il riavvio può anche verificarsi dopo l'applicazione degli aggiornamenti. Per altre informazioni, vedere [Windows Update: domande frequenti](https://support.microsoft.com/help/12373/windows-update-faq).

<a id="other-situations-affecting-the-availability-of-your-vm" class="xliff"></a>

### Altre situazioni che influiscono sulla disponibilità della macchina virtuale
Esistono altri casi in cui Azure può sospendere attivamente l'uso di una macchina virtuale. Si riceveranno tuttavia notifiche di posta elettronica prima che venga intrapresa questa azione e si avrà quindi la possibilità di risolvere i problemi sottostanti. Tra i problemi che compromettono la disponibilità delle VM sono incluse le violazioni della sicurezza e la scadenza dei metodi di pagamento.

<a id="host-server-faults" class="xliff"></a>

### Errori del server host 
La macchina virtuale è ospitata in un server fisico in esecuzione in un data center di Azure. Il server fisico esegue un agente denominato agente host e alcuni altri componenti di Azure. Se questi componenti software di Azure nel server fisico non rispondono, il sistema di monitoraggio attiva un riavvio del server host per tentarne il ripristino. La macchina virtuale in genere torna disponibile entro cinque minuti e rimane nello stesso host in cui si trovava in precedenza.

Gli errori del server vengono solitamente generati da errori hardware, ad esempio dal guasto di un disco rigido o di un'unità SSD. Azure monitorizza in modo continuo queste occorrenze, identifica i bug sottostanti e rilascia gli aggiornamenti dopo aver implementato e testato l'attenuazione.

Poiché alcuni errori del server host possono essere specifici del server, una situazione di riavvio ripetuto di una macchina virtuale può essere migliorata ridistribuendo manualmente la macchina virtuale in un altro server host. Questa operazione può essere attivata usando l'opzione **Ridistribuisci** nella pagina dei dettagli della macchina virtuale oppure arrestando e riavviando la macchina virtuale nel portale di Azure.

<a id="auto-recovery" class="xliff"></a>

### Ripristino automatico
Se il server host non può essere riavviato per qualsiasi motivo, la piattaforma Azure avvia un'azione di ripristino automatico per portare il server host in errore fuori dalla rotazione e consentirne un'analisi più approfondita. 

Tutte le macchine virtuali presenti nell'host vengono automaticamente riassegnate a un server host diverso, integro. Questo processo richiede in genere 15 minuti. Per altre informazioni sul processo di ripristino automatico, vedere [Auto-recovery of VMs](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines) (Ripristino automatico delle macchine virtuali).

<a id="unplanned-maintenance" class="xliff"></a>

### Manutenzione non pianificata
In rare occasioni, è possibile che il team operativo di Azure debba eseguire alcune attività di manutenzione per garantire l'integrità complessiva della piattaforma Azure. Questo comportamento può influire sulla disponibilità della macchina virtuale e in genere determina la stessa azione di recupero automatico descritta in precedenza.  

Le operazioni di manutenzione non pianificata includono:

- Deframmentazione urgente di un nodo
- Aggiornamenti urgenti di switch di rete

<a id="vm-crashes" class="xliff"></a>

### Arresti anomali della macchina virtuale
Una VM può venire riavviata a causa di problemi interni. Il ruolo o il carico di lavoro in esecuzione nella macchina virtuale può attivare un controllo bug nel sistema operativo guest. Per determinare il motivo dell'arresto anomalo, visualizzare i registri applicazioni e di sistema per le macchine virtuali Windows e i log seriali per le macchine virtuali Linux.

<a id="storage-related-forced-shutdowns" class="xliff"></a>

### Arresti forzati correlati all'archiviazione
Le macchine virtuali in Azure si basano su dischi virtuali per il sistema operativo e l'archiviazione dati ospitata nell'infrastruttura di archiviazione di Azure. Ogni volta che la disponibilità o la connettività tra la macchina virtuale e i dischi virtuali associati viene interrotta per più di 120 secondi, la piattaforma Azure esegue un arresto forzato delle macchine virtuali per evitare il danneggiamento dei dati. Le macchine virtuali vengono automaticamente riaccese dopo aver ripristinato la connettività dell'archiviazione. 

La durata dell'arresto può essere di cinque minuti o molto più lunga. Di seguito è illustrato uno dei casi specifici di arresto forzato correlato all'archiviazione: 

**Superamento dei limiti di I/O**

È possibile che si verifichi un arresto temporaneo delle macchine virtuali quando le richieste di I/O vengono continuamente limitate perché il volume di operazioni di input/output al secondo (IOPS) è superiore ai limiti per il disco. L'archiviazione su disco standard è limitata a 500 operazioni di I/O al secondo. Per attenuare questo problema, usare lo striping del disco o configurare lo spazio di archiviazione all'interno della macchina virtuale guest, a seconda del carico di lavoro. Per altre informazioni, vedere [Configurazione delle macchine virtuali di Azure per prestazioni di archiviazione ottimali](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

Con l'archiviazione Premium di Azure sono previsti limiti di IOPS più elevati, fino a 80.000 IOPS. Per altre informazioni, vedere [Archiviazione Premium con prestazioni elevate](../articles/storage/storage-premium-storage.md).

<a id="other-incidents" class="xliff"></a>

### Altri eventi imprevisti
In rare circostanze, un problema di ampia portata può interessare più server in un data center di Azure. In questo caso, il team di Azure invia notifiche di posta elettronica alle sottoscrizioni interessate. È possibile visualizzare il [dashboard per l'integrità dei servizi di Azure](https://azure.microsoft.com/status/) e il portale di Azure per verificare lo stato delle interruzioni in corso e degli eventi imprevisti passati.
