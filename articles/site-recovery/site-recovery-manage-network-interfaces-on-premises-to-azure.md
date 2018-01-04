---
title: Gestire le interfacce di rete in Azure Site Recovery per locale per gli scenari di Azure | Documenti Microsoft
description: Viene descritto come gestire le interfacce di rete locale per gli scenari di Azure con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: manayar
ms.openlocfilehash: b60c746ae6c243d2b448056f8768df3baaed4cc1
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2017
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-scenarios"></a>Gestire le interfacce di rete di macchina virtuale per locale per gli scenari di Azure

Una macchina virtuale in Azure deve avere almeno un'interfaccia di rete associata e può avere il numero di interfacce di rete associato come il supporta dimensioni di macchina virtuale. Per impostazione predefinita, la prima interfaccia di rete associata a una macchina virtuale di Azure è definita come l'interfaccia di rete primaria. Tutte le altre interfacce di rete nella macchina virtuale sono interfacce di rete secondarie. Per impostazione predefinita, tutto il traffico in uscita dalla macchina virtuale viene inviato l'indirizzo IP assegnato alla configurazione IP primaria dell'interfaccia di rete primaria.

In un ambiente locale, le macchine virtuali/server possono avere più interfacce di rete per reti diverse all'interno dell'ambiente. Reti diverse vengono in genere utilizzate per l'esecuzione di operazioni specifiche, ad esempio gli aggiornamenti, manutenzione, l'accesso a internet, e così via. Quando la migrazione o il failover in Azure da un ambiente locale, tenere presente che le interfacce di rete nella macchina virtuale stessa devono tutti essere connessi alla stessa rete virtuale.

Per impostazione predefinita, il ripristino del sito crea molte interfacce di rete in una macchina virtuale di Azure come sono connessi al server locale. È possibile evitare la creazione di interfacce di rete ridondanti durante la migrazione o failover modificando le impostazioni di interfaccia di rete con impostazioni per la macchina virtuale replicata.

## <a name="select-the-target-network"></a>Selezionare la rete di destinazione

Per VMware & computer fisici e per le macchine virtuali Hyper-V (senza VMM), è possibile specificare la rete virtuale di destinazione per singole macchine virtuali. Per le macchine virtuali Hyper-V gestite con VMM, [mapping di rete](site-recovery-network-mapping.md) viene utilizzato per eseguire il mapping di reti VM in un server VMM di origine e reti Azure di destinazione.

1. In 'Elementi replicato' in un insieme di credenziali di servizi di ripristino, fare clic su degli elementi replicato per accedere alle impostazioni per quell'elemento replicato.

2. Fare clic sulla scheda "Calcolo e rete" per accedere alle impostazioni di rete per l'articolo replicato.

3. In 'Proprietà di rete', scegliere una rete virtuale dall'elenco di interfacce di rete disponibili.

    ![Calcolo e rete](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

La modifica di rete di destinazione, influisce su tutte le interfacce di rete per tale macchina virtuale specifica.

Per i cloud VMM, la modifica di mapping di rete influisce su tutte le macchine virtuali e le relative interfacce di rete.

## <a name="select-the-target-interface-type"></a>Selezionare il tipo di interfaccia di destinazione

Nella sezione 'Interfacce di rete' del pannello "Calcolo e rete", è possibile visualizzare e modificare le impostazioni dell'interfaccia di rete e specificare il tipo di interfaccia di rete di destinazione.

- Oggetto **primario** interfaccia di rete è necessaria per il failover.
- Interfacce di tutti gli altri rete selezionata, se presente, vengono **secondario** interfacce di rete.
- Selezionare **non utilizzano** per escludere un'interfaccia di rete dalla creazione in caso di failover.

Per impostazione predefinita, quando si seleziona l'abilitazione della replica, il ripristino del sito tutti rilevati interfacce di rete nel server locale, uno come 'Primary' e tutti gli altri contrassegnato come 'Secondario' interfacce di rete. Tutte le interfacce successive aggiunte nel server locale sono contrassegnate 'Non utilizzare' per impostazione predefinita. Quando si aggiungono più interfacce di rete, verificare che le dimensioni di destinazione corretto macchina virtuale di Azure vengano selezionata per contenere tutte le interfacce di rete necessarie.

## <a name="modifying-network-interface-settings"></a>Modifica delle impostazioni di interfaccia di rete

È possibile modificare le subnet IP per le interfacce di rete dell'articolo replicato. Se non viene specificato un indirizzo IP, il ripristino del sito assegnerà l'indirizzo IP disponibile successiva da una subnet all'interfaccia di rete in caso di failover.

1. Fare clic su qualsiasi interfaccia di rete disponibile per aprire il pannello Impostazioni interfaccia di rete.

2. Scegliere la subnet desiderata dall'elenco di subnet disponibili.

3. Immettere l'indirizzo IP desiderato (se necessario).

    ![Impostazioni dell'interfaccia di rete](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Fare clic su OK per terminare la modifica e tornare al pannello delle "Calcolo e rete".

5. Ripetere i passaggi da 1 a 4 per le altre interfacce di rete.

6. Fare clic su 'Salva per salvare tutte le modifiche.

## <a name="next-steps"></a>Passaggi successivi
  [Altre informazioni](../virtual-network/virtual-network-network-interface-vm.md) sulle interfacce di rete per macchine virtuali di Azure.
