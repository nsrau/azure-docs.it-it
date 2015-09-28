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
	ms.date="07/23/2015"
	ms.author="kenazk"/>


# Manutenzione pianificata per le macchine virtuali di Azure

## Perché Azure esegue la manutenzione pianificata
<p> Microsoft Azure esegue periodicamente aggiornamenti a livello globale per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host sottostante alle macchine virtuali. Molti di questi aggiornamenti vengono eseguiti senza alcun impatto sulle macchine virtuali o sui servizi cloud, inclusi gli aggiornamenti con mantenimento della memoria.

Tuttavia, alcuni aggiornamenti richiedono un riavvio delle macchine virtuali per applicare gli aggiornamenti necessari all'infrastruttura. Le macchine virtuali vengono arrestate durante l'applicazione delle patch all'infrastruttura e quindi riavviate.

Esistono due tipi di manutenzione che possono influire sulla disponibilità delle macchine virtuali: pianificata e non pianificata. Questa pagina descrive come Microsoft Azure esegue la manutenzione pianificata. Per altre informazioni sulla manutenzione non pianificata, vedere la pagina relativa al [confronto tra manutenzione pianificata e non pianificata](virtual-machines-manage-availability.md).

## Aggiornamenti con mantenimento della memoria
Per una classe di aggiornamenti in Microsoft Azure, i clienti non visualizzeranno alcun impatto sulle macchine virtuali in esecuzione. Molti di questi aggiornamenti sono componenti o servizi che possono essere aggiornati senza interferire con l'istanza in esecuzione. Alcuni di questi aggiornamenti sono aggiornamenti dell’infrastruttura della piattaforma nel sistema operativo host che possono essere applicati senza richiedere un riavvio completo delle macchine virtuali.

Questi aggiornamenti vengono eseguiti con la tecnologia che consente la migrazione in tempo reale (un aggiornamento con "mantenimento della memoria"). Durante l'aggiornamento, la macchina virtuale viene inserita in uno stato "sospeso", mantenendo la memoria in RAM, mentre il sistema operativo host sottostante riceve gli aggiornamenti e le patch necessari. La macchina virtuale viene ripresa dopo un periodo di pausa massimo di 30 secondi. Dopo la ripresa, l’orologio della macchina virtuale viene sincronizzato automaticamente.

Non tutti gli aggiornamenti possono essere distribuiti utilizzando questo meccanismo, ma dato il breve periodo di pausa, la distribuzione degli aggiornamenti in questo modo riduce notevolmente l’impatto sulle macchine virtuali.

Gli aggiornamenti a istanza multipla (per le macchine virtuali in un set di disponibilità) vengono applicati su un dominio di aggiornamento alla volta.

## Configurazioni delle macchine virtuali
Esistono due tipi di configurazioni delle macchine virtuali: a istanza multipla e a istanza singola. In una configurazione a istanza multipla, le macchine virtuali simili vengono inserite in un set di disponibilità.

La configurazione a istanza multipla fornisce la ridondanza ed è consigliata per garantire la disponibilità dell'applicazione. Tutte le macchine virtuali nel set di disponibilità devono essere quasi identiche e avere la stessa funzione nell'applicazione.

Per ulteriori informazioni sulla configurazione delle macchine virtuali per la disponibilità elevata, vedere [Gestione della disponibilità delle macchine virtuali](virtual-machines-manage-availability.md).

Al contrario, una configurazione a istanza singola viene utilizzata per le macchine virtuali autonome che non vengono inserite in un set di disponibilità. Tali macchine virtuali non sono idonee per il contratto di servizio che richiede due o più macchine virtuali distribuite nello stesso set di disponibilità.

Per ulteriori informazioni sul contratto di servizio, vedere la sezione "Servizi cloud, macchine virtuali e rete virtuale" in [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/).


## Aggiornamenti delle configurazioni a istanza multipla
Durante la manutenzione pianificata, la piattaforma Azure aggiorna innanzitutto il set di macchine virtuali ospitate in una configurazione a istanza multipla. Ciò causa un riavvio di queste macchine virtuali.

