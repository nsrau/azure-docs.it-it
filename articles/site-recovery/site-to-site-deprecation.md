---
title: Deprecazione del ripristino di emergenza tra siti gestiti dal cliente (con VMM) con Azure Site Recovery | Microsoft Docs
description: Informazioni dettagliate sulla deprecazione imminente del ripristino di emergenza tra i siti di proprietà del cliente che usano Hyper-V e tra siti gestiti da SCVMM in Azure e opzioni alternative
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661671"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Deprecazione del ripristino di emergenza tra siti gestiti dal cliente (con VMM) con Azure Site Recovery

Questo articolo descrive il piano di deprecazione imminente, le implicazioni corrispondenti e le opzioni alternative disponibili per i clienti per lo scenario seguente:

RIPRISTINO di emergenza tra siti di proprietà del cliente gestiti da System Center Virtual Machine Manager (SCVMM) utilizzando Site Recovery

> [!IMPORTANT]
> Si consiglia ai clienti di eseguire la procedura di correzione al più presto per evitare eventuali problemi nell'ambiente. 

## <a name="what-changes-should-you-expect"></a>Quali modifiche si aspettano?

- A partire da marzo 2020, si riceveranno notifiche portale di Azure & comunicazioni tramite posta elettronica con la prossima deprecazione della replica da sito a sito delle VM Hyper-V. La deprecazione è prevista per il 2023 marzo.

- Se si dispone di una configurazione esistente, l'installazione non avrà alcun effetto.

- Una volta che gli scenari sono stati deprecati, a meno che il cliente non segua gli approcci alternativi, è possibile che le repliche esistenti vengano interrotte. I clienti non saranno in grado di visualizzare, gestire o eseguire operazioni relative al ripristino di emergenza tramite l'esperienza Azure Site Recovery in portale di Azure.
 
## <a name="alternatives"></a>Alternativi 

Di seguito sono riportate le alternative tra cui il cliente può scegliere per assicurarsi che la strategia di ripristino di emergenza non abbia effetto quando lo scenario è deprecato. 

- Opzione 1 (scelta consigliata): scegliere di [iniziare a usare Azure come destinazione del ripristino](hyper-v-vmm-azure-tutorial.md)di emergenza.


- Opzione 2: scegliere di continuare con la replica da sito a sito usando la [soluzione di replica Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)sottostante, ma non sarà possibile gestire le configurazioni di ripristino di emergenza usando Azure Site Recovery nel portale di Azure. 


## <a name="remediation-steps"></a>Procedura di correzione

Se si sceglie di usare l'opzione 1, seguire questa procedura:

1. [Disabilitare la protezione di tutte le macchine virtuali associate a VMMs](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Usare l'opzione **Disabilita replica e Rimuovi** oppure eseguire gli script indicati per assicurarsi che le impostazioni di replica locale vengano pulite. 

2. [Annullare la registrazione di tutti i server VMM](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) dalla configurazione della replica da sito a sito.

3. [Preparare le risorse di Azure](tutorial-prepare-azure-for-hyperv.md) per abilitare la replica delle macchine virtuali.
4. [Preparare i server Hyper-V locali](hyper-v-prepare-on-premises-tutorial.md)
5. [Configurare la replica per le macchine virtuali nel cloud VMM](hyper-v-vmm-azure-tutorial.md)
6. Facoltativo ma consigliato: [eseguire un'esercitazione sul ripristino di](tutorial-dr-drill-azure.md) emergenza

Se si sceglie di usare l'opzione 2 dell'uso della replica Hyper-V, eseguire la procedura seguente:

1. In **Elementi protetti** > **Elementi replicati** fare clic con il pulsante destro del mouse sul computer e quindi scegliere **Disabilita replica**.
2. In **Disabilita replica**selezionare **Rimuovi**.

    L'opzione rimuove l'elemento replicato da Azure Site Recovery, interrompendo la fatturazione. La configurazione della replica nella macchina virtuale locale **non** verrà rimossa. 

## <a name="next-steps"></a>Passaggi successivi
Pianificare la deprecazione e scegliere un'opzione alternativa più adatta per l'infrastruttura e l'azienda. Nel caso in cui siano presenti query relative a questo, contattare supporto tecnico Microsoft

