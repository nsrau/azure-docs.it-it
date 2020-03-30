---
title: Deprecazione del ripristino di emergenza tra siti gestiti dal cliente (con VMM) tramite Azure Site Recovery - Documenti Microsoft
description: Dettagli sulla prossima deprecazione del ripristino di emergenza tra i siti di proprietà del cliente tramite Hyper-V e tra i siti gestiti da SCVMM ad Azure e le opzioni alternative
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661671"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Deprecazione del ripristino di emergenza tra siti gestiti dal cliente (con VMM) tramite Azure Site Recovery

In questo articolo viene descritto il piano di deprecazione imminente, le implicazioni corrispondenti e le opzioni alternative disponibili per i clienti per il seguente scenario:

Ripristino di emergenza tra siti di proprietà del cliente gestito da System Center Virtual Machine Manager (SCVMM) tramite Site Recovery

> [!IMPORTANT]
> Si consiglia ai clienti di adottare al più presto le misure di correzione per evitare eventuali interruzioni del proprio ambiente. 

## <a name="what-changes-should-you-expect"></a>Quali cambiamenti ci si dovrebbe aspettare?

- A partire da marzo 2020 si riceveranno le notifiche del portale di Azure & le comunicazioni di posta elettronica con la prossima deprecazione della replica da sito a sito delle macchine virtuali Hyper-V. La deprecazione è prevista per marzo 2023.

- Se si dispone di una configurazione esistente, non ci sarà alcun impatto sulla configurazione.

- Una volta che gli scenari sono deprecati a meno che il cliente non segua gli approcci alternativi, le repliche esistenti potrebbero essere interrotte. I clienti non saranno in grado di visualizzare, gestire o eseguire operazioni correlate al ripristino di emergenza tramite l'esperienza di Azure Site Recovery nel portale di Azure.Customers won't be able to view, manage, or performs performs any DR-related operations via the Azure Site Recovery experience in Azure portal.
 
## <a name="alternatives"></a>Alternativi 

Di seguito sono riportate le alternative che il cliente può scegliere per garantire che la strategia di ripristino di emergenza non venga influenzata dopo che lo scenario è deprecato. 

- Opzione 1 (scelta consigliata): scegliere di iniziare a [usare Azure come destinazione di ripristino di emergenza.](hyper-v-vmm-azure-tutorial.md)


- Opzione 2: scegliere di continuare con la replica da sito a sito utilizzando la [soluzione di replica Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)sottostante, ma non sarà possibile gestire le configurazioni di ripristino di emergenza usando Azure Site Recovery nel portale di Azure.Option 2: Choose to continue with site-to-site replication using the underlying Hyper-V Replica solution , but you will be unable to manage DR configurations using Azure Site Recovery in the Azure portal. 


## <a name="remediation-steps"></a>Passaggi di correzioneRemediation steps

Se si sceglie di andare con l'opzione 1, si prega di eseguire i seguenti passaggi:

1. [Disabilitare la protezione di tutte le macchine virtuali associate alle macchine virtuali.](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario) Utilizzare l'opzione **Disabilita replica e rimuovi** oppure eseguire gli script indicati per assicurarsi che le impostazioni di replica locali vengano pulite. 

2. [Annullare la registrazione](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) di tutti i server VMM dalla configurazione della replica da sito a sito.

3. [Preparare le risorse](tutorial-prepare-azure-for-hyperv.md) di Azure per abilitare la replica delle macchine virtuali.
4. [Preparare i server Hyper-V in locale](hyper-v-prepare-on-premises-tutorial.md)
5. [Configurare la replica per le macchine virtuali nel cloud VMM](hyper-v-vmm-azure-tutorial.md)
6. Facoltativo ma consigliato: [eseguire un'esercitazione di ripristino di emergenzaOptional](tutorial-dr-drill-azure.md) but recommended: Run a DR drill

Se si sceglie di utilizzare l'opzione 2 di utilizzo della replica Hyper-V, eseguire la procedura seguente:

1. In **Elementi** > **protetti Elementi replicati**fare clic con il pulsante destro del mouse sul computer > **Disabilita replica.**
2. In **Disabilita replica**selezionare **Rimuovi**.

    L'opzione rimuove l'elemento replicato da Azure Site Recovery, interrompendo la fatturazione. La configurazione della replica nella macchina virtuale locale **non** verrà rimossa. 

## <a name="next-steps"></a>Passaggi successivi
Pianificare la deprecazione e scegliere un'opzione alternativa più adatta all'infrastruttura e al business. Nel caso in cui si dispone di domande in merito, contattare il supporto tecnico Microsoft

