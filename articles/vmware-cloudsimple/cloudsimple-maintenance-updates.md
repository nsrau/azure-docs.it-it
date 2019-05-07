---
title: Soluzione Azure di VMware da CloudSimple - CloudSimple manutenzione e aggiornamenti
description: Descrive il processo di servizio CloudSimple per aggiornamenti e manutenzione pianificata
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160246"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Gli aggiornamenti e manutenzione CloudSimple

L'ambiente cloud privato è progettato per non avere nessun singolo punto di errore:

* ESXi cluster sono configurati con la disponibilità elevata di vSphere. I cluster vengono ridimensionati per disporre di almeno un nodo di riserva per garantire la resilienza.
* Con ridondanza della risorsa di archiviazione primaria viene fornito dalla rete vSAN, che richiede almeno tre nodi per garantire la protezione contro un singolo errore. rete vSAN può essere configurato per fornire maggiore resilienza per cluster di maggiori dimensioni.
* vCenter PSC e le macchine virtuali NSX Manager sono configurate con criteri di archiviazione RAID 10 per proteggere dagli errori di archiviazione. Le macchine virtuali sono protette contro gli errori di nodo/di rete da vSphere a disponibilità elevata.
* Gli host ESXi hanno ventole ridondanti e schede di rete.
* Commutatori TOR e spina vengono configurati in coppie a disponibilità elevata per offrire resilienza.

CloudSimple consente di monitorare le macchine virtuali seguenti per la disponibilità e tempi di attività in modo continuo e garantisce una disponibilità contratti di servizio:

* Host ESXi
* vCenter
* PSC
* NSX Manager

CloudSimple monitora anche le seguenti operazioni in modo continuativo per gli errori:

* Dischi rigidi
* Porte NIC fisiche
* Server
* Ventole
* Potenza
* Switch
* Porte del commutatore

Se un disco o un nodo ha esito negativo, viene automaticamente aggiunto un nuovo nodo al cluster VMware interessato per spostarli di nuovo in integrità immediatamente.

CloudSimple esegue il backup, gestisce e aggiorna tali elementi VMware nei cloud privati:

* ESXi
* vCenter Platform Services
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Eseguire il backup e ripristino

Backup CloudSimple include:

* I backup incrementali ogni notte di vCenter e PSC DVS regole.
* Uso di vCenter API native per eseguire il backup dei componenti al livello dell'applicazione.
* Backup automatico prima di qualsiasi aggiornamento o l'aggiornamento del software di gestione di VMware.
* Crittografia dei dati nell'origine, da vCenter, prima di trasferire i dati su un canale crittografato TLS 1.2 in Azure. I dati vengono archiviati in un blob di Azure dove viene replicato in aree geografiche.

È possibile richiedere un ripristino, aprire una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Manutenzione 

CloudSimple esegue diversi tipi di manutenzione pianificata.

### <a name="backendinternal-maintenance"></a>Manutenzione di back-end/interno

Tali operazioni di manutenzione in genere comporta la riconfigurazione di asset fisico o l'installazione di patch software. E non influisce sul consumo delle risorse in fase di manutenzione. Con schede di rete dovrà ogni rack fisico ridondanti, non sono interessate normale traffico di rete e operazioni basati su cloud privato. È possibile notare un impatto sulle prestazioni solo se l'organizzazione prevede di usare la larghezza di banda completa ridondante durante l'intervallo di manutenzione.

### <a name="cloudsimple-portal-maintenance"></a>Manutenzione portale CloudSimple

Tempo di inattività limitato del servizio è obbligatorio quando si aggiorna il piano di controllo CloudSimple o dell'infrastruttura. Attualmente, gli intervalli di manutenzione possono essere di frequenti come una volta al mese. La frequenza è previsto per rifiutare nel corso del tempo. CloudSimple fornisce la notifica per la manutenzione del portale e mantiene l'intervallo più breve possibile. Durante un intervallo di manutenzione del portale, i servizi seguenti continuano a funzionare senza alcuna conseguenza:

* Le applicazioni e il piano di gestione di VMware
* accesso a vCenter
* Tutte le funzionalità di rete e archiviazione
* Tutto il traffico di Azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenzione dell'infrastruttura VMware

In alcuni casi è necessario apportare modifiche alla configurazione dell'infrastruttura VMware.  Attualmente, questi intervalli possono verificarsi ogni 1-2 mesi, ma la frequenza è previsto per rifiutare nel corso del tempo. Questo tipo di manutenzione può essere in genere eseguito senza interrompere il normale utilizzo dei servizi CloudSimple. Durante un intervallo di manutenzione di VMware, i servizi seguenti continuano a funzionare senza alcuna conseguenza:

* Le applicazioni e il piano di gestione di VMware
* accesso a vCenter
* Tutte le funzionalità di rete e archiviazione
* Tutto il traffico di Azure

## <a name="updates-and-upgrades"></a>Gli aggiornamenti

CloudSimple è responsabile della gestione del ciclo di vita del software di VMware (ESXi vCenter, PSC e NSX) in un cloud privato.

Gli aggiornamenti software includono:

* **Patch**. Patch di sicurezza o correzioni di bug rilasciate da VMware.
* **Gli aggiornamenti**. Modifica della versione secondaria di un componente dello stack VMware.
* **Gli aggiornamenti**. Modifica della versione principale di un componente dello stack VMware.

CloudSimple testa una patch di sicurezza critiche, non appena diventa disponibile da VMware. Per ogni contratto di servizio, CloudSimple implementa la patch di protezione per ambienti cloud privati all'interno di una settimana.

CloudSimple fornisce aggiornamenti di manutenzione trimestrale per i componenti software di VMware. Quando è disponibile una nuova versione principale del software di VMware, CloudSimple funziona con i clienti per coordinare una finestra di manutenzione appropriato per l'aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

[Eseguire il backup di macchine virtuali del carico di lavoro con Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).