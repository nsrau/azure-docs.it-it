---
title: Monitorare e tenere traccia dell'utilizzo dei servizi gratuiti di Azure
description: Informazioni su come controllare l'utilizzo dei servizi gratuiti nel portale di Azure.
author: amberbhargava
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 83937e31d844ba0cc8efc17f1ecefa2ad6e7a7c1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992829"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Controllare l'uso dei servizi gratuiti inclusi con l'account gratuito di Azure

I costi dei servizi inclusi gratuitamente in un account Azure gratuito non vengono addebitati, a meno che non si superino i relativi limiti. Per rimanere entro i limiti, è possibile usare il portale di Azure per monitorare l'utilizzo dei servizi gratuiti.

## <a name="check-usage-in-the-azure-portal"></a>Controllare l'utilizzo nel portale di Azure

1.  Accedere al [portale di Azure](https://portal.azure.com).

2.  Cercare **Sottoscrizioni**.

    ![Screenshot che mostra la ricerca di sottoscrizioni nel portale](./media/check-free-service-usage/billing-search-subscriptions.png)

3.  Selezionare la sottoscrizione creata al momento dell'iscrizione per l'account gratuito.

4.  Scorrere verso il basso per trovare la tabella che mostra l'utilizzo dei servizi gratuiti.

    ![Screenshot che mostra l'uso dei servizi gratuiti](./media/check-free-service-usage/subscription-usage-free-services.png)

    La tabella è composta dalle colonne seguenti:

* **Contatore:** Identifica l'unità di misura per il servizio utilizzato.
* **Uso/Limite:** uso e limite del mese attuale per il misuratore.
* **Stato:** Stato di utilizzo del servizio. In base all'utilizzo, sarà mostrato uno degli stati seguenti:
  * **Non in uso:** Il contatore non è stato usato o l'utilizzo per il contatore non ha raggiunto il sistema di fatturazione.
  * **Il limite è stato superato il giorno \<Date>:** il limite per il misuratore è stato superato il giorno \<Date>.
  * È **improbabile che superi:** È improbabile che venga superato il limite per il contatore.
  * **Supera \<> di data:** È probabile che venga superato il limite per il contatore in \<data >.

> [!IMPORTANT]
>
> I servizi gratuiti sono disponibili solo per la sottoscrizione creata al momento dell'iscrizione per l'account Azure gratuito. Se la tabella dei servizi gratuiti non è visualizzata nella pagina della panoramica della sottoscrizione, i servizi gratuiti non sono disponibili per la sottoscrizione.

## <a name="need-help-contact-us"></a>Opzioni per Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Aggiornare l'account Azure gratuito](upgrade-azure-subscription.md)
