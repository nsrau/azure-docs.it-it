---
title: 'Soluzioni VMware di Azure (AVS): manutenzione e aggiornamenti AVS'
description: Descrive il processo del servizio AVS per la manutenzione e gli aggiornamenti pianificati
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf5937183fc20579ecd21aca8543a0a78d4b9ff3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025028"
---
# <a name="avs-maintenance-and-updates"></a>Manutenzione e aggiornamenti AVS

L'ambiente di cloud privato AVS è progettato per non avere un singolo punto di errore.

* I cluster ESXi sono configurati con la disponibilità elevata di vSphere (HA). I cluster vengono ridimensionati in modo da avere almeno un nodo di riserva per la resilienza.
* L'archiviazione primaria ridondante viene fornita da rete VSAN, che richiede almeno tre nodi per garantire la protezione da un singolo errore. Rete VSAN può essere configurato in modo da garantire una resilienza maggiore per i cluster più grandi.
* le macchine virtuali di gestione vCenter, PSC e NSX sono configurate con l'archiviazione RAID-10 per la protezione da errori di archiviazione. Le macchine virtuali sono protette dagli errori di nodo/rete di vSphere HA.
* Gli host ESXi hanno ventilatori e schede di rete ridondanti.
* Le opzioni TOR e spin sono configurate in coppie a disponibilità elevata per garantire la resilienza.

AVS monitora continuamente le macchine virtuali seguenti per tempi di indisponibilità e disponibilità e offre contratti di disponibilità:

* Host ESXi
* vCenter
* PSC
* Gestione NSX

AVS monitora anche le seguenti operazioni in modo continuo per gli errori:

* Dischi rigidi
* Porte NIC fisiche
* Server
* Fan
* Power
* Switch
* Porte switch

Se si verifica un errore in un disco o in un nodo, viene aggiunto automaticamente un nuovo nodo al cluster VMware interessato per ripristinare immediatamente l'integrità.

AVS esegue il backup, gestisce e aggiorna questi elementi VMware nei cloud privati AVS:

* ESXi
* Servizi della piattaforma vCenter
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Backup e ripristino

Il backup AVS include:

* Backup incrementali notturni delle regole vCenter, PSC e DVS.
* API vCenter native per eseguire il backup dei componenti a livello di applicazione.
* Backup automatico prima dell'aggiornamento o dell'aggiornamento del software di gestione VMware.
* crittografia dei dati vCenter nell'origine prima del trasferimento dei dati su un canale crittografato TLS 1.2 in Azure. I dati vengono archiviati in un BLOB di Azure in cui vengono replicati tra le aree.

È possibile richiedere un ripristino aprendo un [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Manutenzione

AVS esegue diversi tipi di manutenzione pianificata.

### <a name="backendinternal-maintenance"></a>Back-end/manutenzione interna

Questa manutenzione comporta in genere la riconfigurazione delle risorse fisiche o l'installazione di patch software. Non influisce sul consumo normale delle risorse gestite. Con le schede di rete ridondanti che passano a ogni rack fisico, il normale traffico di rete e le operazioni del cloud privato AVS non sono interessate. Si può notare un effetto sulle prestazioni solo se l'organizzazione prevede di usare la larghezza di banda ridondante completa durante l'intervallo di manutenzione.

### <a name="avs-portal-maintenance"></a>Manutenzione del portale AVS

È necessario un tempo di inattività del servizio limitato quando viene aggiornato l'infrastruttura o il piano di controllo AVS. Attualmente, gli intervalli di manutenzione possono essere più frequenti di una volta al mese. La frequenza dovrebbe ridursi nel tempo. AVS fornisce notifiche per la manutenzione del portale e mantiene l'intervallo il più breve possibile. Durante un intervallo di manutenzione del portale, i servizi seguenti continuano a funzionare senza alcun effetto:

* Piano e applicazioni di gestione VMware
* accesso a vCenter
* Tutte le funzionalità di rete e di archiviazione
* Tutto il traffico di Azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenzione dell'infrastruttura VMware

Talvolta è necessario apportare modifiche alla configurazione dell'infrastruttura VMware. Attualmente, questi intervalli possono verificarsi ogni 1-2 mesi, ma la frequenza dovrebbe ridursi nel tempo. Questo tipo di manutenzione può essere in genere eseguito senza interrompere il consumo normale dei servizi AVS. Durante un intervallo di manutenzione VMware, i servizi seguenti continuano a funzionare senza alcun effetto:

* Piano e applicazioni di gestione VMware
* accesso a vCenter
* Tutte le funzionalità di rete e di archiviazione
* Tutto il traffico di Azure

## <a name="updates-and-upgrades"></a>Aggiornamenti e aggiornamenti

AVS è responsabile della gestione del ciclo di vita del software VMware (ESXi, vCenter, PSC e NSX) nel cloud privato AVS.

Gli aggiornamenti software includono:

* **Patch**. Patch di sicurezza o correzioni di bug rilasciate da VMware.
* **Aggiornamenti**. Modifica della versione secondaria di un componente dello stack VMware.
* **Aggiornamenti**. Modifica della versione principale di un componente dello stack VMware.

AVS testa una patch di sicurezza critica non appena diventa disponibile da VMware. Per SLA, AVS esegue il rollup della patch di sicurezza per gli ambienti di cloud privato AVS entro una settimana.

AVS fornisce aggiornamenti di manutenzione trimestrale per i componenti software VMware. Quando è disponibile una nuova versione principale del software VMware, AVS collabora con i clienti per coordinare una finestra di manutenzione adatta per l'aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

[Eseguire il backup di macchine virtuali del carico di lavoro con Veeam](backup-workloads-veeam.md)
