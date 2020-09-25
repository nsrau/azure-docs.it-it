---
title: Concetti-aggiornamenti e aggiornamenti del cloud privato
description: Informazioni sui processi e le funzionalità di aggiornamento principali della soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 380e97eae559145a9ef5ed7b6e7bf14f18039eed
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316801"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Aggiornamenti e aggiornamenti del cloud privato della soluzione VMware di Azure

## <a name="overview"></a>Panoramica

Uno dei principali vantaggi dei cloud privati della soluzione VMware di Azure è che la piattaforma viene mantenuta per l'utente. La manutenzione della piattaforma include aggiornamenti automatici a un bundle software VMware convalidato, che consente di assicurarsi di usare la versione più recente del software di cloud privato della soluzione VMware di Azure convalidata.

In particolare, un cloud privato della soluzione VMware di Azure include:

- Nodi server bare metal dedicati sottoposti a provisioning con VMware ESXi hypervisor 
- server vCenter per la gestione di ESXi e rete VSAN 
- VMware NSX-T Software Defined Networking per macchine virtuali con carico di lavoro vSphere  
- Archivio dati VMware rete VSAN per macchine virtuali con carico di lavoro vSphere  
- HCX VMware per la mobilità del carico di lavoro  

Oltre a questi componenti, una cloud privata della soluzione VMware di Azure include risorse nel sottoinsieme di Azure necessario per la connettività e per il funzionamento del cloud privato. La soluzione VMware di Azure monitora costantemente l'integrità dei componenti sottoposto e VMware. Quando la soluzione VMware di Azure rileva un errore, viene eseguita un'azione per ripristinare i componenti non riusciti. 

## <a name="what-components-get-updated"></a>Quali componenti vengono aggiornati?   

La soluzione VMware di Azure aggiorna i componenti VMware seguenti: 

- server vCenter e ESXi in esecuzione nei nodi del server bare metal 
- Rete VSAN 
- NSX-T 

La soluzione VMware di Azure aggiorna anche il software nel sottosistema, ad esempio i driver, il software sui commutatori di rete e il firmware nei nodi bare metal. 

## <a name="types-of-updates"></a>Tipi di aggiornamenti

La soluzione VMware di Azure applica i tipi di aggiornamenti seguenti ai componenti VMware:

- Patch: patch di sicurezza e correzioni di bug rilasciate da VMware. 
- Aggiornamenti: aggiornamenti della versione secondaria di uno o più componenti VMware. 
- Aggiornamenti: aggiornamenti della versione principale di uno o più componenti VMware.

Si riceverà una notifica prima e dopo l'applicazione delle patch ai cloud privati. Si collaborerà anche con l'utente per pianificare una finestra di manutenzione prima di applicare aggiornamenti o aggiornamenti al cloud privato. 

## <a name="vmware-appliance-backup"></a>Backup di appliance VMware 

Oltre a eseguire aggiornamenti, la soluzione VMware di Azure esegue un backup della configurazione di questi componenti VMware:

- Server vCenter 
- Gestione NSX-T 

In momenti di errore, la soluzione VMware di Azure può ripristinarli dal backup della configurazione. 

Per ulteriori informazioni sulle versioni del software VMware, vedere l'articolo relativo al [concetto di cloud e cluster privati](concepts-private-clouds-clusters.md) e le [domande frequenti](faq.md).

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consiste nel [creare un cloud privato](tutorial-create-private-cloud.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
