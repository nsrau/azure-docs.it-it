---
title: Monitorare e tenere traccia dei servizi gratuiti - Azure | Microsoft Docs
description: Informazioni su come controllare l'utilizzo dei servizi gratuiti. Usare il portale di Azure e un file CSV relativo ai dati di utilizzo.
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 8bc63091dfba822f9839f61dd12c212154ba695d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Controllare l'uso dei servizi gratuiti inclusi con l'account gratuito di Azure 

I servizi non inclusi nell'account gratuito di Azure non vengono addebitati finché non si superano i limiti di questi servizi. Per rimanere entro i limiti, è possibile usare il portale di Azure o il file di dati di utilizzo per monitorare e tenere traccia dell'uso dei servizi gratuiti. 

## <a name="check-usage-on-the-azure-portal"></a>Controllare l'uso sul portale di Azure

1.  Accedere al [Portale di Azure]( http://portal.azure.com).

2.  Nell'area di spostamento a sinistra selezionare **Tutti i servizi**.

3.  Selezionare **Sottoscrizioni**.

4.  Selezionare la sottoscrizione che è stata creata al momento della registrazione per l'account gratuito.

    ![Screenshot che mostra tutte le sottoscrizioni](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Nella sezione Panoramica vengono elencate le informazioni essenziali della sottoscrizione, quali ID sottoscrizione, tipo di offerta e nome della sottoscrizione. È anche possibile trovare informazioni sulla data di scadenza del credito per l'account gratuito.

    ![Screenshot che mostra le informazioni essenziali della sottoscrizione](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Scorrere verso il basso per trovare le informazioni sui costi attuali e previsti. I costi includono l'uso dei servizi non inclusi nell'account gratuito e l'uso che supera i limiti dei servizi gratuiti. 

    ![Screenshot che mostra le informazioni sui costi della sottoscrizione](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Nell'ultima parte della sezione Panoramica è presenta una tabella relativa all'uso dei servizi gratuiti. 

    ![Screenshot che mostra l'uso dei servizi gratuiti](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    La tabella contiene le colonne seguenti:

* **Nome misuratore:** identifica l'unità di misura del contatore utilizzato. Per informazioni sul mapping del misuratore di servizi, vedere [Comprendere il mapping del misuratore di servizi gratuiti](billing-understand-free-service-meter-mapping.md). 
* **Uso/Limite:** uso e limite del mese attuale per il misuratore. Questa informazione è disponibile anche nella barra di stato.
* **Stato**: stato dell'uso del misuratore. In base al modello di utilizzo è possibile avere uno di questi valori.
  * **Non in uso:** il misuratore non è stato usato o l'uso del misuratore non ha raggiunto il sistema di fatturazione.
  * **Il limite è stato superato il giorno \<Date>:** il limite per il misuratore è stato superato il giorno \<Date>.
  * **Il superamento è improbabile:** è improbabile che il limite per il misuratore verrà superato.
  * **Il limite verrà superato il giorno \<Date>:** è probabile che il limite per il misuratore verrà superato il giorno \<Date>.


## <a name="check-usage-through-the-usage-file"></a>Controllare l'utilizzo tramite il file di dati di utilizzo

Il file di dati di utilizzo fornisce informazioni dettagliate per la sottoscrizione di Azure. È possibile scaricare il file di dati di utilizzo mensile e giornaliero dal Centro account di Azure. Per informazioni su come scaricare il file di dati di utilizzo e comprendere l'accesso richiesto, vedere [Ottenere la fattura e i dati di utilizzo](billing-download-azure-invoice-daily-usage-date.md). Per informazioni sulle colonne nel file di dati di utilizzo, vedere [Informazioni sulle condizioni dell'utilizzo](billing-understand-your-usage.md). 

Il file di dati di utilizzo contiene informazioni sui dati di utilizzo per i servizi gratuiti e a pagamento. I misuratori dei servizi gratuiti hanno la parola **Gratuito** alla fine del nome del misuratore. Per trovare i misuratori gratuiti, aprire il file in Excel e filtrare la **colonna Categoria misuratore** per le celle che contengono il testo **- Gratuito** (Usare il filtro Filtri per testo &rarr; Contiene) &nbsp;

![Screenshot che mostra l'uso dei servizi gratuiti](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
