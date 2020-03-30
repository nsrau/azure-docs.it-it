---
title: Manutenzione e aggiornamenti CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Descrive il processo del servizio CloudSimple per la manutenzione pianificata e gli aggiornamenti
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025028"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Manutenzione e aggiornamenti CloudSimple

L'ambiente Private Cloud è progettato per non avere un singolo punto di errore.

* I cluster ESXi sono configurati con vSphere High Availability (HA). Le dimensioni dei cluster hanno almeno un nodo di riserva per la resilienza.
* L'archiviazione primaria ridondante viene fornita da vSAN, che richiede almeno tre nodi per fornire protezione da un singolo errore. vSAN può essere configurato per fornire una maggiore resilienza per i cluster più grandi.
* Le macchine virtuali di vCenter, PSC e NSX Manager sono configurate con l'archiviazione RAID-10 per la protezione da errori di archiviazione. Le macchine virtuali sono protette da errori di nodo/rete da vSphere HA.
* Gli host ESXi hanno ventole ridondanti e schede di interfaccia di rete.
* Gli interruttori TOR e spine sono configurati in coppie HA per fornire resilienza.

CloudSimple continuously monitors the following VMs for uptime and availability, and provides availability SLAs:

* Host ESXi
* vCenter
* Psc
* Responsabile NSX

CloudSimple monitora inoltre continuamente quanto segue per gli errori:

* Dischi rigidi
* Porte NIC fisiche
* Server
* Fan
* Power
* Switch
* Passare da una porta all'altra

Se si verifica un errore in un disco o in un nodo, viene aggiunto automaticamente un nuovo nodo al cluster VMware interessato per riportarlo immediatamente all'integrità.

CloudSimple esegue il backup, la manutenzione e l'aggiornamento di questi elementi VMware nei cloud privati:

* Esxi
* Servizi della piattaforma vCenter
* Controller
* VSAN (informazioni in stato ine
* Nsx

## <a name="back-up-and-restore"></a>Backup e ripristino

Il backup CloudSimple include:

* Backup incrementali notturni delle regole vCenter, PSC e DVS.
* API native di vCenter per eseguire il backup dei componenti a livello di applicazione.
* Backup automatico prima dell'aggiornamento o dell'aggiornamento del software di gestione VMware.
* Crittografia dei dati di vCenter nell'origine prima che i dati vengano trasferiti su un canale crittografato TLS1.2 in Azure.VCenter data encryption at the source before data is transferred over a TLS1.2 encrypted channel to Azure. I dati vengono archiviati in un BLOB di Azure in cui vengono replicati tra le aree.

È possibile richiedere un ripristino aprendo una richiesta di [supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Manutenzione

CloudSimple esegue diversi tipi di manutenzione pianificata.

### <a name="backendinternal-maintenance"></a>Backend/manutenzione interna

Questa manutenzione comporta in genere la riconfigurazione delle risorse fisiche o l'installazione di patch software. Non influisce sul consumo normale delle risorse servite. Con schede di interfaccia di rete ridondanti che si recano a ogni rack fisico, il normale traffico di rete e le operazioni del cloud privato non sono interessate. È possibile notare un impatto sulle prestazioni solo se l'organizzazione prevede di utilizzare l'intera larghezza di banda ridondante durante l'intervallo di manutenzione.

### <a name="cloudsimple-portal-maintenance"></a>Manutenzione del portale CloudSimple

Quando il piano o l'infrastruttura di controllo CloudSimple viene aggiornato, è necessario un tempo di inattività del servizio limitato. Attualmente, gli intervalli di manutenzione possono essere frequenti come una volta al mese. Si prevede che la frequenza diminuirà nel tempo. CloudSimple fornisce una notifica per la manutenzione del portale e mantiene l'intervallo il più breve possibile. Durante un intervallo di manutenzione del portale, i servizi seguenti continuano a funzionare senza alcun impatto:

* Piano di gestione VMware e applicazioni
* Accesso al vCenter
* Tutte le reti e lo storage
* Tutto il traffico di AzureAll Azure traffic

### <a name="vmware-infrastructure-maintenance"></a>Manutenzione dell'infrastruttura VMware

Occasionalmente è necessario apportare modifiche alla configurazione dell'infrastruttura VMware.  Attualmente, questi intervalli possono verificarsi ogni 1-2 mesi, ma si prevede che la frequenza diminuisca nel tempo. Questo tipo di manutenzione di solito può essere fatto senza interrompere il normale consumo dei servizi CloudSimple. Durante un intervallo di manutenzione di VMware, i seguenti servizi continuano a funzionare senza alcun impatto:

* Piano di gestione VMware e applicazioni
* Accesso al vCenter
* Tutte le reti e lo storage
* Tutto il traffico di AzureAll Azure traffic

## <a name="updates-and-upgrades"></a>Aggiornamenti e aggiornamenti

CloudSimple è responsabile della gestione del ciclo di vita del software VMware (ESXi, vCenter, PSC e NSX) nel cloud privato.

Gli aggiornamenti software includono:

* **Patches**. Patch di sicurezza o correzioni di bug rilasciate da VMware.
* **Aggiornamenti**. Modifica della versione secondaria di un componente dello stack VMware.
* **Aggiornamenti**. Modifica principale della versione di un componente dello stack VMware.

CloudSimple testa una patch di sicurezza critica non appena diventa disponibile da VMware. Per sLA, CloudSimple implementa la patch di sicurezza in ambienti Cloud privati entro una settimana.

CloudSimple fornisce aggiornamenti di manutenzione trimestrali ai componenti software VMware. Quando è disponibile una nuova versione principale del software VMware, CloudSimple collabora con i clienti per coordinare una finestra di manutenzione adatta per l'aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

[Eseguire il backup delle macchine virtuali del carico di lavoro con VeeamBack up workload VMs using Veeam](backup-workloads-veeam.md)
