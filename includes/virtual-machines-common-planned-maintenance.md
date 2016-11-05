

## Aggiornamenti con mantenimento della memoria
Per una classe di aggiornamenti in Microsoft Azure, i clienti non visualizzeranno alcun impatto sulle macchine virtuali in esecuzione. Molti di questi aggiornamenti sono componenti o servizi che possono essere aggiornati senza interferire con l'istanza in esecuzione. Alcuni di questi aggiornamenti sono aggiornamenti dell’infrastruttura della piattaforma nel sistema operativo host che possono essere applicati senza richiedere un riavvio completo delle macchine virtuali.

Questi aggiornamenti vengono eseguiti con la tecnologia che consente la migrazione sul posto in tempo reale, anche detta aggiornamento con mantenimento della memoria. Durante l'aggiornamento, la macchina virtuale viene inserita in uno stato "sospeso", mantenendo la memoria in RAM, mentre il sistema operativo host sottostante riceve gli aggiornamenti e le patch necessari. La macchina virtuale viene ripresa dopo un periodo di pausa massimo di 30 secondi. Dopo la ripresa, l’orologio della macchina virtuale viene sincronizzato automaticamente.

Non tutti gli aggiornamenti possono essere distribuiti utilizzando questo meccanismo, ma dato il breve periodo di pausa, la distribuzione degli aggiornamenti in questo modo riduce notevolmente l’impatto sulle macchine virtuali.

Gli aggiornamenti a istanza multipla (per le macchine virtuali in un set di disponibilità) vengono applicati su un dominio di aggiornamento alla volta.

## Configurazioni delle macchine virtuali
Esistono due tipi di configurazioni delle macchine virtuali: a istanza multipla e a istanza singola. In una configurazione a istanza multipla, le macchine virtuali simili vengono inserite in un set di disponibilità.

La configurazione a istanza multipla offre ridondanza tra computer fisici, alimentazione e rete, ed è consigliata per garantire la disponibilità dell'applicazione. Tutte le macchine virtuali nel set di disponibilità devono avere la stessa funzione nell'applicazione.

