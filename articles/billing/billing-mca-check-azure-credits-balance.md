---
title: Tenere traccia del saldo dei crediti Azure per un Contratto del cliente Microsoft
description: Informazioni su come controllare il saldo dei crediti Azure per un Contratto del cliente Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: b8faf3801a2217e6ef7040a7c2b67c7e6fad8e60
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709593"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Tenere traccia del saldo dei crediti Azure per un Contratto del cliente Microsoft

È possibile controllare il saldo dei crediti Azure per un Contratto del cliente Microsoft nel portale di Azure. I crediti vengono usati per il pagamento degli addebiti da essi coperti.

Vengono addebitati costi quando si usano prodotti non coperti dai crediti o quando l'utilizzo supera il saldo dei crediti. Per altre informazioni, vedere [Prodotti non coperti dai crediti Azure(#products-that-arent-covered-by-azure-credits).

Questo articolo si applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Controllare il saldo dei crediti

1. Accedere al [portale di Azure]( https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Selezionare **Crediti Azure** sul lato sinistro. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione o un profilo di fatturazione e quindi **Crediti Azure**.

4. Nella pagina Crediti Azure vengono visualizzate le informazioni seguenti:

   ![Screenshot del saldo dei crediti e delle transazioni per un profilo di fatturazione](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termine               | Definizione                           |
   |--------------------|--------------------------------------------------------|
   | Saldo stimato  | Importo stimato dei crediti disponibili dopo aver preso in considerazione tutte le transazioni fatturate e in sospeso |
   | Saldo corrente    | Importo dei crediti dall'ultima fattura. Non include le transazioni in sospeso |
   | Transazioni       | Tutte le transazioni di fatturazione che hanno influito sul saldo dei crediti Azure |

   Quando il saldo stimato scende a 0, viene addebitato tutto l'utilizzo, anche quello riguardante prodotti coperti dai crediti.

6. Selezionare **Elenco dei crediti** per visualizzare l'elenco dei crediti per il profilo di fatturazione. L'elenco dei crediti offre le informazioni seguenti:

   ![Screenshot dell'elenco dei crediti per un profilo di fatturazione](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termine | Definizione |
   |---|---|
   | Saldo stimato | Importo del credito Azure disponibile dopo la sottrazione dal saldo corrente degli addebiti non fatturati idonei per il credito|
   | Saldo corrente | Importo del credito Azure disponibile prima di prendere in considerazione gli addebiti non fatturati idonei per il credito. Viene calcolato aggiungendo i nuovi crediti Azure ricevuti al saldo del credito al momento dell'ultima fattura|
   | Source (Sorgente) | Origine di acquisizione del credito |
   | Data di inizio | Data in cui è stato acquisito il credito |
   | Expiration date | Data di scadenza del credito |
   | Balance | Saldo dall'ultima fattura |
   | Importo originale | Importo originale del credito |
   | Stato | Stato corrente del credito. Può essere attivo, usato, scaduto o in scadenza |

## <a name="how-credits-are-used"></a>Modalità d'uso dei crediti

In un account di fatturazione per un Contratto del cliente Microsoft, per gestire le fatture e i metodi di pagamento si usano profili di fatturazione. Per ogni profilo di fatturazione viene generata una fattura mensile, per il cui pagamento si usano i metodi di pagamento.

I crediti Azure sono uno dei metodi di pagamento. È possibile ottenere credito da Microsoft come credito promozionale e credito del livello di servizio. Questi crediti vengono assegnati a un profilo di fatturazione. Quando viene generata una fattura per il profilo di fatturazione, i crediti vengono applicati automaticamente all'importo fatturato totale per calcolare l'importo da pagare. L'importo rimanente viene pagato con un altro metodo di pagamento, ad esempio assegno o bonifico.

## <a name="products-that-arent-covered-by-azure-credits"></a>Prodotti non coperti dai crediti Azure

 I prodotti seguenti non sono coperti dai crediti Azure e il relativo uso viene addebitato indipendentemente dal saldo dei crediti:

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
- Piani di supporto di Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'account di fatturazione del Contratto del cliente Microsoft](billing-mca-overview.md)
- [Informazioni sui termini presenti nella fattura del Contratto del cliente Microsoft](billing-mca-understand-your-invoice.md)
