---
title: Tipi di quote in Azure Stack | Microsoft Docs
description: Esaminare i tipi di quote diversi disponibili per i servizi e risorse in Azure Stack.
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
ms.topic: get-started-article
ms.date: 07/30/2018
ms.author: sethm
ms.reviewer: xiaofmao
ms.openlocfilehash: 3c0ab236dd6fce10be0a50c435f04517e14c1387
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077596"
---
# <a name="quota-types-in-azure-stack"></a>Tipi di quote in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

[Le quote](azure-stack-plan-offer-quota-overview.md#plans) definire i limiti delle risorse che può effettuare il provisioning o utilizzare una sottoscrizione utente. Ad esempio, una quota potrebbe consentire all'utente di creare fino a cinque macchine virtuali. Ogni risorsa può avere un proprio tipi di quote.

## <a name="compute-quota-types"></a>Tipi di quota di calcolo 
| **Tipo** | **Valore predefinito** | **Descrizione** |
| --- | --- | --- |
| Numero massimo di macchine virtuali | 20 | Il numero massimo di macchine virtuali che è possibile creare una sottoscrizione in questa posizione. |
| Numero massimo di memorie centrali delle macchine virtuali | 50 | Il numero massimo di core che è possibile creare una sottoscrizione in questa posizione (ad esempio, una macchina virtuale A3 dispone di quattro core). |
| Numero massimo di set di disponibilità | 10 | Il numero massimo di set di disponibilità che possono essere create in questa posizione. |
| Imposta il numero massimo di scalabilità di macchine virtuali | 20 | Il numero massimo di set di scalabilità di macchine virtuali che possono essere create in questa posizione. |

## <a name="storage-quota-types"></a>Tipi di quote di archiviazione 
| **Elemento** | **Valore predefinito** | **Descrizione** |
| --- | --- | --- |
| Capacità massima (GB) |500 |Capacità di archiviazione totale che può essere utilizzata da una sottoscrizione in questa posizione. |
| Numero totale di account di archiviazione |20 |Il numero massimo di account di archiviazione che è possibile creare una sottoscrizione in questa posizione. |

> [!NOTE]  
> Può richiedere fino a due ore prima che viene applicata una quota di archiviazione.


## <a name="network-quota-types"></a>Tipi di quote di rete
| **Elemento** | **Valore predefinito** | **Descrizione** |
| --- | --- | --- |
| Indirizzi IP pubblici max |50 |Il numero massimo di indirizzi IP pubblici che è possibile creare una sottoscrizione in questa posizione. |
| Reti virtuali massime |50 |Il numero massimo di reti virtuali che è possibile creare una sottoscrizione in questa posizione. |
| Gateway di rete virtuale massima |1 |Il numero massimo di gateway di rete virtuale (VPN gateway) che è possibile creare una sottoscrizione in questa posizione. |
| Numero massimo di connessioni di rete |2 |Il numero massimo di connessioni di rete (da punto a punto o site-to-site) che una sottoscrizione può creare in tutti i gateway di rete virtuale in questa posizione. |
| Servizi di bilanciamento del carico massimo |50 |Il numero massimo di servizi di bilanciamento del carico che può creare una sottoscrizione in questa posizione. |
| Schede di interfaccia di rete max |100 |Il numero massimo di interfacce di rete che è possibile creare una sottoscrizione in questa posizione. |
| Gruppi di sicurezza di rete max |50 |Il numero massimo di gruppi di sicurezza di rete che è possibile creare una sottoscrizione in questa posizione. |

## <a name="view-an-existing-quota"></a>Visualizzare una quota esistente
1. Nel dashboard predefinito del portale di amministrazione, trovare il **provider di risorse** riquadro.
2. Selezionare il servizio con la quota che si desidera visualizzare, ad esempio **Compute** oppure **archiviazione**.
3. Selezionare **quote**e quindi selezionare la quota si desidera visualizzare.


## <a name="edit-a-quota"></a>Modifica di una quota  
È possibile scegliere di modificare la configurazione originale di una quota anziché [usando un piano aggiuntivo](create-add-on-plan.md). Quando si modifica una quota, la nuova configurazione applica automaticamente a livello globale per tutti i piani che usano tale quota e tutte le sottoscrizioni esistenti che usano tali piani. Le modifiche a una quota è diversa quando si usa un piano del componente aggiuntivo per fornire una quota modificata, quale un utente sceglie di sottoscrivere. 

### <a name="to-edit-a-quota"></a>Per modificare una quota  
1. Nel dashboard predefinito del portale di amministrazione, trovare il **provider di risorse** riquadro.
2. Selezionare il servizio con la quota che si desidera modificare, ad esempio **Compute**, **Network**, o **archiviazione**.
3. Successivamente, selezionare **quote**e quindi selezionare la quota che si desidera modificare.
4. Nel **impostare le quote** riquadro, modificare i valori e quindi selezionare **salvare**. 

I nuovi valori per la quota si applicano globalmente a tutti i piani che usano la quota modificata e per tutte le sottoscrizioni esistenti che usano tali piani. 



## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su piani, offerte e quote.](azure-stack-plan-offer-quota-overview.md)
- [Creare le quote durante la creazione di un piano.](azure-stack-create-plan.md)
