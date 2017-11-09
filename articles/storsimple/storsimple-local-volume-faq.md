---
title: Domande frequenti sui volumi aggiunti in locale di StorSimple|Microsoft Docs
description: Fornisce risposte alle domande frequenti sui volumi aggiunti in locale di StorSimple.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: 
ms.assetid: 7a2f4b1a-4ac9-4ce1-9359-bd40a9cbbb5d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/11/2017
ms.author: manuaery
ms.openlocfilehash: 22eb95bf0e3a20893acbb860ad10cfe6a3bcf088
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>Volumi aggiunti in locale di StorSimple: domande frequenti
## <a name="overview"></a>Panoramica
Ecco alcune domande e risposte relative alla creazione di un volume aggiunto in locale di StorSimple, alla conversione di un volume a livelli in un volume aggiunto in locale (e viceversa) o al backup e ripristino di un volume aggiunto in locale.

Le domande e le risposte sono suddivise nelle categorie seguenti

* Creazione di un volume aggiunto in locale
* Backup di un volume aggiunto in locale
* Conversione di un volume a livelli in un volume aggiunto in locale
* Ripristino di un volume aggiunto in locale
* Failover di un volume aggiunto in locale

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Domande sulla creazione di un volume aggiunto in locale
**D.** Qual è la dimensione massima di un volume aggiunto in locale che è possibile creare nei dispositivi della serie 8000?

**R** Nei dispositivi che eseguono l'aggiornamento 3.0 di StorSimple serie 8000 è possibile effettuare il provisioning di volumi aggiunti in locale fino a 8,5 TB o di volumi a livelli fino a 200 TB nel dispositivo 8100. Nel dispositivo 8600 più grande è possibile effettuare il provisioning di volumi aggiunti in locale fino a 22,5 TB o di volumi a livelli fino a 500 TB.    
Nei dispositivi che eseguono l'aggiornamento 2.x di StorSimple serie 8000 è possibile effettuare il provisioning di volumi aggiunti in locale fino a 8 TB o di volumi a livelli fino a 200 TB nel dispositivo 8100. Nel dispositivo 8600 più grande è possibile effettuare il provisioning di volumi aggiunti in locale fino a 20 TB o di volumi a livelli fino a 500 TB.   

**D.** Quando si cerca di creare un volume aggiunto in locale in un dispositivo 8100 recentemente sottoposto all'aggiornamento 2.0, la massima dimensione disponibile è solo di 6 TB e non di 8 TB. Perché non è possibile creare un volume di 8 TB?

**R** Nei dispositivi che eseguono l'aggiornamento 2.0 è possibile effettuare il provisioning di volumi aggiunti in locale fino a 8 TB O di volumi a livelli fino a 200 TB nel dispositivo 8100. Se il dispositivo include già volumi a livello, lo spazio disponibile per la creazione di un volume aggiunto in locale sarà proporzionalmente più bassa di questo limite massimo. Se, ad esempio, è già stato effettuato il provisioning di 100 TB di volumi a livelli nel dispositivo 8100 (pari a metà della capacità a livelli), la dimensione massima di un volume locale che è possibile creare nel dispositivo 8100 sarà di conseguenza ridotta a 4 TB (circa metà della capacità massima del volume aggiunto in locale).

Poiché parte dello spazio locale del dispositivo viene usato per ospitare il working set di volumi a livelli, lo spazio disponibile per la creazione di un volume aggiunto in locale è ridotta se il dispositivo include volumi a livelli. Al contrario, la creazione di un volume aggiunto in locale riduce in proporzione lo spazio disponibile per i volumi a livelli. La tabella seguente riepiloga la capacità a livelli disponibile nei dispositivi 8100 e 8600 quando vengono creati volumi aggiunti in locale.

####<a name="update-30"></a>Aggiornamento 3.0 
| Capacità volumi aggiunti in locale di cui è stato effettuato il provisioning | Capacità disponibile di cui effettuare il provisioning per volumi a livelli - 8100 | Capacità disponibile di cui effettuare il provisioning per volumi a livelli - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8.5 TB |0 TB |311.1 TB |
| 10 TB |ND |277.8 TB |
| 15 TB |ND |166.7 TB |
| 22.5 TB |ND |0 TB |

