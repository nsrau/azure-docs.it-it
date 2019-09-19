---
title: Monitorare e tenere traccia dell'utilizzo dei servizi gratuiti di Azure
description: Informazioni su come controllare l'utilizzo dei servizi gratuiti nel portale di Azure e nel file CSV dei dati di utilizzo.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491416"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Controllare l'utilizzo dei servizi gratuiti inclusi nell'account Azure gratuito

I costi dei servizi inclusi gratuitamente in un account Azure gratuito non vengono addebitati, a meno che non si superino i relativi limiti. Per rimanere entro i limiti, è possibile usare il portale di Azure o il file di dati di utilizzo per monitorare e tenere traccia dell'uso dei servizi gratuiti.

## <a name="check-usage-in-the-azure-portal"></a>Controllare l'utilizzo nel portale di Azure

1.  Accedere al [portale di Azure](https://portal.azure.com).

2.  Nell'area di spostamento sinistra selezionare **Tutti i servizi**.

3.  Selezionare **Sottoscrizioni**.

4.  Selezionare la sottoscrizione che è stata creata al momento della registrazione per l'account gratuito.

    ![Screenshot che mostra tutte le sottoscrizioni](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  La sezione Panoramica mostra le informazioni essenziali sulla sottoscrizione, ad esempio ID sottoscrizione, tipo di offerta e nome della sottoscrizione. È anche possibile trovare informazioni sulla data di scadenza del credito per l'account gratuito.

    ![Screenshot che mostra le informazioni essenziali della sottoscrizione](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Scorrere in basso per trovare le informazioni sui costi attuali e previsti. I costi includono l'utilizzo dei servizi non inclusi nell'account gratuito e l'utilizzo che supera i limiti dei servizi gratuiti.

    ![Screenshot che mostra le informazioni sui costi della sottoscrizione](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Nell'ultima parte della sezione Panoramica è presente una tabella che mostra l'utilizzo dei servizi gratuiti.

    ![Screenshot che mostra l'uso dei servizi gratuiti](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    La tabella è composta dalle colonne seguenti:

* **Nome misuratore:** Identifica l'unità di misura del contatore utilizzato. Per informazioni sul mapping del misuratore di servizi, vedere [Comprendere il mapping del misuratore di servizi gratuiti](billing-understand-free-service-meter-mapping.md).
* **Utilizzo/Limite:** utilizzo e limite del mese attuale per il misuratore. Questa informazione è disponibile anche nella barra di stato.
* **Stato:** stato dell'uso del misuratore. In base al modello di utilizzo è possibile avere uno degli stati seguenti:
  * **Non in uso:** il misuratore non è stato usato o l'uso del misuratore non ha raggiunto il sistema di fatturazione.
  * **Il limite è stato superato il giorno \<Data>:** il limite per il misuratore è stato superato il giorno \<Data>.
  * **Il superamento è improbabile:** è improbabile che il limite per il misuratore verrà superato.
  * **Data del superamento \<Data>:** è improbabile che il limite per il misuratore verrà superato il giorno \<Data>.

## <a name="check-usage-with-the-usage-file"></a>Controllare l'utilizzo con il file di dati di utilizzo

Il file di dati di utilizzo fornisce informazioni dettagliate per la sottoscrizione di Azure. È possibile scaricare il file di dati di utilizzo mensile e giornaliero dal Centro account di Azure. Per informazioni su come scaricare il file di dati di utilizzo e comprendere l'accesso richiesto, vedere [Ottenere la fattura e i dati di utilizzo](billing-download-azure-invoice-daily-usage-date.md). Per informazioni sulle colonne nel file di dati di utilizzo, vedere [Informazioni sulle condizioni dell'utilizzo](billing-understand-your-usage.md).

Il file di dati di utilizzo contiene informazioni sui dati di utilizzo per i servizi gratuiti e a pagamento. I misuratori dei servizi gratuiti hanno la parola **Gratuito** alla fine del nome del misuratore. Per trovare i misuratori gratuiti, aprire il file in Excel e filtrare la colonna **Categoria misuratore** per le celle che contengono il testo **- Gratuito** (usare il filtro Filtri per testo &rarr; Contiene).


![Screenshot che mostra l'uso dei servizi gratuiti](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Aggiornare la sottoscrizione](billing-upgrade-azure-subscription.md)
