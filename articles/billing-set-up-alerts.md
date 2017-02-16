---
title: Impostare avvisi di fatturazione per le sottoscrizioni di Microsoft Azure | Documentazione Microsoft
description: "Viene descritto come è possibile impostare gli avvisi di fatturazione di Azure per evitare sorprese fatturazione."
services: 
documentationcenter: 
author: vikdesai
manager: vikdesai
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: vikdesai
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ce21f881d95b369073168d205c315d74de9f24ef


---
# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Impostare avvisi di fatturazione per le sottoscrizioni di Microsoft Azure
Si è preoccupati per la spesa mensile per la sottoscrizione di Azure? Se si amministrano gli account per una sottoscrizione di Azure, è possibile usare Azure Billing Alert Service per creare avvisi di fatturazione personalizzati che consentono di monitorare e gestire l'attività di fatturazione per gli account Azure.

Questo servizio è disponibile in anteprima, quindi è necessario innanzitutto effettuare l'iscrizione. Visitare la [pagina Anteprima funzionalità](https://account.windowsazure.com/PreviewFeatures) nel portale di gestione account Azure per abilitare questa funzionalità.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Impostare la soglia di avviso e i destinatari di posta elettronica
Dopo aver ricevuto tramite posta elettronica la conferma dell'attivazione del servizio di fatturazione per la sottoscrizione, visitare la [pagina relativa alle sottoscrizioni](https://account.windowsazure.com/Subscriptions) nel portale degli account. Fare clic sulla sottoscrizione che si desidera monitorare, quindi selezionare **Avvisi**.

![][Image1]

Fare clic su **Aggiungi avviso** per creare il primo avviso. È possibile impostare un totale di cinque avvisi di fatturazione per sottoscrizione, con una soglia diversa e un massimo di due destinatari di posta elettronica per ciascun avviso.

![][Image2]

Quando si aggiunge un avviso, assegnare un nome univoco, scegliere una soglia di spesa e gli indirizzi di posta elettronica a cui verrà inviato. Mentre si imposta la soglia, è possibile scegliere un valore per **Totale fattura** o **Credito monetario** dall'elenco **Avviso per**. Se si specifica un totale fattura, viene inviato un avviso quando la spesa per la sottoscrizione supera la soglia. Se si specifica un credito monetario, viene inviato un avviso quando i crediti monetari scendono al di sotto del limite. I crediti monetari in genere si applicano alle versioni di valutazione gratuite e alle sottoscrizioni associate agli account MSDN.

![][Image3]

Azure supporta qualsiasi indirizzo di posta elettronica ma non ne verifica il corretto funzionamento, pertanto è necessario accertarsi che non siano presenti errori di digitazione.

## <a name="check-on-your-alerts"></a>Controllare gli avvisi
Dopo aver impostato gli avvisi, nel centro account viene visualizzato un elenco degli avvisi già impostati ed è indicato il numero di avvisi aggiuntivi che è possibile ancora impostare. Per ogni avviso è possibile visualizzare la data e l'ora di invio, il tipo di avviso (per totale fattura o credito monetario) e il limite impostato. Il formato dell'ora è 24 ore UTC (Universal Time Coordinate) e il formato della data è aaaa-mm-gg. Fare clic sul segno più per modificare un avviso nell'elenco o sul simbolo del cestino per eliminarlo.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png



<!--HONumber=Dec16_HO2-->


