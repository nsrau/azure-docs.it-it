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
	ms.date="11/26/2014" 
	ms.author="kenazk"/>


# Manutenzione pianificata per le macchine virtuali di Azure

## Perché Azure esegue la manutenzione pianificata
<p> Microsoft Azure esegue periodicamente aggiornamenti in tutto il mondo per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host sottostante alle macchine virtuali. Molti di questi aggiornamenti vengono eseguiti senza alcun impatto sulle macchine virtuali o sui servizi cloud. Tuttavia, alcuni di questi aggiornamenti richiedono un riavvio della macchina virtuale per applicare gli aggiornamenti necessari all'infrastruttura. La macchina virtuale verrà arrestata durante l'applicazione delle patch all'infrastruttura e quindi riavviata. Si noti che esistono due tipi di manutenzione che possono influire sulla disponibilità della macchina virtuale: pianificata e non pianificata. Questa pagina descrive come Microsoft Azure esegue la manutenzione pianificata. Per altre informazioni sulla manutenzione non pianificata, vedere [Confronto tra manutenzione pianificata e manutenzione non pianificata].

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

* [Configurazioni di macchina virtuale]  
* [Aggiornamento a istanza multipla]
* [Aggiornamento a istanza singola]
* [Notifica tramite posta elettronica]


## Configurazioni di macchina virtuale
Esistono due tipi di configurazioni di macchina virtuale: a istanza multipla e a istanza singola.  Le macchine virtuali a istanza multipla vengono configurate inserendo le macchine virtuali identiche in un set di disponibilità. La configurazione a istanza multipla fornisce la ridondanza ed è consigliata per garantire la disponibilità dell'applicazione. Tutte le macchine virtuali nel set di disponibilità devono essere quasi identiche e servire allo stesso scopo nell'applicazione. Per altre informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere "<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/">Gestire la disponibilità delle macchine virtuali</a>". 

Al contrario, le macchine virtuali a istanza singola sono macchine virtuali autonome che non vengono inserite in un set di disponibilità. Le macchine virtuali a istanza singola non sono di per sé idonee per il contratto di servizio che richiede due o più macchine virtuali distribuite nello stesso set di disponibilità. Per altre informazioni sul contratto di servizio, vedere la sezione "Servizi cloud, macchine virtuali e rete virtuale" in [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/).


## Aggiornamento a istanza multipla
Durante la manutenzione pianificata la piattaforma Azure eseguirà innanzitutto l'aggiornamento del set di computer host che ospitano il set di macchine virtuali in una configurazione a istanza multipla comportando il riavvio di queste macchine virtuali. In una configurazione a istanza multipla le macchine virtuali vengono aggiornate in modo tale da preservare la disponibilità per tutto il processo, supponendo che ogni computer svolga una funzione simile a quella degli altri computer del set. A ogni macchina virtuale nel set di disponibilità viene assegnato un dominio di aggiornamento e un dominio di errore dalla piattaforma Azure sottostante. Ogni dominio di aggiornamento è un gruppo di macchine virtuali che verranno riavviate nello stesso intervallo di tempo. Ogni dominio di errore è un gruppo di macchine virtuali che condividono un'unità di alimentazione o un commutatore di rete comune. 

Per altre informazioni sui domini di aggiornamento e i domini di errore, vedere "<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">Configurare più macchine virtuali in un set di disponibilità per la ridondanza</a>".

Microsoft Azure garantisce che nessun evento di manutenzione pianificata farà in modo che le macchine virtuali di due domini di aggiornamento diversi saranno offline contemporaneamente. La manutenzione viene eseguita arrestando ogni macchina virtuale, applicando l'aggiornamento ai computer host, riavviando la macchina virtuale e passando al dominio di aggiornamento successivo. L'evento di manutenzione pianificata termina quando tutti i domini di aggiornamento sono stati aggiornati. L'ordine in cui vengono riavviati i domini di aggiornamento può non essere sequenziale durante la manutenzione pianificata ma verrà riavviato un solo dominio di aggiornamento alla volta. Attualmente, non viene fornito alcun preavviso di manutenzione pianificata per le macchine virtuali nella configurazione a istanza multipla.

Di seguito è riportato un esempio di ciò che può essere visualizzato nel Visualizzatore eventi di Windows dopo il ripristino della macchina virtuale:

<!--Image reference-->
![][image2]

## Aggiornamento a istanza singola
Una volta completati gli aggiornamenti a istanza multipla, Azure eseguirà l'aggiornamento nel set di computer host che ospitano le macchine virtuali a istanza singola. Anche questo aggiornamento comporterà un riavvio delle macchine virtuali che non sono in esecuzione nei set di disponibilità. Si noti che anche se si ha una sola istanza in esecuzione in un set di disponibilità, essa verrà comunque considerata come istanza multipla dalla piattaforma Azure. In una configurazione a istanza singola le macchine virtuali vengono aggiornate arrestando le macchine virtuali, applicando l'aggiornamento al computer host e riavviando la macchina virtuale. Questi aggiornamenti vengono eseguiti in tutte le macchine virtuali di un'area in una singola finestra di manutenzione. Questo evento di manutenzione pianificata influirà sulla disponibilità dell'applicazione per questo tipo di configurazione di macchina virtuale. 
 
### Notifica tramite posta elettronica
Solo per le macchine virtuali con configurazione a istanza singola Azure invierà una comunicazione tramite posta elettronica con un preavviso di almeno una settimana per informare dell'imminente manutenzione pianificata. Questo messaggio di posta elettronica verrà inviato all'account di posta elettronica principale fornito dalla sottoscrizione. Di seguito è riportato un esempio di questo tipo di messaggio di posta elettronica:

<!--Image reference-->
![][image1]

<!--Anchors-->
[Perché Azure esegue la manutenzione pianificata]: #why-azure-performs-planned-maintenance
[Configurazioni di macchina virtuale]: #virtual-machine-configurations
[Aggiornamento a istanza multipla]: #multi-instance-update
[Aggiornamento a istanza singola]: #single-instance-update
[Notifica tramite posta elettronica]: #email-notification
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png


<!--Link references-->
[Disponibilità di gestione delle macchine virtuali]: ../virtual-machines-windows-tutorial/
[Confronto tra manutenzione pianificata e manutenzione non pianificata]: ../virtual-machines-manage-availability/#Understand-planned-versus-unplanned-maintenance/ 

<!--HONumber=45--> 
