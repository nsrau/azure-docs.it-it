---
title: Soluzione VMware di Azure di CloudSimple-manutenzione e aggiornamenti di CloudSimple
description: Descrive il processo del servizio CloudSimple per la manutenzione e gli aggiornamenti pianificati
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6eeecbecc89995c25e687cc6808ed3b0c5dc5c
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816211"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Manutenzione e aggiornamenti di CloudSimple

L'ambiente cloud privato è progettato per non avere un singolo punto di errore:

* I cluster ESXi sono configurati con la disponibilità elevata di vSphere. I cluster vengono ridimensionati in modo da avere almeno un nodo di riserva per la resilienza.
* L'archiviazione primaria ridondante viene fornita da rete VSAN, che richiede almeno tre nodi per garantire la protezione da un singolo errore. Rete VSAN può essere configurato in modo da garantire una resilienza maggiore per i cluster più grandi.
* le macchine virtuali di gestione vCenter, PSC e NSX sono configurate con i criteri di archiviazione RAID-10 per proteggersi da errori di archiviazione. Le macchine virtuali sono protette dagli errori di nodo/rete di vSphere HA.
* Gli host ESXi hanno ventilatori e schede di rete ridondanti.
* Le opzioni TOR e spin sono configurate in coppie a disponibilità elevata per garantire la resilienza.

CloudSimple monitora continuamente le macchine virtuali seguenti per tempi di indisponibilità e disponibilità e offre contratti di disponibilità:

* Host ESXi
* vCenter
* PSC
* Gestione NSX

CloudSimple monitora inoltre costantemente gli errori seguenti:

* Dischi rigidi
* Porte NIC fisiche
* Server
* Fan
* Potenza
* Switch
* Porte switch

Se si verifica un errore in un disco o in un nodo, viene aggiunto automaticamente un nuovo nodo al cluster VMware interessato per ripristinare immediatamente l'integrità.

CloudSimple esegue il backup, gestisce e aggiorna questi elementi VMware nei cloud privati:

* ESXi
* Servizi della piattaforma vCenter
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Eseguire il backup e il ripristino

Il backup di CloudSimple include:

* Backup incrementali notturni delle regole vCenter, PSC e DVS.
* Uso di API vCenter native per eseguire il backup dei componenti a livello di applicazione.
* Backup automatico prima dell'aggiornamento o dell'aggiornamento del software di gestione VMware.
* Crittografia dei dati all'origine, da vCenter, prima del trasferimento dei dati su un canale crittografato TLS 1.2 in Azure. I dati vengono archiviati in un BLOB di Azure in cui vengono replicati tra le aree.

È possibile richiedere un ripristino aprendo un [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Manutenzione

CloudSimple esegue diversi tipi di manutenzione pianificata.

### <a name="backendinternal-maintenance"></a>Back-end/manutenzione interna

Questa manutenzione comporta in genere la riconfigurazione delle risorse fisiche o l'installazione di patch software. Non influisce sul consumo normale delle risorse gestite. Con le schede di rete ridondanti che passano a ogni rack fisico, il normale traffico di rete e il cloud privato non sono interessati. Si può notare un effetto sulle prestazioni solo se l'organizzazione prevede di usare la larghezza di banda ridondante completa durante l'intervallo di manutenzione.

### <a name="cloudsimple-portal-maintenance"></a>Manutenzione del portale CloudSimple

Quando viene aggiornato il piano o l'infrastruttura di controllo CloudSimple, è necessario un tempo di inattività del servizio limitato. Attualmente, gli intervalli di manutenzione possono essere più frequenti di una volta al mese. La frequenza dovrebbe ridursi nel tempo. CloudSimple fornisce la notifica per la manutenzione del portale e mantiene l'intervallo il più breve possibile. Durante un intervallo di manutenzione del portale, i servizi seguenti continuano a funzionare senza alcun effetto:

* Piano e applicazioni di gestione VMware
* accesso a vCenter
* Tutte le funzionalità di rete e di archiviazione
* Tutto il traffico di Azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenzione dell'infrastruttura VMware

Talvolta è necessario apportare modifiche alla configurazione dell'infrastruttura VMware.  Attualmente, questi intervalli possono verificarsi ogni 1-2 mesi, ma la frequenza dovrebbe ridursi nel tempo. Questo tipo di manutenzione può essere in genere eseguito senza interrompere il consumo normale dei servizi CloudSimple. Durante un intervallo di manutenzione VMware, i servizi seguenti continuano a funzionare senza alcun effetto:

* Piano e applicazioni di gestione VMware
* accesso a vCenter
* Tutte le funzionalità di rete e di archiviazione
* Tutto il traffico di Azure

## <a name="updates-and-upgrades"></a>Aggiornamenti e aggiornamenti

CloudSimple è responsabile della gestione del ciclo di vita del software VMware (ESXi, vCenter, PSC e NSX) nel cloud privato.

Gli aggiornamenti software includono:

* **Patch**. Patch di sicurezza o correzioni di bug rilasciate da VMware.
* **Aggiornamenti**. Modifica della versione secondaria di un componente dello stack VMware.
* **Aggiornamenti**. Modifica della versione principale di un componente dello stack VMware.

CloudSimple testa una patch di sicurezza critica non appena diventa disponibile da VMware. Per SLA, CloudSimple esegue il rollup della patch di sicurezza negli ambienti di cloud privato entro una settimana.

CloudSimple fornisce aggiornamenti di manutenzione trimestrale per i componenti software VMware. Quando è disponibile una nuova versione principale del software VMware, CloudSimple collabora con i clienti per coordinare una finestra di manutenzione adatta per l'aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

[Eseguire il backup delle VM del carico di lavoro usando Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).