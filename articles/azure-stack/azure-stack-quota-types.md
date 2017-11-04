---
title: Tipi di quote nello Stack di Azure | Documenti Microsoft
description: Esaminare i tipi diversi di quota disponibili per servizi e le risorse nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/23/2017
ms.author: erikje
ms.openlocfilehash: d9bb048ece32bf5b34e05d7459488aa0f24d0d44
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="quota-types-in-azure-stack"></a>Tipi di quote nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

[Le quote](azure-stack-plan-offer-quota-overview.md#plans) definire i limiti delle risorse che è in grado di eseguire il provisioning o utilizzare una sottoscrizione utente. Ad esempio, una quota potrebbe consentire all'utente di creare fino a cinque macchine virtuali. Ogni risorsa può avere un proprio tipi di quote.

## <a name="compute-quota-types"></a>Tipi di quote di calcolo
| **Tipo** | **Valore predefinito** | **Descrizione** |
| --- | --- | --- |
| Numero massimo di macchine virtuali | 20 | Il numero massimo di macchine virtuali che è possibile creare una sottoscrizione in questa posizione. |
| Numero massimo di core di macchina virtuale | 50 | Il numero massimo di core che è possibile creare una sottoscrizione in questa posizione (ad esempio, una macchina virtuale A3 ha quattro core). |
| Imposta il numero massimo di disponibilità | 10 | Il numero massimo di set di disponibilità che possono essere creati in questa posizione. |
| Imposta il numero massimo di scalabilità della macchina virtuale | 20 | Il numero massimo di set di scalabilità di macchine virtuali che possono essere creati in questa posizione. |

> [!NOTE]
> Consente di calcolare le quote non vengono applicate in questa versione technical preview.
> 
> 

## <a name="storage-quota-types"></a>Tipi di quote di archiviazione
| **Elemento** | **Valore predefinito** | **Descrizione** |
| --- | --- | --- |
| Capacità massima (GB) |500 |Capacità di archiviazione totale possono essere utilizzate da una sottoscrizione in questa posizione. |
| Numero totale di account di archiviazione |20 |Il numero massimo di account di archiviazione che è possibile creare una sottoscrizione in questa posizione. |

## <a name="network-quota-types"></a>Tipi di quote di rete
| **Elemento** | **Valore predefinito** | **Descrizione** |
| --- | --- | --- |
| Numero massimo IP pubblici |50 |Il numero massimo di indirizzi IP pubblici che è possibile creare una sottoscrizione in questa posizione. |
| Reti virtuali max |50 |Il numero massimo di reti virtuali che è possibile creare una sottoscrizione in questa posizione. |
| Gateway di rete virtuale max |1 |Il numero massimo di gateway di rete virtuale (VPN gateway) che è possibile creare una sottoscrizione in questa posizione. |
| Numero massimo di connessioni di rete |2 |Il numero massimo di connessioni di rete (Point-to- o site-to-site) che è possibile creare una sottoscrizione in tutti i gateway di rete virtuale in questa posizione. |
| Servizi di bilanciamento del carico massimo |50 |Il numero massimo di servizi di bilanciamento del carico che è possibile creare una sottoscrizione in questa posizione. |
| Schede di interfaccia di rete max |100 |Il numero massimo di interfacce di rete che è possibile creare una sottoscrizione in questa posizione. |
| Gruppi di sicurezza di rete max |50 |Il numero massimo di gruppi di sicurezza di rete che è possibile creare una sottoscrizione in questa posizione. |

## <a name="view-an-existing-quota"></a>Consente di visualizzare una quota esistente
1. Fare clic su **più servizi** > **i provider di risorse**.
2. Selezionare il servizio con la quota che si desidera visualizzare.
3. Fare clic su **quote**e selezionare la quota di cui si desidera visualizzare.

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sui piani, offerte e le quote.](azure-stack-plan-offer-quota-overview.md)

[Creare le quote durante la creazione di un piano.](azure-stack-create-plan.md)
