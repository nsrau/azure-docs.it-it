---
title: Rilevare il saldo del credito di Azure per contratto dei clienti di Microsoft | Microsoft Docs
description: Informazioni su come controllare il saldo del credito Azure per contratto dei clienti Microsoft.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: amberb
ms.openlocfilehash: 1e8c3e6863b9cd8f2f5ced18a57918c32c865e75
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372265"
---
# <a name="track-azure-credit-balance-for-microsoft-customer-agreement"></a>Tenere traccia di saldo del credito di Azure per il contratto di clienti Microsoft

È possibile controllare il saldo del credito di Azure per Microsoft Customer accordo nel portale di Azure. Usare i crediti per pagare i prodotti che vengono analizzati i crediti.

Vengono addebitati quando si utilizzano i prodotti che non vengono analizzati i crediti o l'utilizzo superi il saldo del credito. Per altre informazioni, vedere [prodotti che non sono coperti dai crediti Azure.](#products-that-arent-covered-by-azure-credits)

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-credit-balance-in-the-azure-portal"></a>Controllare il saldo del credito nel portale di Azure

1. Accedere al [portale di Azure]( https://portal.azure.com).

2. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di gestione costi + fatturazione](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Passare al profilo di fatturazione. In base all'accesso, potrebbe essere necessario selezionare un account di fatturazione. L'account di fatturazione, selezionare **fatturazione profili** e quindi un profilo di fatturazione.

4. Selezionare **crediti Azure**.

5. La pagina di crediti di Azure consente di visualizzare le informazioni seguenti:

   ![Screenshot del saldo del credito e le transazioni per un profilo di fatturazione](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Nome               | Definizione                           |
   |--------------------|--------------------------------------------------------|
   | Saldo stimato  | Quantità stimata di crediti che sono prendere in considerazione tutti fatturati e le transazioni in sospeso |
   | Saldo corrente    | Quantità di crediti al momento della stesura l'ultima fattura. Non include eventuali transazioni in sospeso |
   | Transazioni       | Tutte le transazioni fatturazione interessato il saldo del credito di Azure |

   Quando il saldo stimato pari a 0, vengono addebitate tutte all'utilizzo, tra cui per i prodotti che vengono analizzati i crediti.

6. Selezionare **elenco di crediti** per visualizzare l'elenco di crediti per il profilo di fatturazione. L'elenco di crediti fornisce le informazioni seguenti:

   ![Screenshot degli elenchi di crediti per un profilo di fatturazione](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Nome                 | Definizione                           |
   |----------------------|--------------------------------------------------------|
   | `Source`               | L'origine di acquisizione del credito |
   | Data di inizio           | La data di quando è stato acquistato il credito |
   | Expiration date      | La data in cui scade la carta di credito |
   | Balance              | Il saldo l'ultima fattura |
   | Quantità originale      | La quantità originale di carta di credito |
   | Stato               | Lo stato corrente della carta di credito. Lo stato può essere attivo, utilizzato, scaduto o in scadenza |

## <a name="how-credits-are-used-in-microsoft-customer-agreement"></a>Modalità di utilizzo crediti nel contratto di Microsoft dal cliente

In un account di fatturazione per un contratto di clienti Microsoft, utilizzare i profili di fatturazione per gestire le fatture e i metodi di pagamento. Viene generata una fattura mensile per ogni profilo di fatturazione e userai i metodi di pagamento per pagare le fatture.

I crediti di Azure sono uno dei metodi di pagamento. Carta di credito è ottenere da Microsoft, ad esempio carta di credito promozionale e il credito a livello di servizio. Questi crediti vengono assegnati a un profilo di fatturazione. Quando viene generata una fattura per il profilo di fatturazione, i crediti vengono applicati automaticamente all'importo fatturato totale per calcolare la quantità che è necessario pagare. Si paga la quantità rimanente con un altro metodo di pagamento, come controllo o bonifico.

## <a name="products-that-arent-covered-by-azure-credits"></a>Prodotti che non sono coperti dai crediti Azure

 I prodotti seguenti non sono coperti dai crediti Azure. L'addebito viene effettuato per l'uso di questi prodotti indipendentemente dal fatto il saldo del credito:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 
- Utente registrato
- Openlogic
- Utente registrato XenApp Essentials con Diritti di accesso remoto
- Ubuntu Advantage
- Visual Studio Enterprise (mensile)
- Visual Studio Enterprise (annuale)
- Visual Studio Professional (mensile)
- Visual Studio Professional (annuale)
- Prodotti di Azure Marketplace
- Piani di supporto per Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'account di fatturazione per Microsoft Customer contratto](billing-mca-overview.md)
- [Comprendere i termini sulla fattura di contratto di Microsoft dal cliente](billing-mca-understand-your-invoice.md)