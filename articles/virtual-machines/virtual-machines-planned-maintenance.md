<properties 
	pageTitle="Manutenzione pianificata per le macchine virtuali di Azure" 
	description="Informazioni sulla manutenzione pianificata di Azure e sul relativo impatto sulle macchine virtuali in esecuzione in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kenazk" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="kenazk"/>


# Manutenzione pianificata per le macchine virtuali di Azure

## Perché Azure esegue la manutenzione pianificata
<p> Microsoft Azure esegue periodicamente aggiornamenti in tutto il mondo per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host sottostante alle macchine virtuali. Molti di questi aggiornamenti vengono eseguiti senza alcun impatto sulle macchine virtuali o sui servizi cloud, inclusi gli aggiornamenti con mantenimento della memoria. Tuttavia, alcuni aggiornamenti richiedono un riavvio della macchina virtuale per applicare gli aggiornamenti necessari all'infrastruttura. La macchina virtuale verrà arrestata durante l'applicazione delle patch all'infrastruttura e quindi riavviata. Si noti che esistono due tipi di manutenzione che possono influire sulla disponibilità della macchina virtuale: pianificata e non pianificata. Questa pagina descrive come Microsoft Azure esegue la manutenzione pianificata. Per altre informazioni sulla manutenzione non pianificata, vedere la pagina relativa al [confronto tra manutenzione pianificata e non pianificata].

## Aggiornamenti con mantenimento della memoria
Per una classe di aggiornamenti in Microsoft Azure, i clienti non visualizzano alcun impatto sulle macchine virtuali in esecuzione. Molti di questi aggiornamenti sono componenti o servizi che possono essere aggiornati senza interferire con l'istanza in esecuzione. Alcuni di questi aggiornamenti sono aggiornamenti dell’infrastruttura della piattaforma nel sistema operativo host che possono essere applicati senza richiedere un riavvio completo della macchina virtuale. Questi aggiornamenti vengono eseguiti con la tecnologia sviluppata nel team di Azure, che consente la migrazione live o un aggiornamento con "mantenimento della memoria". Durante l'aggiornamento, la macchina virtuale viene inserita in uno stato "sospeso", mantenendo la memoria in RAM, mentre il sistema operativo host sottostante riceve l’aggiornamento e le patch necessari. La macchina virtuale viene quindi ripresa dopo un periodo di pausa massimo di 30 secondi. Dopo la ripresa, l’orologio della macchina virtuale viene sincronizzato automaticamente.

Non tutti gli aggiornamenti possono essere distribuiti con questo meccanismo, ma dato il breve periodo di pausa, la distribuzione degli aggiornamenti in questo modo riduce notevolmente l’impatto sulle macchine virtuali. Per macchine virtuali a istanza multipla (in un set di disponibilità), gli aggiornamenti vengono applicati un dominio di aggiornamento (UD) alla volta.

## Configurazioni di macchina virtuale
Esistono due tipi di configurazioni di macchina virtuale: a istanza multipla e a istanza singola. Le macchine virtuali a istanza multipla vengono configurate inserendo le macchine virtuali identiche in un set di disponibilità. La configurazione a istanza multipla fornisce la ridondanza ed è consigliata per garantire la disponibilità dell'applicazione. Tutte le macchine virtuali nel set di disponibilità devono essere quasi identiche e servire allo stesso scopo nell'applicazione. Per altre informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere "<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/">Gestire la disponibilità delle macchine virtuali</a>".

Al contrario, le macchine virtuali a istanza singola sono macchine virtuali autonome che non vengono inserite in un set di disponibilità. Le macchine virtuali a istanza singola non sono di per sé idonee per il contratto di servizio che richiede due o più macchine virtuali distribuite nello stesso set di disponibilità. Per altre informazioni sul contratto di servizio, vedere la sezione "Servizi cloud, macchine virtuali e rete virtuale" in [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/).


## Aggiornamento a istanza multipla
Durante la manutenzione pianificata la piattaforma Azure eseguirà innanzitutto l'aggiornamento del set di computer host che ospitano il set di macchine virtuali in una configurazione a istanza multipla comportando il riavvio di queste macchine virtuali. In una configurazione a istanza multipla le macchine virtuali vengono aggiornate in modo tale da preservare la disponibilità per tutto il processo, supponendo che ogni computer svolga una funzione simile a quella degli altri computer del set. A ogni macchina virtuale nel set di disponibilità viene assegnato un dominio di aggiornamento e un dominio di errore dalla piattaforma Azure sottostante. Ogni dominio di aggiornamento è un gruppo di macchine virtuali che verranno riavviate nello stesso intervallo di tempo. Ogni dominio di errore è un gruppo di macchine virtuali che condividono un'unità di alimentazione o un commutatore di rete comune.

