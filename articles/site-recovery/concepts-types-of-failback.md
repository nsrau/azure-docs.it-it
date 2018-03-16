---
title: Failback in Azure Site Recovery | Microsoft Docs
description: In questo articolo viene fornita una panoramica dei diversi tipi di failback e avvertenze da tenere in considerazione durante l'esecuzione locale del failback con il servizio di Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: guaravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanki
ms.openlocfilehash: 372a7867b47960338d7a1bf7e646fb9fffbe72e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-failback"></a>Panoramica del failback

Dopo aver eseguito il failover in Azure, è possibile eseguire il failback nel sito locale. Esistono due diversi tipi di failback con Azure Site Recovery: 

- Failback nella posizione originaria 
- Failback in una posizione alternativa

Se è stato eseguito il failover di una macchina virtuale VMware è possibile eseguire il failback nella stessa macchina virtuale di origine locale, se è ancora presente. In questo scenario viene eseguita la replica delle sole modifiche. Questo scenario è noto come **ripristino nel percorso originale**. Se la macchina virtuale locale non esiste, lo scenario è un **ripristino del percorso alternativo**.

> [!NOTE]
> È possibile eseguire il failback solo al server vCenter e al server di configurazione originali. Non è possibile distribuire un server di configurazione ed eseguire il failback usandolo. Non è inoltre possibile aggiungere un nuovo vCenter al server di configurazione esistente ed eseguire il failback nel nuovo vCenter.

## <a name="original-location-recovery-olr"></a>Ripristino del percorso originale (OLR)
Se si sceglie di eseguire il failback nella macchina virtuale originaria è necessario rispettare le condizioni seguenti:

* Se la macchina virtuale è gestita da un server vCenter, l'host ESX della destinazione master deve avere accesso all'archivio dati della macchina virtuale.
* Se la macchina virtuale si trova in un host ESX, ma non è gestita da vCenter, il relativo disco rigido deve trovarsi in un archivio dati accessibile da parte dell'host della destinazione master.
* Se la macchina virtuale si trova in un host ESX e non usa vCenter, è necessario completare l'individuazione dell'host ESX della destinazione master prima di abilitare la riprotezione. Questa opzione è valida anche per il failback di server fisici.
* È possibile eseguire il failback in una rete virtuale (vSAN) o in un disco basato su Raw Device Mapping (RDM), se i dischi esistono già e sono connessi alla macchina virtuale locale.

> [!IMPORTANT]
> È importante abilitare disk.enableUUID= TRUE in modo che, durante il failback, il servizio Azure Site Recovery sia in grado di identificare il disco VMDK originale nella macchina virtuale in cui scrivere le modifiche in sospeso. Se questo valore non è impostato su TRUE, il servizio tenta di identificare il disco VMDK locale corrispondente in base ad approssimazioni ottimali. Se il disco VMDK giusto non viene identificato, il servizio crea un disco extra sul quale vengono scritti i dati.

## <a name="alternate-location-recovery-alr"></a>Ripristino del percorso alternativo (ALR)
Se la macchina virtuale locale non esiste prima della riprotezione della macchina virtuale, lo scenario è denominato ripristino del percorso alternativo. Il flusso di lavoro della riprotezione crea nuovamente la macchina virtuale locale. Verrà anche eseguito un download completo dei dati.

* Quando si esegue il failback in una posizione alternativa, la macchina virtuale viene ripristinata nello stesso host ESX in cui è stato distribuito il server di destinazione master. L'archivio dati usato per creare il disco sarà lo stesso archivio selezionato durante la riprotezione della macchina virtuale.
* È possibile eseguire il failback solo in un file system di macchina virtuale (VMFS) oppure vSAN. Con RDM, la riprotezione e il failback avranno esito negativo.
* La riprotezione comporta un trasferimento di dati iniziale di grandi dimensioni, a cui fanno seguito le modifiche. Questo processo avviene perché la macchina virtuale non esiste in locale. È necessario eseguire di nuovo la replica dei dati completi. La riprotezione impiegherà quindi più tempo rispetto al ripristino nel percorso originale.
* Non è possibile eseguire il failback in dischi basati su RDM. In un archivio dati VMFS/vSAN è possibile creare solo nuovi dischi VMDK.

> [!NOTE]
> In seguito al failover in Azure, è possibile eseguire il failback di una macchina fisica solo come macchina virtuale VMware. Si tratta dello stesso flusso di lavoro del ripristino nel percorso alternativo. È necessario individuare almeno un server di destinazione master e gli host ESX/ESXi necessari per il failback.

## <a name="next-steps"></a>Passaggi successivi

Attenersi ai passaggi per eseguire l'[operazione di failback](vmware-azure-failback.md).

