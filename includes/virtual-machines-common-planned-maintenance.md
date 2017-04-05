

## <a name="memory-preserving-updates"></a>Aggiornamenti con mantenimento della memoria
Per una classe di aggiornamenti in Microsoft Azure, i clienti non visualizzano alcun impatto sulle macchine virtuali in esecuzione. Molti di questi aggiornamenti sono componenti o servizi che possono essere aggiornati senza interferire con l'istanza in esecuzione. Alcuni di questi aggiornamenti sono aggiornamenti dell’infrastruttura della piattaforma nel sistema operativo host che possono essere applicati senza richiedere un riavvio completo delle macchine virtuali.

Questi aggiornamenti vengono eseguiti con la tecnologia che consente la migrazione sul posto in tempo reale, anche detta aggiornamento con mantenimento della memoria. Durante l'aggiornamento, la macchina virtuale viene inserita in uno stato "sospeso", mantenendo la memoria in RAM, mentre il sistema operativo host sottostante riceve gli aggiornamenti e le patch necessari. La macchina virtuale viene ripresa dopo un periodo di pausa massimo di 30 secondi. Dopo la ripresa, l’orologio della macchina virtuale viene sincronizzato automaticamente.

Non tutti gli aggiornamenti possono essere distribuiti utilizzando questo meccanismo, ma dato il breve periodo di pausa, la distribuzione degli aggiornamenti in questo modo riduce notevolmente l’impatto sulle macchine virtuali.

Gli aggiornamenti a istanza multipla (per le macchine virtuali in un set di disponibilità) vengono applicati su un dominio di aggiornamento alla volta.  

## <a name="virtual-machine-configurations"></a>Configurazioni delle macchine virtuali
Esistono due tipi di configurazioni delle macchine virtuali: a istanza multipla e a istanza singola. In una configurazione a istanza multipla, le macchine virtuali simili vengono inserite in un set di disponibilità.

La configurazione a istanza multipla offre ridondanza tra computer fisici, alimentazione e rete, ed è consigliata per garantire la disponibilità dell'applicazione. Tutte le macchine virtuali nel set di disponibilità devono avere la stessa funzione nell'applicazione.

