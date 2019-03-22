---
title: Tipi di quote in Azure Stack | Microsoft Docs
description: Visualizzare e modificare i tipi di quote diversi disponibili per i servizi e risorse in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: aff9dade7fe0238c0ea8ccc3ae5bba57437c6f89
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339552"
---
# <a name="quota-types-in-azure-stack"></a>Tipi di quote in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

[Le quote](azure-stack-plan-offer-quota-overview.md#plans) definire i limiti delle risorse che può effettuare il provisioning o utilizzare una sottoscrizione utente. Ad esempio, una quota potrebbe consentire all'utente di creare fino a cinque macchine virtuali. Ogni risorsa può avere un proprio tipi di quote.

## <a name="compute-quota-types"></a>Tipi di quota di calcolo

| **Tipo** | **Valore predefinito** | **Descrizione** |
| --- | --- | --- |
| Numero massimo di macchine virtuali | 50 | Il numero massimo di macchine virtuali che è possibile creare una sottoscrizione in questa posizione. |
| Numero massimo di memorie centrali delle macchine virtuali | 100 | Il numero massimo di core che è possibile creare una sottoscrizione in questa posizione (ad esempio, una macchina virtuale A3 dispone di quattro core). |
| Numero massimo di set di disponibilità | 10 | Il numero massimo di set di disponibilità che possono essere create in questa posizione. |
| Imposta il numero massimo di scalabilità di macchine virtuali | 100 | Il numero massimo di set di scalabilità di macchine virtuali che possono essere create in questa posizione. |
| Capacità massima (in GB) del disco gestito standard | 2048 | La capacità massima di managed disks standard che possono essere create in questa posizione. |
| Capacità massima (in GB) del disco gestito premium | 2048 | La capacità massima di premium servizio managed disks che possono essere create in questa posizione. |

## <a name="storage-quota-types"></a>Tipi di quote di archiviazione

| **Elemento** | **Valore predefinito** | **Descrizione** |
| --- | --- | --- |
| Capacità massima (GB) |2048 |Totale capacità di archiviazione (inclusi i BLOB e tutti gli snapshot associati, tabelle, code) possono essere usate da una sottoscrizione in questa posizione. |
| Numero totale di account di archiviazione |20 |Il numero massimo di account di archiviazione che è possibile creare una sottoscrizione in questa posizione. |

> [!NOTE]  
> Può richiedere fino a due ore prima che viene applicata una quota di archiviazione.

## <a name="network-quota-types"></a>Tipi di quote di rete

| **Elemento** | **Valore predefinito** | **Descrizione** |
| --- | --- | --- |
| Indirizzi IP pubblici massimo |50 |Il numero massimo di indirizzi IP pubblici che è possibile creare una sottoscrizione in questa posizione. |
| Reti virtuali massime |50 |Il numero massimo di reti virtuali che è possibile creare una sottoscrizione in questa posizione. |
| Gateway di rete virtuale massima |1 |Il numero massimo di gateway di rete virtuale (VPN gateway) che è possibile creare una sottoscrizione in questa posizione. |
| Connessioni di rete massima |2 |Il numero massimo di connessioni di rete (da punto a punto o site-to-site) che una sottoscrizione può creare in tutti i gateway di rete virtuale in questa posizione. |
| Servizi di bilanciamento del carico massimo |50 |Il numero massimo di servizi di bilanciamento del carico che può creare una sottoscrizione in questa posizione. |
| Interfacce di rete massime |100 |Il numero massimo di interfacce di rete che è possibile creare una sottoscrizione in questa posizione. |
| Gruppi di sicurezza di rete massima |50 |Il numero massimo di gruppi di sicurezza di rete che è possibile creare una sottoscrizione in questa posizione. |

## <a name="view-an-existing-quota"></a>Visualizzare una quota esistente

Esistono due modi diversi per visualizzare una quota esistente:

### <a name="plans"></a>Piani

1. Nel riquadro di spostamento a sinistra del portale di amministrazione, selezionare **piani**.
2. Selezionare il piano di cui che si desidera visualizzare i dettagli, facendo clic sul relativo nome.
3. Nel pannello aperto, selezionare **servizi e le quote**.
4. Selezionare la quota si desidera vedere facendovi clic sopra nel **nome** colonna.

    [![Le quote](media/azure-stack-quota-types/quotas1sm.png "consente di visualizzare le quote")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Provider di risorse

1. Nel dashboard predefinito del portale di amministrazione, trovare il **provider di risorse** riquadro.
2. Selezionare il servizio con la quota che si desidera visualizzare, ad esempio **Compute**, **Network**, o **archiviazione**.
3. Selezionare **quote**e quindi selezionare la quota si desidera visualizzare.

## <a name="edit-a-quota"></a>Modifica di una quota

Esistono due diversi modi per modificare un obiettivo di vendita:

### <a name="edit-a-plan"></a>Modificare un piano

1. Nel riquadro di spostamento a sinistra del portale di amministrazione, selezionare **piani**.
2. Selezionare il piano per il quale si desidera modificare una quota, facendo clic sul relativo nome.
3. Nel pannello aperto, selezionare **servizi e le quote**.
4. Selezionare la quota si desidera modificare facendo clic su esso nel **nome** colonna.
    [![Le quote](media/azure-stack-quota-types/quotas1sm.png "consente di visualizzare le quote")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. Nel pannello aperto, selezionare **modifica nel componente Compute**, **modificare nella rete**, o **modifica nell'archiviazione**.
    ![Le quote](media/azure-stack-quota-types/quotas3.png "consente di visualizzare le quote")

In alternativa, è possibile seguire questa procedura per modificare un obiettivo di vendita:

1. Nel dashboard del portale di amministratore predefinito, trovare il **provider di risorse** riquadro.
2. Selezionare il servizio con la quota che si desidera modificare, ad esempio **Compute**, **Network**, o **archiviazione**.
3. Successivamente, selezionare **quote**e quindi selezionare la quota che si desidera modificare.
4. Nel **le quote di archiviazione impostato**, **calcolo impostare quote**, o **quote di rete impostato** (a seconda del tipo di quota si è scelto di modifica), modificare i valori e quindi selezionare **Salvare**.

### <a name="edit-original-configuration"></a>Modificare la configurazione originale
  
È possibile scegliere di modificare la configurazione originale di una quota anziché [usando un piano aggiuntivo](create-add-on-plan.md). Quando si modifica una quota, la nuova configurazione applica automaticamente a livello globale per tutti i piani che usano tale quota e tutte le sottoscrizioni esistenti che usano tali piani. Le modifiche a una quota è diversa quando si usa un piano del componente aggiuntivo per fornire una quota modificata, quale un utente sceglie di sottoscrivere.

I nuovi valori per la quota si applicano globalmente a tutti i piani che usano la quota modificata e per tutte le sottoscrizioni esistenti che usano tali piani.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su piani, offerte e quote.](azure-stack-plan-offer-quota-overview.md)
- [Creare le quote durante la creazione di un piano.](azure-stack-create-plan.md)
