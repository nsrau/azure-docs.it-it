---
title: Gestire le interfacce di rete in Azure Site Recovery per la replica dall'ambiente locale ad Azure | Microsoft Docs
description: Descrive come gestire le interfacce di rete per la replica dall'ambiente locale ad Azure con Azure Site Recovery.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: a0d42608dc689e5f084f4ec91858531feeac8033
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-replication"></a>Gestire le interfacce di rete delle macchine virtuali per la replica dall'ambiente locale ad Azure

Una macchina virtuale (VM) in Azure deve avere almeno un'interfaccia di rete collegata, fino al numero massimo supportato dalle dimensioni della macchina virtuale stessa.

Per impostazione predefinita, la prima interfaccia di rete collegata a una macchina virtuale di Azure è definita interfaccia di rete primaria. Tutte le altre interfacce di rete nella macchina virtuale sono interfacce di rete secondarie. Sempre per impostazione predefinita, tutto il traffico in uscita dalla macchina virtuale viene inviato all'indirizzo IP assegnato alla configurazione IP primaria dell'interfaccia di rete primaria.

In un ambiente locale le macchine virtuali o i server possono avere più interfacce di rete per reti diverse all'interno dell'ambiente. In genere vengono usate reti diverse per l'esecuzione di operazioni specifiche, ad esempio aggiornamenti, manutenzione e accesso a Internet. Quando si esegue la migrazione o si effettua il failover in Azure da un ambiente locale, tenere presente che le interfacce di rete nella stessa macchina virtuale devono essere tutte connesse alla stessa rete virtuale.

Per impostazione predefinita, Azure Site Recovery crea un numero di interfacce di rete in una macchina virtuale di Azure corrispondente a quello delle VM connesse al server locale. È possibile evitare la creazione di interfacce di rete ridondanti durante la migrazione o il failover modificando le impostazioni delle interfacce di rete nell'ambito delle impostazioni per la macchina virtuale replicata.

## <a name="select-the-target-network"></a>Selezionare la rete di destinazione

Per VMware e i computer fisici e per le macchine virtuali Hyper-V (senza System Center Virtual Machine Manager), è possibile specificare la rete virtuale di destinazione per le singole macchine virtuali. Per le macchine virtuali Hyper-V gestite con Virtual Machine Manager, usare il [mapping di rete](site-recovery-network-mapping.md) per associare le reti delle macchine virtuali in un server Virtual Machine Manager di origine e nelle reti Azure di destinazione.

1. In **Elementi replicati** in un insieme di credenziali di Servizi di ripristino selezionare un elemento replicato per accedere alle impostazioni per tale elemento replicato.

2. Selezionare la scheda **Calcolo e rete** per accedere alle impostazioni di rete per l'elemento replicato.

3. In **Proprietà di rete** scegliere una rete virtuale dall'elenco di interfacce di rete disponibili.

    ![Impostazioni di rete](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

La modifica della rete di destinazione influisce su tutte le interfacce di rete per quella macchina virtuale specifica.

Per i cloud Virtual Machine Manager, la modifica del mapping di rete incide su tutte le macchine virtuali e le relative interfacce di rete.

## <a name="select-the-target-interface-type"></a>Selezionare il tipo di interfaccia di destinazione

Nella sezione **Interfacce di rete** del riquadro **Calcolo e rete** è possibile visualizzare e modificare le impostazioni dell'interfaccia di rete. È anche possibile specificare il tipo di interfaccia di rete di destinazione.

- Un'interfaccia di rete **Primaria** è necessaria per il failover.
- Tutte le altre eventuali interfacce di rete selezionate sono interfacce di rete **secondarie**.
- Selezionare **Non utilizzare** per escludere un'interfaccia di rete dalla creazione in caso di failover.

Per impostazione predefinita, quando si abilita la replica, Site Recovery seleziona tutte le interfacce di rete rilevate nel server locale, contrassegnandone una come **Primaria** e tutte le altre come **secondarie**. Tutte le interfacce successive aggiunte nel server locale sono contrassegnate da **Non utilizzare** per impostazione predefinita. Quando si aggiungono altre interfacce di rete, verificare che siano selezionate le dimensioni corrette di destinazione della macchina virtuale di Azure in modo da contenere tutte le interfacce di rete richieste.

## <a name="modify-network-interface-settings"></a>Modificare le impostazioni dell'interfaccia di rete

È possibile modificare la subnet e l'indirizzo IP per le interfacce di rete dell'elemento replicato. Se non è specificato un indirizzo IP, Site Recovery assegnerà l'indirizzo IP successivo disponibile dalla subnet all'interfaccia di rete in caso di failover.

1. Selezionare un'interfaccia di rete disponibile per aprire le impostazioni dell'interfaccia di rete.

2. Scegliere la subnet desiderata dall'elenco di subnet disponibili.

3. Immettere l'indirizzo IP desiderato (come richiesto).

    ![Impostazioni dell'interfaccia di rete](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Selezionare **OK** per completare la modifica e tornare al riquadro **Calcolo e rete**.

5. Ripetere i passaggi da 1 a 4 per le altre interfacce di rete.

6. Selezionare **Salva** per salvare tutte le modifiche.

## <a name="next-steps"></a>Passaggi successivi
  [Altre informazioni](../virtual-network/virtual-network-network-interface-vm.md) sulle interfacce di rete per le macchine virtuali di Azure.