Per altre informazioni sulla configurazione delle macchine virtuali per una disponibilità elevata, vedere [Gestire la disponibilità delle macchine virtuali Windows](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Gestire la disponibilità delle macchine virtuali Linux](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Al contrario, una configurazione a istanza singola viene utilizzata per le macchine virtuali autonome che non vengono inserite in un set di disponibilità. Tali macchine virtuali non sono idonee per il contratto di servizio che richiede la distribuzione di due o più macchine virtuali nello stesso set di disponibilità.

Per altre informazioni sui contratti di servizio, vedere le sezioni "Servizi cloud e macchine virtuali" in [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## <a name="multi-instance-configuration-updates"></a>Aggiornamenti delle configurazioni a istanza multipla
Durante la manutenzione pianificata, la piattaforma Azure aggiorna innanzitutto il set di macchine virtuali ospitate in una configurazione a istanza multipla. L'aggiornamento causa un riavvio di queste macchine virtuali con circa 15 minuti di inattività.

In un aggiornamento della configurazione a istanza multipla si presuppone che ogni macchina virtuale abbia una funzione simile a quella delle altre nel set di disponibilità. In questa impostazione le macchine virtuali vengono aggiornate in modo tale da mantenere la disponibilità per tutto il processo.

A ciascuna macchina virtuale in un set di disponibilità viene assegnato un dominio di aggiornamento e un dominio di errore dalla piattaforma Azure sottostante. Ciascun dominio di aggiornamento è un gruppo di macchine virtuali che verranno riavviate nello stesso intervallo di tempo. Ciascun dominio di errore è un gruppo di macchine virtuali che condividono un'unità di alimentazione o un commutatore di rete comune.


Per altre informazioni sui domini di aggiornamento e sui domini di errore, vedere [Configurazione di più macchine virtuali in un set di disponibilità per la ridondanza](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)

Per mantenere la disponibilità attraverso un aggiornamento, Azure esegue la manutenzione tramite il dominio di aggiornamento, aggiornando un dominio alla volta. La manutenzione in un dominio di aggiornamento implica l'arresto di tutte le macchine virtuali nel dominio, l'applicazione dell'aggiornamento ai computer host e quindi il riavvio delle macchine virtuali. Al termine del processo di manutenzione nel dominio, Azure ripete il processo con il dominio di aggiornamento successivo e continua con ogni dominio finché non sono stati tutti aggiornati.

L'ordine dei domini di aggiornamento in corso di riavvio potrebbe non procedere in modo sequenziale durante la manutenzione pianificata, ma viene riavviato un solo dominio di aggiornamento alla volta. Al momento Azure offre un servizio di notifica avanzata di una settimana per la manutenzione pianificata delle macchine virtuali nella configurazione a istanza multipla.

Di seguito viene riportato un esempio di ciò che Visualizzatore eventi di Windows potrebbe visualizzare al termine del ripristino di una macchina virtuale:

<!--Image reference-->
![][image2]


Usare il visualizzatore per segnalare le macchine virtuali che sono configurate in una configurazione a istanza multipla mediante il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure. Ad esempio, tramite il portale di Azure è possibile aggiungere il _Set di disponibilità_ alla finestra di dialogo di ricerca **Macchine virtuali (classico)**. Le macchine virtuali con lo stesso set di disponibilità fanno parte di una configurazione a istanza multipla. Nell'esempio seguente la configurazione a istanza multipla è costituita dalle macchine virtuali SQLContoso01 e SQLContoso02.

<!--Image reference-->
  ![Visualizzazione Macchine virtuali (classico) del portale di Azure][image4]

## <a name="single-instance-configuration-updates"></a>Aggiornamenti della configurazione a istanza singola
Una volta completati gli aggiornamenti della configurazione a istanza multipla, Azure esegue gli aggiornamenti della configurazione a istanza singola. Anche questi aggiornamenti comportano un riavvio delle macchine virtuali che non sono in esecuzione nei set di disponibilità.

> [!NOTE]
> Anche se un set di disponibilità dispone di una sola istanza di macchina virtuale in esecuzione, la piattaforma Azure considera l'aggiornamento come aggiornamento della configurazione a istanza multipla.
>

La manutenzione in una configurazione a istanza singola implica l'arresto di tutte le macchine virtuali in esecuzione in un computer host, l'aggiornamento del computer host e quindi il riavvio delle macchine virtuali. La manutenzione richiede circa 15 minuti di inattività. L'evento di manutenzione pianificata viene eseguito in tutte le macchine virtuali di un'area in un'unica finestra di manutenzione.


Gli eventi di manutenzione pianificata influiscono sulla disponibilità dell'applicazione per le configurazioni a istanza singola. Azure offre un servizio di notifica avanzato di una settimana per la manutenzione pianificata delle macchine virtuali nelle configurazioni a istanza singola.

## <a name="email-notification"></a>Notifica tramite posta elettronica
Solo per le configurazioni delle macchine virtuali a istanza singola e a istanza multipla, Azure invia un avviso di posta elettronica dell'imminente manutenzione pianificata (con un anticipo di una settimana). Questo messaggio viene inviato agli account di posta elettronica dell'amministratore e del co-amministratore forniti nella sottoscrizione. Di seguito viene riportato un esempio di questo tipo di messaggio di posta elettronica:

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>Coppie di aree

Quando si esegue la manutenzione, Azure aggiorna solo le istanze della macchina virtuale in una singola area della relativa coppia. Ad esempio, quando si aggiornano le macchine virtuali negli Stati Uniti centro-settentrionali, Azure non aggiorna contemporaneamente le macchine virtuali negli Stati Uniti centro-meridionali. Questa operazione viene pianificata in un secondo momento, consentendo il failover o il bilanciamento del carico tra le aree. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali.

Vedere la tabella seguente per coppie di aree correnti:

| Area 1 | Area 2 |
|:--- | ---:|
| Stati Uniti Orientali |Stati Uniti occidentali |
| Stati Uniti orientali 2 |Stati Uniti centrali |
| Stati Uniti centro-settentrionali |Stati Uniti centro-meridionali |
| Stati Uniti centro-occidentali |Stati Uniti occidentali 2 |
| Canada orientale |Canada centrale |
| Brasile meridionale |Stati Uniti centro-meridionali |
| Governo degli Stati Uniti - Iowa |Governo degli Stati Uniti - Virginia |
| Dipartimento della difesa Stati Uniti orientali |Dipartimento della difesa Stati Uniti centrali |
| Europa settentrionale |Europa occidentale |
| Regno Unito occidentale |Regno Unito meridionale |
| Germania centrale |Germania nord-orientale |
| Asia sudorientale |Asia orientale |
| Australia sudorientale |Australia orientale |
| India centrale |India meridionale |
| India occidentale |India meridionale |
| Giappone orientale |Giappone occidentale |
| Corea centrale |Corea meridionale |
| Cina orientale |Cina settentrionale |


<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/availabilitysetexample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/windows/manage-availability.md#Understand-planned-versus-unplanned-maintenance/
