---
title: Modificare le impostazioni di destinazione quando si spostano le VM di Azure tra le aree con Azure Resource Mover
description: Informazioni su come modificare le impostazioni di destinazione quando si spostano le VM di Azure tra le aree con Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: ca22def6bc152d03c3992ed7e94ac2b5ccf179e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604479"
---
# <a name="modify-target-settings"></a>Modificare le impostazioni di destinazione

Questo articolo descrive come modificare le impostazioni di destinazione quando si spostano le risorse tra aree di Azure con il [motore di risorse di Azure](overview.md).


## <a name="modify-vm-settings"></a>Modificare le impostazioni della macchina virtuale

Quando si trasferiscono le macchine virtuali di Azure e le risorse associate, è possibile modificare le impostazioni di destinazione. 

- Si consiglia di modificare le impostazioni di destinazione solo dopo che la raccolta di spostamento è stata convalidata.
- Si consiglia di modificare le impostazioni prima di preparare le risorse perché alcune proprietà di destinazione potrebbero non essere disponibili per la modifica al termine della preparazione.

Tuttavia:
- Se si sta spostando la risorsa di origine, in genere è possibile modificare le impostazioni di destinazione fino a quando non si avvia il processo di avvio dello spostamento.
- Se si assegna una risorsa esistente nell'area di origine, è possibile modificare le impostazioni di destinazione fino al completamento del commit dello spostamento.

### <a name="settings-you-can-modify"></a>Impostazioni che è possibile modificare

Le impostazioni di configurazione che è possibile modificare sono riepilogate nella tabella.