In un aggiornamento della configurazione a istanza multipla, le macchine virtuali vengono aggiornate in modo tale da preservare la disponibilità per tutto il processo, supponendo che ciascuna macchina virtuale abbia una funzione simile a quella delle altre del set.

A ciascuna macchina virtuale nel set di disponibilità viene assegnato un dominio di aggiornamento e un dominio di errore dalla piattaforma Azure sottostante. Ciascun dominio di aggiornamento è un gruppo di macchine virtuali che verranno riavviate nello stesso intervallo di tempo. Ciascun dominio di errore è un gruppo di macchine virtuali che condividono un'unità di alimentazione o un commutatore di rete comune.

Per ulteriori informazioni sui domini di aggiornamento e sui domini di errore, vedere [Configurazione di più macchine virtuali in un set di disponibilità per la ridondanza](virtual-machines-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)

Per impedire che i domini di aggiornamento passino offline contemporaneamente, la manutenzione viene eseguita arrestando tutte le macchine virtuali in un dominio di aggiornamento, applicando l'aggiornamento sulle macchine host, riavviando le macchine virtuali e passando al dominio di aggiornamento successivo. L'evento di manutenzione pianificata termina quando tutti i domini di aggiornamento sono stati aggiornati.

L'ordine dei domini di aggiornamento in corso di riavvio potrebbe non procedere in modo sequenziale durante la manutenzione pianificata, ma viene riavviato un solo dominio di aggiornamento alla volta. Attualmente, Azure offre un servizio di notifica avanzata di 48 ore per la manutenzione pianificata delle macchine virtuali nella configurazione a istanza multipla.

Di seguito viene riportato un esempio di ciò che Visualizzatore eventi di Windows potrebbe visualizzare al termine del ripristino di una macchina virtuale:

<!--Image reference-->
![][image2]

## Aggiornamenti della configurazione a istanza singola
Una volta completati gli aggiornamenti della configurazione a istanza multipla, Azure eseguirà gli aggiornamenti della configurazione a istanza singola. Anche questo aggiornamento comporta un riavvio delle macchine virtuali che non sono in esecuzione nei set di disponibilità.

Anche se si dispone di una sola istanza in esecuzione in un set di disponibilità, la piattaforma Azure considera l’aggiornamento come aggiornamento della configurazione a istanza multipla.

In una configurazione a istanza singola le macchine virtuali vengono aggiornate arrestando le macchine virtuali, applicando l'aggiornamento al computer host e riavviando le macchine virtuali. Questi aggiornamenti vengono eseguiti in tutte le macchine virtuali di un'area in una singola finestra di manutenzione.

Questo evento di manutenzione pianificata influirà sulla disponibilità dell'applicazione per questo tipo di configurazione di macchina virtuale. Azure offre un servizio di notifica avanzato di una settimana per la manutenzione pianificata delle macchine virtuali nella configurazione a istanza singola.

### Notifica tramite posta elettronica
Solo per le configurazioni delle macchine virtuali a istanza singola e a istanza multipla, Azure invia una comunicazione tramite posta elettronica in anticipo, per avvisare della manutenzione pianificata imminente (con un anticipo di 1 settimana per istanze singole e di 48 ore per istanze multiple). Questo messaggio di posta elettronica verrà inviato agli account di posta elettronica dell’amministratore e del co-amministratore dell’account forniti nella sottoscrizione. Di seguito viene riportato un esempio di questo tipo di messaggio di posta elettronica:

<!--Image reference-->
![][image1]

## Coppie di aree
Azure consente di organizzare un set di coppie di aree. Azure non implementerà contemporaneamente un aggiornamento su coppie di aree durante una manutenzione pianificata delle macchine virtuali con configurazioni a istanza singola.

Consultare la tabella seguente per informazioni relative alle coppie di aree correnti:

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
Governo degli Stati Uniti - Iowa | Governo degli Stati Uniti - Virginia

Ad esempio, durante una manutenzione pianificata, Azure non implementerà un aggiornamento su Stati Uniti occidentali se Stati Uniti orientali è sottoposto contemporaneamente a manutenzione. Tuttavia, altre aree, ad esempio Europa settentrionale, possono essere sottoposte a manutenzione contemporaneamente a Stati Uniti orientali.

<!--Anchors-->
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG


<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=Sept15_HO3-->