####<a name="update-2x"></a>Aggiornamento 2.x  
 | Capacità volumi aggiunti in locale di cui è stato effettuato il provisioning | Capacità disponibile di cui effettuare il provisioning per volumi a livelli - 8100 | Capacità disponibile di cui effettuare il provisioning per volumi a livelli - 8600 |  
 | --- | --- | --- |  
 | 0 |200 TB |500 TB |  
 | 1 TB |25 TB |475 TB |  
 | 4 TB |100 TB |400 TB |  
 | 8 TB |0 TB |300 TB |  
 | 10 TB |ND |250 TB |  
 | 15 TB |ND |125 TB |  
 | 20 TB |ND |0 TB |   

**D.** Perché la creazione di un volume aggiunto in locale è un'operazione di lunga durata? 

**R.** Viene effettuato il thick provisioning dei volumi aggiunti in locale. Per creare spazio nei livelli locali del dispositivo, è possibile che venga effettuato il push di alcuni dati dai volumi a livelli esistenti al cloud durante il processo di provisioning, e, poiché questo dipende dalla dimensione del volume di cui effettuare il provisioning, dai dati esistenti nel dispositivo e dalla larghezza di banda disponibile per il cloud, la creazione di un volume locale può richiedere diverse ore.

**D.** Quanto tempo è necessario per creare un volume aggiunto in locale?

**R.** Poiché viene effettuato il thick provisioning dei volumi aggiunti in locale, è possibile che venga effettuato il push di alcuni dati esistenti dai volumi a livelli al cloud durante il processo di provisioning. Il tempo necessario per creare un volume aggiunto in locale dipende quindi da più fattori, inclusi la dimensione del volume, i dati sul dispositivo e la larghezza di banda disponibile. In un dispositivo appena installato senza volumi, il tempo necessario per creare un volume aggiunto in locale è di circa 10 minuti per ogni terabyte di dati. La creazione di volumi locali, tuttavia, può richiedere diverse ore a seconda dei fattori illustrati sopra in un dispositivo in uso.

**D.** Si vuole creare un volume aggiunto in locale. Esistono procedure consigliate da tenere in considerazione?

**R.** I volumi aggiunti in locale sono adatti ai carichi di lavoro che richiedono sempre garanzie locali per i dati e sono sensibili alle latenze cloud. Quando si considera l'utilizzo di volumi locali per i carichi di lavoro, tenere presente quanto segue:

* Viene effettuato il thick provisioning dei volumi aggiunti in locale e la creazione di volumi locali ha effetto sullo spazio disponibile per i volumi a livelli. È quindi consigliabile iniziare con volumi più piccoli e aumentarli parallelamente ai requisiti di archiviazione.
* Il provisioning di volumi locali è un'operazione di lunga durata che può comportare il push dei dati esistenti dai volumi a livelli al cloud. Di conseguenza, questi volumi possono subire un calo delle prestazioni.
* Il provisioning di volumi locali è un'operazione dispendiosa in termini di tempo. Il tempo effettivo richiesto dipende da più fattori: la dimensione del volume di cui effettuare il provisioning, i dati nel dispositivo e la larghezza di banda disponibile. Se non è stato eseguito il backup dei volumi esistenti nel cloud, la creazione dei volumi è più lenta. Prima del provisioning di un volume locale, è consigliabile acquisire snapshot nel cloud dei volumi esistenti.
* È possibile convertire i volumi a livelli esistenti in volumi aggiunti in locale. Questa conversione comporta il provisioning di spazio nel dispositivo per il volume aggiunto in locale risultante (oltre a trasferire eventuali dati a livello dal cloud). Questa è poi un'operazione di lunga durata che dipende dai fattori illustrati sopra. È consigliabile eseguire il backup dei volumi esistenti prima della conversione perché il processo sarà ancora più lento senza il backup dei volumi esistenti. Il dispositivo può anche subire un calo delle prestazioni durante questo processo.

