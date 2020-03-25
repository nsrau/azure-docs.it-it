---
title: Monitorare e tenere traccia dell'utilizzo dei servizi gratuiti di Azure
description: Informazioni su come controllare l'utilizzo dei servizi gratuiti nel portale di Azure.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 0a62deecc6f70bb21059ae3ddda9eea173de65fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199807"
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

* **Contatore:** identifica l'unità di misura per il servizio utilizzato.
* **Utilizzo/Limite:** utilizzo e limite del mese attuale per il misuratore.
* **Stato:** stato di utilizzo del servizio. In base all'utilizzo, sarà mostrato uno degli stati seguenti:
  * **Non in uso:** il misuratore non è stato usato o l'uso del misuratore non ha raggiunto il sistema di fatturazione.
  * **Il limite è stato superato il giorno \<Data>:** il limite per il misuratore è stato superato il giorno \<Data>.
  * **Il superamento è improbabile:** è improbabile che il limite per il misuratore verrà superato.
  * **Data del superamento \<Data>:** è improbabile che il limite per il misuratore verrà superato il giorno \<Data>.

> [!IMPORTANT]
>
> I servizi gratuiti sono disponibili solo per la sottoscrizione creata al momento dell'iscrizione per l'account Azure gratuito. Se la tabella dei servizi gratuiti non è visualizzata nella pagina della panoramica della sottoscrizione, i servizi gratuiti non sono disponibili per la sottoscrizione.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Aggiornare l'account Azure gratuito](upgrade-azure-subscription.md)