Per altre informazioni sui domini di aggiornamento e i domini di errore, vedere "<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">Configurare più macchine virtuali in un set di disponibilità per la ridondanza</a>".

Microsoft Azure garantisce che nessun evento di manutenzione pianificata farà in modo che le macchine virtuali di due domini di aggiornamento diversi saranno offline contemporaneamente. La manutenzione viene eseguita arrestando ogni macchina virtuale, applicando l'aggiornamento ai computer host, riavviando la macchina virtuale e passando al dominio di aggiornamento successivo. L'evento di manutenzione pianificata termina quando tutti i domini di aggiornamento sono stati aggiornati. L'ordine in cui vengono riavviati i domini di aggiornamento può non essere sequenziale durante la manutenzione pianificata ma verrà riavviato un solo dominio di aggiornamento alla volta. Attualmente, Azure offre un servizio di notifica avanzata di 48 ore per la manutenzione pianificata delle macchine virtuali nella configurazione a istanza multipla.

Di seguito è riportato un esempio di ciò che può essere visualizzato nel Visualizzatore eventi di Windows dopo il ripristino della macchina virtuale:

<!--Image reference-->
![][image2]

## Aggiornamento a istanza singola
Una volta completati gli aggiornamenti a istanza multipla, Azure eseguirà l'aggiornamento nel set di computer host che ospitano le macchine virtuali a istanza singola. Anche questo aggiornamento comporterà un riavvio delle macchine virtuali che non sono in esecuzione nei set di disponibilità. Tenere presente che anche se si ha una sola istanza in esecuzione in un set di disponibilità, essa verrà comunque considerata come istanza multipla dalla piattaforma Azure. In una configurazione a istanza singola le macchine virtuali vengono aggiornate arrestando le macchine virtuali, applicando l'aggiornamento al computer host e riavviando la macchina virtuale. Questi aggiornamenti vengono eseguiti in tutte le macchine virtuali di un'area in una singola finestra di manutenzione. Questo evento di manutenzione pianificata influirà sulla disponibilità dell'applicazione per questo tipo di configurazione di macchina virtuale. Azure offre un servizio di notifica avanzato di una settimana per la manutenzione pianificata delle macchine virtuali nella configurazione a istanza singola.
 
### Notifica tramite posta elettronica
Solo per le macchine virtuali a istanza singola e a istanza multipla, Azure invia una comunicazione tramite posta elettronica in anticipo, per avvisare l’utente della manutenzione pianificata imminente (con un anticipo di 1 settimana per istanze singole e di 48 ore per istanze multiple). Questo messaggio di posta elettronica verrà inviato all'account di posta elettronica principale fornito dalla sottoscrizione. Di seguito è riportato un esempio di questo tipo di messaggio di posta elettronica:

<!--Image reference-->
![][image1]

## Coppie di aree
Azure organizza un set di coppie di aree e garantisce che, per gli aggiornamenti di istanze singole, solo un'area della coppia sarà sottoposta a manutenzione pianificata. Azure non implementerà un aggiornamento contemporaneo di coppie di aree durante una manutenzione pianificata di macchine virtuali a istanza singola. Fare riferimento alla tabella seguente per informazioni relative alle coppie di aree correnti:

Area 1 | Area 2
:----- | ------:
Stati Uniti centro-settentrionali | Stati Uniti centro-meridionali
Stati Uniti Orientali | Stati Uniti occidentali
Stati Uniti orientali 2 | Stati Uniti centrali
Europa settentrionale | Europa occidentale
Asia sudorientale | Asia orientale
Cina orientale | Cina settentrionale
Giappone orientale | Giappone occidentale
Brasile meridionale | Stati Uniti centro-meridionali
Australia sudorientale | Australia orientale

Ad esempio, durante un'implementazione di manutenzione pianificata, Azure non implementerà un aggiornamento di Stati Uniti occidentali se Stati Uniti orientali è sottoposto a manutenzione contemporaneamente. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali.

<!--Anchors-->
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG


<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[confronto tra manutenzione pianificata e non pianificata]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
 

<!---HONumber=July15_HO1-->