Per altre informazioni sulla configurazione delle macchine virtuali per una disponibilità elevata, vedere [Gestione della disponibilità delle macchine virtuali Windows](../articles/virtual-machines/virtual-machines-windows-manage-availability.md) o [Gestione della disponibilità delle macchine virtuali Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

Al contrario, una configurazione a istanza singola viene utilizzata per le macchine virtuali autonome che non vengono inserite in un set di disponibilità. Tali macchine virtuali non sono idonee per il contratto di servizio che richiede due o più macchine virtuali distribuite nello stesso set di disponibilità.

Per altre informazioni sul contratto di servizio, vedere la sezione "Servizi cloud, macchine virtuali e rete virtuale" in [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## Aggiornamenti delle configurazioni a istanza multipla
Durante la manutenzione pianificata, la piattaforma Azure aggiorna innanzitutto il set di macchine virtuali ospitate in una configurazione a istanza multipla. Questo causa un riavvio di queste macchine virtuali con circa 15 minuti di inattività.

In un aggiornamento della configurazione a istanza multipla, le macchine virtuali vengono aggiornate in modo tale da mantenere la disponibilità per tutto il processo, supponendo che ogni macchina virtuale abbia una funzione simile a quella delle altre del set.

A ciascuna macchina virtuale nel set di disponibilità viene assegnato un dominio di aggiornamento e un dominio di errore dalla piattaforma Azure sottostante. Ciascun dominio di aggiornamento è un gruppo di macchine virtuali che verranno riavviate nello stesso intervallo di tempo. Ciascun dominio di errore è un gruppo di macchine virtuali che condividono un'unità di alimentazione o un commutatore di rete comune.

Per altre informazioni sui domini di aggiornamento e sui domini di errore, vedere [Configurazione di più macchine virtuali in un set di disponibilità per la ridondanza](../articles/virtual-machines/virtual-machines-windows-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)

Per impedire che i domini di aggiornamento passino offline contemporaneamente, la manutenzione viene eseguita arrestando tutte le macchine virtuali in un dominio di aggiornamento, applicando l'aggiornamento sulle macchine host, riavviando le macchine virtuali e passando al dominio di aggiornamento successivo. L'evento di manutenzione pianificata termina quando tutti i domini di aggiornamento sono stati aggiornati.

L'ordine dei domini di aggiornamento in corso di riavvio potrebbe non procedere in modo sequenziale durante la manutenzione pianificata, ma viene riavviato un solo dominio di aggiornamento alla volta. Al momento Azure offre un servizio di notifica avanzata di una settimana per la manutenzione pianificata delle macchine virtuali nella configurazione a istanza multipla.

Di seguito viene riportato un esempio di ciò che Visualizzatore eventi di Windows potrebbe visualizzare al termine del ripristino di una macchina virtuale:

<!--Image reference-->
![][image2]

Usare il visualizzatore per determinare quali macchine virtuali sono configurate in una configurazione a istanza multipla mediante il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure. Ad esempio, per determinare quali macchine virtuali sono in una configurazione a istanza multipla, è possibile esplorare l'elenco delle macchine virtuali con la colonna Set di disponibilità aggiunta alla finestra di dialogo di ricerca delle macchine virtuali. Nell'esempio seguente, le macchine virtuali Example-VM1 e Example-VM2 sono in una configurazione a istanza multipla:

<!--Image reference-->
![][image4]

## Aggiornamenti della configurazione a istanza singola
Una volta completati gli aggiornamenti della configurazione a istanza multipla, Azure eseguirà gli aggiornamenti della configurazione a istanza singola. Anche questo aggiornamento comporta un riavvio delle macchine virtuali che non sono in esecuzione nei set di disponibilità.

Anche se si dispone di una sola istanza in esecuzione in un set di disponibilità, la piattaforma Azure considera l’aggiornamento come aggiornamento della configurazione a istanza multipla.

In una configurazione a istanza singola le macchine virtuali vengono aggiornate arrestando le macchine virtuali, applicando l'aggiornamento al computer host e riavviando le macchine virtuali, con circa 15 minuti di inattività. Questi aggiornamenti vengono eseguiti in tutte le macchine virtuali di un'area in una singola finestra di manutenzione.

Questo evento di manutenzione pianificata influirà sulla disponibilità dell'applicazione per questo tipo di configurazione di macchina virtuale. Azure offre un servizio di notifica avanzato di una settimana per la manutenzione pianificata delle macchine virtuali nella configurazione a istanza singola.

## Notifica tramite posta elettronica
Solo per le configurazioni delle macchine virtuali a istanza singola e a istanza multipla, Azure invia una comunicazione tramite posta elettronica in anticipo, per avvisare della manutenzione pianificata imminente (con un anticipo di una settimana). Questo messaggio di posta elettronica verrà inviato agli account di posta elettronica dell'amministratore e del co-amministratore forniti nella sottoscrizione. Di seguito viene riportato un esempio di questo tipo di messaggio di posta elettronica:

<!--Image reference-->
![][image1]

## Coppie di aree
Quando si esegue la manutenzione, Azure aggiorna solo le istanze della macchina virtuale in una singola area della relativa coppia. Ad esempio, quando si aggiornano le macchine virtuali negli Stati Uniti centro-settentrionali, Azure non aggiorna contemporaneamente le macchine virtuali negli Stati Uniti centro-meridionali. Questa operazione viene pianificata in un secondo momento, consentendo il failover o il bilanciamento del carico tra le aree. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali.

Consultare la tabella seguente per informazioni relative alle coppie di aree correnti:

| Area 1 | Area 2 |
|:--- | ---:|
| Stati Uniti centro-settentrionali |Stati Uniti centro-meridionali |
| Stati Uniti Orientali |Stati Uniti occidentali |
| Stati Uniti orientali 2 |Stati Uniti centrali |
| Europa settentrionale |Europa occidentale |
| Asia sudorientale |Asia orientale |
| Cina orientale |Cina settentrionale |
| Giappone orientale |Giappone occidentale |
| Brasile meridionale |Stati Uniti centro-meridionali |
| Australia sudorientale |Australia orientale |
| India centrale |India meridionale |
| India occidentale |India meridionale |
| Governo degli Stati Uniti - Iowa |Governo degli Stati Uniti - Virginia |

<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/AvailabilitySetExample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

