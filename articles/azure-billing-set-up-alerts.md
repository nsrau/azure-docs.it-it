<properties 
	pageTitle="Impostare avvisi di fatturazione per le sottoscrizioni Microsoft Azure" 
	description="Viene descritto come è possibile impostare gli avvisi di fatturazione di Azure per evitare sorprese fatturazione." 
	services="" 
	documentationCenter="" 
	authors="vikdesai" 
	manager="msmbaldwin" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="vikdesai"/>

# Impostare avvisi di fatturazione per le sottoscrizioni Microsoft Azure

Si è preoccupati per la spesa mensile per la sottoscrizione di Azure? Se si amministrano gli account per una sottoscrizione di Azure, è possibile usare Azure Billing Alert Service per creare avvisi di fatturazione personalizzati che consentono di monitorare e gestire l'attività di fatturazione per gli account Azure.

Poiché si tratta di un servizio in versione di anteprima, è innanzitutto necessario effettuare l'iscrizione. A questo scopo, visitare la <a href="https://account.windowsazure.com/PreviewFeatures">pagina relativa alle funzionalità di anteprima</a> nel portale di gestione degli account Azure.

## Impostare la soglia di avviso e i destinatari di posta elettronica

Dopo aver ricevuto tramite posta elettronica la conferma dell'attivazione del servizio di fatturazione per la sottoscrizione, visitare la <a href="https://account.windowsazure.com/Subscriptions">pagina relativa alle sottoscrizioni</a> nel portale degli account. Fare clic sulla sottoscrizione che si desidera monitorare, quindi selezionare **Avvisi**.

![][Image1]

Fare clic su **Aggiungi avviso** per creare il primo avviso. È possibile impostare un totale di cinque avvisi di fatturazione per sottoscrizione, con una soglia diversa e un massimo di due destinatari di posta elettronica per ciascun avviso.

![][Image2]

Quando si aggiunge un avviso, assegnare un nome univoco, scegliere una soglia di spesa e gli indirizzi di posta elettronica a cui verrà inviato. Mentre si imposta la soglia, è possibile scegliere un valore per **Totale fattura** o **Credito monetari**o dall'elenco **Avviso per**. Se si specifica un totale fattura, viene inviato un avviso quando la spesa per la sottoscrizione supera la soglia. Se si specifica un credito monetario, viene inviato un avviso quando i crediti monetari scendono al di sotto del limite. I crediti monetari in genere si applicano alle versioni di valutazione gratuite e alle sottoscrizioni associate agli account MSDN.

![][Image3]

Azure supporta qualsiasi indirizzo di posta elettronica ma non ne verifica il corretto funzionamento, pertanto è necessario accertarsi che non siano presenti errori di digitazione.

## Controllare gli avvisi

Dopo aver impostato gli avvisi, nel centro account viene visualizzato un elenco degli avvisi già impostati ed è indicato il numero di avvisi aggiuntivi che è possibile ancora impostare. Per ogni avviso è possibile visualizzare la data e l'ora di invio, il tipo di avviso (per totale fattura o credito monetario) e il limite impostato. Il formato dell'ora è 24 ore UTC (Universal Time Coordinate) e il formato della data è aaaa-mm-gg. Fare clic sul segno più per modificare un avviso nell'elenco o sul simbolo del cestino per eliminarlo.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png

<!---HONumber=July15_HO3-->