**Risorsa** | **Opzioni** 
--- | --- | --- 
**Nome macchina virtuale** | Opzioni:<br/><br/> -Creare una nuova VM con lo stesso nome nell'area di destinazione.<br/><br/> -Creare una nuova macchina virtuale con un nome diverso nell'area di destinazione.<br/><br/> -Usare una macchina virtuale esistente nell'area di destinazione.<br/><br/> Se si crea una nuova macchina virtuale, ad eccezione delle impostazioni modificate, alla nuova VM di destinazione vengono assegnate le stesse impostazioni dell'origine.
**Zona di disponibilità della macchina virtuale** | Zona di disponibilità in cui verrà inserita la macchina virtuale di destinazione. Questa operazione può essere contrassegnata come **na** se non si vuole modificare le impostazioni di origine o se non si vuole collocare la macchina virtuale in una zona di disponibilità.
**SKU di VM** | Il [tipo di macchina virtuale](https://azure.microsoft.com/pricing/details/virtual-machines/series/) , disponibile nell'area di destinazione, che verrà usato per la macchina virtuale di destinazione.<br/><br/> La macchina virtuale di destinazione selezionata non deve essere inferiore alla VM di origine.
**Risorse di rete** | Opzioni per le reti virtuali (reti virtuali)/Network gruppi di sicurezza/interfacce di rete:<br/><br/> -Creare una nuova risorsa con lo stesso nome nell'area di destinazione.<br/><br/> -Creare una nuova risorsa con un nome diverso nell'area di destinazione.<br/><br/> -Usare una risorsa di rete esistente nell'area di destinazione.<br/><br/> Se si crea una nuova risorsa di destinazione, ad eccezione delle impostazioni modificate, vengono assegnate le stesse impostazioni della risorsa di origine.
**Nome indirizzo IP pubblico** | Specificare il nome.
**SKU indirizzo IP pubblico** | Specificare lo [SKU](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku).
**Area indirizzi IP pubblici** | Specificare la [zona](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#standard) per gli indirizzi IP pubblici standard.<br/><br/> Se si vuole che la zona sia ridondante, immettere come **ridondanza**della zona.
**Nome del servizio di bilanciamento del carico** | Specificare il nome.
**SKU del servizio di bilanciamento del carico** | Basic o Standard. Si consiglia di usare standard.
**Area del servizio di bilanciamento del carico** | Specificare una zona per il servizio di bilanciamento del carico. <br/><br/> Se si vuole che la zona sia ridondante, immettere come **ridondanza**della zona.
**Dipendenze delle risorse** | Opzioni per ogni dipendenza:<br/><br/>-La risorsa usa risorse dipendenti dall'origine che si sposteranno nell'area di destinazione.<br/><br/> -La risorsa usa risorse dipendenti diverse che si trovano nell'area di destinazione. In questo caso, è possibile scegliere tra le risorse simili nell'area di destinazione.

### <a name="edit-vm-target-settings"></a>Modificare le impostazioni di destinazione della macchina virtuale

Se non si desiderano risorse dipendenti dall'area di origine alla destinazione, sono disponibili due opzioni:

- Creare una nuova risorsa nell'area di destinazione. A meno che non si specifichino impostazioni diverse, la nuova risorsa avrà le stesse impostazioni della risorsa di origine.
- Usare una risorsa esistente nell'area di destinazione.

Il comportamento esatto dipende dal tipo di risorsa. [Altre](modify-target-settings.md) informazioni sulla modifica delle impostazioni di destinazione.

Si modificano le impostazioni di destinazione per una risorsa usando la voce di **configurazione di destinazione** nella raccolta di spostamento delle risorse. 

Per modificare un'impostazione: 

1. Nella pagina **tra aree** > colonna **configurazione di destinazione** fare clic sul collegamento per la voce risorsa.
2. In **impostazioni di configurazione**è possibile creare una nuova macchina virtuale nell'area di destinazione.
3. Assegnare una nuova zona di disponibilità, un set di disponibilità o uno SKU alla macchina virtuale di destinazione. **Zona di disponibilità** e **SKU**.

Le modifiche vengono apportate solo per la risorsa che si sta modificando. È necessario aggiornare le risorse dipendenti separatamente.


## <a name="modify-sql-settings"></a>Modificare le impostazioni SQL

Quando si spostano le risorse del database SQL di Azure, è possibile modificare le impostazioni di destinazione per lo spostamento. 

- Per il database SQL:
    - Si consiglia di modificare le impostazioni di configurazione di destinazione prima di prepararle per lo spostamento.
    - È possibile modificare le impostazioni per il database di destinazione e la ridondanza della zona per il database.
- Per i pool elastici:
    -  È possibile modificare la configurazione di destinazione in qualsiasi momento prima di avviare lo spostamento.
    - È possibile modificare il pool elastico di destinazione e la ridondanza della zona per il pool. 

### <a name="sql-settings-you-can-modify"></a>Impostazioni SQL che è possibile modificare

**Impostazione** | **Database SQL** | **Pool elastico**
--- | --- | ---
**Nome** | Creare un nuovo database con lo stesso nome nell'area di destinazione.<br/><br/> Creare un nuovo database con un nome diverso nell'area di destinazione.<br/><br/> Usare un database esistente nell'area di destinazione. | Creare un nuovo pool elastico con lo stesso nome nell'area di destinazione.<br/><br/> Creare un nuovo pool elastico con un nome diverso nell'area di destinazione.<br/><br/> Usare un pool elastico esistente nell'area di destinazione.
**Ridondanza della zona** | Per spostarsi da un'area che supporta la ridondanza della zona in un'area che non lo è, digitare **Disable** nell'impostazione della zona.<br/><br/> Per spostarsi da un'area che non supporta la ridondanza della zona in un'area che lo fa, digitare **Enable** nell'impostazione zone. | Per spostarsi da un'area che supporta la ridondanza della zona in un'area che non lo è, digitare **Disable** nell'impostazione della zona.<br/><br/> Per spostarsi da un'area che non supporta la ridondanza della zona in un'area che lo fa, digitare **Enable** nell'impostazione zone.

### <a name="edit-sql-target-settings"></a>Modificare le impostazioni di destinazione SQL

Modificare le impostazioni di destinazione per una risorsa del database SQL di Azure come indicato di seguito: 

1. In **tra le aree**, per la risorsa che si vuole modificare, fare clic sulla voce di **configurazione di destinazione** .
2. In **impostazioni di configurazione**specificare le impostazioni di destinazione riepilogate nella tabella precedente.

## <a name="next-steps"></a>Passaggi successivi

[Spostare una macchina virtuale di Azure](tutorial-move-region-virtual-machines.md) in un'altra area.
