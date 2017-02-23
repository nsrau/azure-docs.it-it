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
ms.date: 02/09/2017
ms.author: vikdesai
translationtype: Human Translation
ms.sourcegitcommit: 54fcf8f12ffe5ead398b2556eb4c94840c15f530
ms.openlocfilehash: b318843c1d1081bb5817f3f2aef6962f7fc00136


---
# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Impostare avvisi di fatturazione per le sottoscrizioni di Microsoft Azure
Se si amministrano gli account per una sottoscrizione di Azure, è possibile usare Azure Billing Alert Service per creare avvisi di fatturazione personalizzati che consentono di monitorare e gestire l'attività di fatturazione per gli account Azure.

Questo servizio è in anteprima, pertanto è necessario abilitarlo nella pagina relativa alle funzionalità di anteprima.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Impostare la soglia di avviso e i destinatari di posta elettronica
1. Visitare la [pagina relativa alle funzionalità di anteprima](https://account.windowsazure.com/PreviewFeatures) e abilitare **Billing Alert Service**.

1. Dopo aver ricevuto tramite posta elettronica la conferma dell'attivazione del servizio di fatturazione per la sottoscrizione, visitare la [pagina relativa alle sottoscrizioni](https://account.windowsazure.com/Subscriptions) nel portale degli account. Fare clic sulla sottoscrizione che si desidera monitorare, quindi selezionare **Avvisi**.

    ![Schermata delle sottoscrizioni del Centro account di Azure, con gli avvisi in evidenza][Image1]

2. Successivamente, fare clic su **Aggiungi avviso** per creare il primo avviso. È possibile impostare un totale di cinque avvisi di fatturazione per sottoscrizione, con una soglia diversa e un massimo di due destinatari di posta elettronica per ciascun avviso.

    ![Schermata degli avvisi, in cui è possibile aggiungere un avviso][Image2]

3. Quando si aggiunge un avviso, assegnare un nome univoco, scegliere una soglia di spesa e gli indirizzi di posta elettronica a cui verrà inviato. Mentre si imposta la soglia, è possibile scegliere un valore per **Totale fattura** o **Credito monetario** dall'elenco **Avviso per**. Se si specifica un totale fattura, viene inviato un avviso quando la spesa per la sottoscrizione supera la soglia. Se si specifica un credito monetario, viene inviato un avviso quando i crediti monetari scendono al di sotto del limite. I crediti monetari in genere si applicano alle sottoscrizioni della versione di valutazione gratuita e Visual Studio.

    ![Schermata della funzionalità di aggiunta avviso, in cui è possibile configurare i destinatari][Image3]

Azure supporta qualsiasi indirizzo di posta elettronica ma non ne verifica il corretto funzionamento, pertanto è necessario accertarsi che non siano presenti errori di digitazione.

## <a name="check-on-your-alerts"></a>Controllare gli avvisi
Dopo aver impostato gli avvisi, nel centro account viene visualizzato un elenco degli avvisi già impostati ed è indicato il numero di avvisi aggiuntivi che è possibile ancora impostare. Per ogni avviso è possibile visualizzare la data e l'ora di invio, il tipo di avviso (per totale fattura o credito monetario) e il limite impostato. Il formato dell'ora è 24 ore UTC (Universal Time Coordinate) e il formato della data è aaaa-mm-gg. Fare clic sul segno più per modificare un avviso nell'elenco o sul simbolo del cestino per eliminarlo.

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Avvisi di fatturazione per i clienti con contratto Enterprise (EA, Enterprise Agreement)
Nell'ambito di una registrazione i clienti EA possono usufruire di avvisi per ogni reparto, impostando le quote di spesa. Per iniziare, vedere [Department Spending Quotas](https://ea.azure.com/helpdocs/departmentSpendingQuotas) (Quote di spesa per reparto) nel portale EA.

## <a name="learn-more-about-azure-cost-management"></a>Altre informazioni sulla gestione dei costi in Azure
- Stimare i costi usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/), il [calcolatore del costo totale di proprietà](https://aka.ms/azure-tco-calculator) e quando si aggiunge un servizio.
- [Verificare regolarmente uso e costi nel portale di Azure](billing-getting-started.md#costs).
- Attivare i [consigli di Azure Advisor sui costi](../advisor/advisor-cost-recommendations.md).

Per altre informazioni, vedere [Introduzione alla gestione dei costi e alla fatturazione di Azure](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 



<!--HONumber=Feb17_HO2-->