Sono disponibili altre informazioni su come [creare un volume aggiunto in locale](storsimple-manage-volumes-u2.md#add-a-volume)

**D.** È possibile creare più volumi aggiunti in locale contemporaneamente?

**R.** Sì, ma tutti i processi di creazione ed espansione dei volumi aggiunti in locale vengono elaborati in sequenza.

Poiché viene effettuato il thick provisioning dei volumi aggiunti in locale, è necessario creare spazio locale nel dispositivo (con la possibilità che venga effettuato il push dei dati esistenti dai volumi a livelli al cloud durante il processo di provisioning). Se quindi è in corso un processo di provisioning, gli altri processi di creazione di volumi locali verranno accodati fino al termine di tale processo.

Allo stesso modo, se è in corso l'espansione di un volume locale esistente o la conversione di un volume a livelli in un volume aggiunto in locale, la creazione di un nuovo volume aggiunto in locale viene accodata fino al completamento del processo precedente. L'espansione di un volume aggiunto in locale comporta l'espansione dello spazio locale esistente per tale volume. La conversione da un volume a livelli a uno aggiunto in locale comporta anche la creazione di spazio locale per il volume aggiunto in locale risultante. In entrambe le operazioni la creazione o l'espansione dello spazio locale è un processo di lunga durata.

È possibile visualizzare questi processi nella pagina **Processi** del servizio Azure StorSimple Manager. Il processo di cui è in corso l'elaborazione viene continuamente aggiornato per riflettere l'avanzamento del provisioning dello spazio. I rimanenti processi dei volumi aggiunti in locale vengono contrassegnati come in esecuzione, ma l'avanzamento viene bloccato e i processi vengono prelevati in base all'ordine di accodamento.

**D.** È stato eliminato un volume aggiunto in locale. Perché lo spazio recuperato non viene visualizzato nello spazio disponibile quando si cerca di creare un nuovo volume? 

**R.** Se si elimina un volume aggiunto in locale, lo spazio disponibile per i nuovi volumi potrebbe non essere immediatamente aggiornato. Il servizio StorSimple Manager aggiorna lo spazio locale disponibile circa ogni ora. È consigliabile attendere un'ora prima di creare il nuovo volume.

**D.** I volumi aggiunti in locale sono supportati nell'appliance cloud?

**R.** I volumi aggiunti in locale non sono supportati nell'appliance cloud (dispositivi 8010 e 8020 chiamati in precedenza dispositivi virtuali StorSimple).

**D.** È possibile usare i cmdlet di Azure PowerShell per creare e gestire volumi aggiunti in locale? 

**R.** No, non è possibile creare volumi aggiunti in locale con i cmdlet di Azure PowerShell. Eventuali volumi creati con Azure PowerShell sono a livelli. È anche consigliabile non usare i cmdlet di Azure PowerShell per modificare le proprietà di un volume aggiunto in locale, perché in questo modo il tipo di volume verrà modificato in volume a livelli.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Domande sul backup di un volume aggiunto in locale
**D.** Gli snapshot locali dei volumi aggiunti in locale sono supportati?

**R.** Sì, è possibile acquisire gli snapshot locali dei volumi aggiunti in locale. È tuttavia consigliabile eseguire periodicamente il backup dei volumi aggiunti in locale con gli snapshot cloud per assicurarsi che i dati siano protetti in caso di emergenza.

**D.** Esistono linee guida per la gestione degli snapshot locali per i volumi aggiunti in locale?

**R.** Snapshot locali frequenti insieme a un tasso elevato di varianza dati nel volume aggiunto in locale potrebbero comportare un rapido consumo dello spazio locale nel dispositivo e il push dei dati dai volumi a livelli al cloud. È quindi consigliabile ridurre al minimo il numero di snapshot locali.

**D.** È stato ricevuto un avviso che informa che gli snapshot locali dei volumi aggiunti in locale potrebbero essere invalidati. Quando può accadere?

**R.** Snapshot locali frequenti insieme a un tasso elevato di varianza dati nel volume aggiunto in locale potrebbero comportare un rapido consumo dello spazio locale nel dispositivo. Se i livelli locali del dispositivo vengono usati molto, con un'interruzione estesa del cloud è possibile che il dispositivo si riempia e le operazioni di scrittura in ingresso nel volume potrebbero comportare l'invalidamento degli snapshot (perché non esiste spazio per aggiornare gli snapshot in modo che facciano riferimento ai precedenti blocchi di dati che sono stati sovrascritti). In una situazione di questo genere, le scritture nel volume continueranno a essere supportate, ma gli snapshot locali potrebbero non essere validi. Non ci sono conseguenze sugli snapshot cloud esistenti.

L'avviso serve a notificare che tale situazione può verificarsi e ad assicurarsi che venga affrontata tempestivamente riesaminando le pianificazioni degli snapshot locali per acquisirli meno di frequente o eliminando gli snapshot locali precedenti non più necessari.

Se gli snapshot locali vengono invalidati, si riceverà un avviso informativo che notifica che gli snapshot locali per il criterio di backup specifico sono stati invalidati insieme all'elenco di timestamp degli snapshot locali invalidati. Questi snapshot verranno automaticamente eliminati e non sarà più possibile visualizzarli nella pagina **Cataloghi di backup** nel portale di Azure classico.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Domande sulla conversione di un volume a livelli in un volume aggiunto in locale
**D.** È stata osservata una certa lentezza sul dispositivo durante la conversione di un volume a livelli in un volume aggiunto in locale. Perché accade? 

**R.** Il processo di conversione include due passaggi: 

1. Provisioning di spazio sul dispositivo per il volume aggiunto in locale da convertire a breve.
2. Download dei dati a livelli dal cloud per assicurare le garanzie locali.

Entrambi i passaggi sono operazioni di lunga durata che dipendono dalla dimensione del volume da convertire, dai dati sul dispositivo e dalla larghezza di banda disponibile. Poiché alcuni dati dei volumi a livelli esistenti possono essere distribuiti nel cloud durante il processo di provisioning, è possibile che il dispositivo subisca un calo delle prestazioni in questa fase. Il processo di conversione può essere più lento anche quando:

* Non è stato eseguito il backup dei volumi esistenti nel cloud. È quindi consigliabile eseguire il backup dei volumi prima di avviare una conversione.
* Sono stati applicati criteri di limitazione della larghezza di banda, che possono vincolare la larghezza di banda disponibile al cloud. È quindi consigliabile avere una connessione al cloud dedicata di almeno 40 Mbps.
* Il processo di conversione può richiedere diverse ore a causa dei numerosi fattori illustrati sopra, quindi è consigliabile eseguire questa operazione in momenti non di picco o nel fine settimana per evitare l'impatto sugli utenti finali.

Sono disponibili altre informazioni su come [convertire un volume a livelli in un volume aggiunto in locale](storsimple-manage-volumes-u2.md#change-the-volume-type)

**D.** È possibile annullare l'operazione di conversione del volume?

**R.** No, non è possibile annullare l'operazione di conversione una volta avviata. Come descritto nella domanda precedente, tenere presenti i potenziali problemi di prestazioni che potrebbero sorgere durante il processo e seguire le procedure consigliate elencate sopra quando si pianifica la conversione.

**D.** Che cosa accade al volume se l'operazione di conversione non riesce?

**R.** La conversione del volume può non riuscire a causa di problemi di connettività cloud. Il dispositivo potrebbe arrestare il processo di conversione dopo una serie di tentativi non riusciti di trasferire i dati a livelli dal cloud. In tale scenario il tipo di volume continuerà a essere il tipo di volume di origine precedente alla conversione e:

* Verrà generato un avviso critico per notificare l'errore di conversione del volume. Sono disponibili altre informazioni sugli [avvisi correlati ai volumi aggiunti in locale](storsimple-manage-alerts.md#locally-pinned-volume-alerts)
* Se si sta convertendo un volume a livelli in uno aggiunto in locale, il volume continuerà a presentare le proprietà di un volume a livelli perché i dati potrebbero ancora trovarsi nel cloud. È consigliabile risolvere i problemi di connettività e quindi riprovare l'operazione di conversione.
* Analogamente, quando la conversione da un volume aggiunto in locale in uno a livelli non riesce, anche se il volume verrà contrassegnato come volume aggiunto in locale, funzionerà come volume a livelli (perché è possibile che i dati siano stati distribuiti nel cloud), ma continuerà a occupare spazio nei livelli locali del dispositivo. Questo spazio non sarà disponibile per gli altri volumi aggiunti in locale. È consigliabile ritentare questa operazione per assicurarsi che la conversione del volume sia stata completata e che lo spazio locale sul dispositivo possa essere recuperato.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Domande sul ripristino di un volume aggiunto in locale
**D.** I volumi aggiunti in locale vengono ripristinati immediatamente?

**R.** Sì, i volumi aggiunti in locale vengono ripristinati immediatamente. Non appena dal cloud viene effettuato il pull delle informazioni sui metadati per il volume durante l'operazione di ripristino, il volume passa online ed è accessibile dall'host, ma le garanzie locali per i dati del volume saranno presenti solo dopo che tutti i dati saranno stati scaricati dal cloud ed è possibile che questi volumi subiscano un calo delle prestazioni durante il ripristino.

**D.** Quanto tempo è necessario per ripristinare un volume aggiunto in locale?

**R.** I volumi aggiunti in locale vengono ripristinati immediatamente e passano online non appena le informazioni sui metadati dei volume vengono recuperate dal cloud, mentre i dati dei volumi continuano a essere scaricati in background. Quest'ultima parte dell'operazione di ripristino (ricevere le garanzie locali per i dati del volume) è di lunga durata e possono essere necessarie diverse ore per impostare nuovamente tutti i dati come locali. Il tempo necessario per completare l'operazione dipende da più fattori, ad esempio la dimensione del volume da ripristinare e la larghezza di banda disponibile. Se il volume originale da ripristinare è stato eliminato, servirà altro tempo per creare lo spazio locale sul dispositivo durante l'operazione di ripristino.

**D.** È necessario ripristinare il volume esistente aggiunto in locale a uno snapshot precedente (acquisito quando il volume era a livelli). In questo caso, il volume verrà ripristinato a livelli?

**R.** No, il volume verrà ripristinato come volume aggiunto in locale. Anche se lo snapshot risale al periodo in cui il volume era a livelli, durante il ripristino dei volumi esistenti, StorSimple usa sempre il tipo di volume corrente sul disco.

**D.** Di recente il volume aggiunto in locale è stato esteso, ma ora è necessario ripristinare uno stato dei dati precedente, quando la dimensione era inferiore. Il ripristino ridimensionerà il volume corrente e sarà necessario estendere la dimensione del volume una volta completato il ripristino?

**R.** Sì, il ripristino ridimensionerà il volume e sarà necessario estendere la dimensione del volume una volta completato il ripristino.

**D.** È possibile modificare il tipo di un volume durante il ripristino?

**R.**No, non è possibile modificare il tipo di volume durante il ripristino.

* I volumi eliminati vengono ripristinati con il tipo archiviato nello snapshot.
* I volumi esistenti vengono ripristinati in base al tipo corrente, indipendentemente dal tipo archiviato nello snapshot (vedere le due domande precedenti).

**D.** È necessario ripristinare il volume aggiunto in locale, ma è stato selezionato uno snapshot temporizzato non corretto. È possibile annullare l'operazione di ripristino corrente?

**R.** Sì, è possibile annullare un'operazione di ripristino in corso. Verrà eseguito il rollback dello stato del volume in corrispondenza dell'avvio del ripristino, ma le operazioni di scrittura eseguite nel volume durante il ripristino andranno perse.

**D.** È stata avviata un'azione di ripristino su uno dei volumi aggiunti in locale e ora viene visualizzato uno snapshot nel catalogo backlog che non si ricorda di avere creato. A che cosa serve?

**R.** Si tratta dello snapshot temporaneo creato prima dell'operazione di ripristino e usato per il rollback nel caso in cui il ripristino venga annullato o non riesca. Non eliminare questo snapshot che verrà eliminato automaticamente al termine del ripristino. Questo comportamento può verificarsi se il processo di ripristino dispone solo di volumi associati in locale o di una combinazione di volumi associati in locale e a livelli. Se il processo di ripristino include solo i volumi a livelli, questo comportamento non si verificherà.

**D.** È possibile clonare un volume aggiunto in locale?

**R.** Sì, ma il volume aggiunto in locale verrà duplicato come volume a livelli per impostazione predefinita. Sono disponibili altre informazioni su come [clonare un volume aggiunto in locale](storsimple-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Domande sul failover di un volume aggiunto in locale
**D.** È necessario effettuare il failover del dispositivo in un altro dispositivo fisico. Dei volumi aggiunti in locale verrà effettuato il failover come volumi aggiunti in locale o a livelli?

**R.** A seconda della versione software del dispositivo di destinazione, il failover dei volumi aggiunti in locale verrà effettuato come:

* Aggiunti in locale se il dispositivo di destinazione esegue l'aggiornamento 2 della serie 8000 di StorSimple.
* A livelli se il dispositivo di destinazione esegue l'aggiornamento 1.x della serie 8000 di StorSimple.
* A livelli se il dispositivo di destinazione è l'appliance cloud (aggiornamento 2 o aggiornamento 1.x della versione software).

Sono disponibili altre informazioni sugli [failover e ripristino di emergenza dei volumi aggiunti in locale nelle diverse versioni](storsimple-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**D.** I volumi aggiunti in locale vengono ripristinati immediatamente durante il ripristino di emergenza?

**R.** Sì, i volumi aggiunti in locale vengono ripristinati immediatamente durante il failover. Non appena dal cloud viene effettuato il pull delle informazioni sui metadati per il volume durante l'operazione di failover, il volume passa online sul dispositivo di destinazione ed è accessibile dall'host. Nel frattempo, i dati del volume continueranno a essere scaricati in background ed è possibile che questi volumi subiscano un calo delle prestazioni durante il failover.

**D.** Il processo di failover è stato completato. Come è possibile tenere traccia dell'avanzamento del volume aggiunto in locale di cui è in corso il ripristino sul dispositivo di destinazione?

**R.** Durante un'operazione di failover, il processo di failover viene contrassegnato come completo una volta che tutti i volumi nel set di failover siano stati immediatamente ripristinati e siano passati online sul dispositivo di destinazione, inclusi i volumi aggiunti in locale di cui potrebbe essere stato effettuato il failover, ma le garanzie locali dei dati saranno disponibili solo quando tutti i dati del volume saranno stati scaricati. È possibile tenere traccia di questo avanzamento per ogni volume aggiunto in locale di cui è stato effettuato il failover monitorando i corrispondenti processi di ripristino creati durante il failover. Questi singoli processi di ripristino verranno creati solo per i volumi aggiunti in locale.

**D.** È possibile modificare il tipo di un volume durante il failover?

**R.** No, non è possibile modificare il tipo di volume durante un failover. Se si sta effettuando il failover in un altro dispositivo fisico che esegue l'aggiornamento 2 della serie 8000 di StorSimple, il failover dei volumi verrà effettuato in base al tipo di volume archiviato nello snapshot. Quando si effettua il failover in un'altra versione del dispositivo, vedere sopra la domanda sul tipo di volume dopo un failover.

**D.** È possibile effettuare il failover di un contenitore di volumi con volumi aggiunti in locale nell'appliance cloud?

**R.** Sì, Il failover dei volumi aggiunti in locale viene effettuato come volumi a livelli. Sono disponibili altre informazioni sugli [failover e ripristino di emergenza dei volumi aggiunti in locale nelle diverse versioni](storsimple-device-failover-disaster-recovery.md#considerations-for-device-failover)

