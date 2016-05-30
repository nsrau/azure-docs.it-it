<properties
	pageTitle="Rete CDN - Controllo del comportamento di memorizzazione nella cache delle richieste con le stringhe di query - Premium"
	description="La memorizzazione nella cache della stringa di query della rete CDN controlla come i file devono essere memorizzati nella cache quando contengono stringhe di query."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="casoper"/>

#Controllo del comportamento di memorizzazione nella cache delle richieste della rete CDN con le stringhe di query - Premium

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Rete CDN Premium di Azure fornita da Verizon](cdn-query-string-premium.md)

##Panoramica

La memorizzazione nella cache della stringa di query controlla come i file devono essere memorizzati nella cache quando contengono stringhe di query.

> [AZURE.IMPORTANT] I prodotti della rete CDN Standard e Premium forniscono la stessa funzionalità di memorizzazione nella cache delle stringhe di query, ma l'interfaccia utente è diversa. Questo documento descrive l'interfaccia della **rete CDN Premium di Azure fornita da Verizon**. Per informazioni sulla memorizzazione nella cache di stringhe di query con la **rete CDN Standard di Azure fornita da Akamai** e la **rete CDN Standard di Azure fornita da Verizon**, vedere l'articolo [Controllo del comportamento di memorizzazione nella cache delle richieste della rete CDN con le stringhe di query](cdn-query-string.md).

Sono disponibili tre modalità:

- **standard-cache**: si tratta della modalità predefinita. Il nodo edge della rete CDN passerà la stringa di query dal richiedente all’origine alla prima richiesta ed eseguirà la memorizzazione nella cache dell’asset. Tutte le richieste successive per quell’asset che vengono presentate dal nodo edge ignoreranno la stringa di query fino a quando l’asset memorizzato nella cache non sarà scaduto.
- **no-cache**: in questa modalità, le richieste con stringhe di query non vengono memorizzate nella cache in corrispondenza del nodo edge della rete CDN. Il nodo edge recupera l'asset direttamente dall'origine e lo passa al richiedente ad ogni richiesta.
- **unique-cache**: questa modalità considera ogni richiesta con una stringa di query come un asset univoco con la propria memorizzazione nella cache. Ad esempio, la risposta dall'origine per una richiesta di *foo.ashx?q=bar* verrebbe memorizzata nella cache in corrispondenza del nodo edge e restituita per le successive memorizzazione nella cache con quella stessa stringa di query. Una richiesta di *foo.ashx?q=somethingelse* verrebbe memorizzata nella cache come asset separato con il proprio time to live.

##Modifica delle impostazioni di memorizzazione nella cache della stringa di query per i profili premium della rete CDN

1. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci**.

	![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)

	Si aprirà il portale di gestione della rete CDN.

2. Passare il puntatore sulla scheda **HTTP Grande**, quindi passare il puntatore sul riquadro a comparsa **Impostazioni della memorizzazione nella cache**. Fare clic su **Memorizzazione nella cache della stringa di Query**.

	Vengono visualizzate le opzioni di memorizzazione nella cache della stringa di query.

	![Opzioni della memorizzazione nella cache della stringa di query della rete CDN](./media/cdn-query-string-premium/cdn-query-string.png)

3. Una volta effettuate le selezioni, fare clic sul pulsante **Aggiorna**.


> [AZURE.IMPORTANT] Le modifiche delle impostazioni non sono immediatamente visibili, perché la propagazione della registrazione nella rete CDN richiede tempo. Per i profili della <b>rete CDN di Azure fornita da Verizon</b> la propagazione in genere viene completata entro 90 minuti, ma in alcuni casi può richiedere più tempo.

<!---HONumber=AcomDC_0518_2016-->