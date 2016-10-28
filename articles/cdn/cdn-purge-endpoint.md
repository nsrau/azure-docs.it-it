<properties
	pageTitle="Ripulire un endpoint della rete CDN di Azure | Microsoft Azure"
	description="Informazioni su come ripulire tutto il contenuto memorizzato nella cache da un endpoint della rete CDN."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>

# Ripulire un endpoint della rete CDN di Azure

## Panoramica

I nodi perimetrali della rete CDN di Azure memorizzeranno nella cache gli asset fino alla scadenza della durata TTL dell'asset. Dopo la scadenza della durata TTL dell'asset, quando un client richiede l'asset dal nodo periodico, questo recupera una nuova copia aggiornata dell'asset per soddisfare la richiesta del client e aggiornare la cache.

A volte si desidera ripulire il contenuto memorizzato nella cache da tutti i nodi periodici e forzare il recupero dei nuovi asset aggiornati. Ciò potrebbe essere dovuto agli aggiornamenti all'applicazione Web o a un aggiornamento rapido di asset che contengono informazioni non corrette.

> [AZURE.TIP] Solo la cancellazione svuota il contenuto della cache sui server perimetrali della rete CDN. Tutte le cache downstream, ad esempio le cache dei server proxy e del browser locale, possono comunque mantenere una copia del file nella cache. È importante tenerlo presente quando si imposta la durata di un file. È possibile forzare un client downstream per richiedere la versione più recente del file assegnandogli un nome univoco ogni volta che viene aggiornato o sfruttando la [memorizzazione nella cache delle stringhe di query](cdn-query-string.md).

Questa esercitazione illustra l'eliminazione dagli asset di tutti i nodi periodici di un endpoint.

## Procedura dettagliata

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo di rete CDN contenente l'endpoint che si desidera ripulire.

2. Nel pannello relativo al profilo di rete CDN fare clic sul pulsante di eliminazione.

	![Pannello del profilo di rete CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)

	Viene visualizzato il pannello di eliminazione.

	![Pannello di eliminazione della rete CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. Nel pannello di eliminazione selezionare l'indirizzo del servizio che si desidera ripulire dall'elenco a discesa degli URL.

	![Maschera di eliminazione](./media/cdn-purge-endpoint/cdn-purge-form.png)

	> [AZURE.NOTE] È possibile visualizzare il pannello di eliminazione anche facendo clic sul pulsante **Elimina** nel pannello dell'endpoint della rete CDN. In tal caso, il campo **URL** sarà prepopolato con l'indirizzo del servizio dell'endpoint specifico.

4. Selezionare gli asset che si desidera ripulire dai nodi periferici. Se si desidera ripulire tutti gli asset, fare clic sulla casella di controllo **Elimina tutto**. In alternativa digitare il percorso completo di ogni asset che si vuole ripulire, ad esempio `/pictures/kitten.png`, nella casella di testo **Percorso**.

	> [AZURE.TIP] Dopo l'immissione di testo verranno visualizzate altre caselle di testo **Percorso** che consentono di compilare un elenco di più asset. È possibile eliminare gli asset dall'elenco facendo clic sul pulsante con i puntini di sospensione (...).
	>
	> I percorsi devono essere un URL relativo che soddisfi la seguente [espressione regolare](https://msdn.microsoft.com/library/az24scfc.aspx): `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)**$";`. Per la **rete CDN di Azure fornita da Verizon** (Standard e Premium), si può usare l'asterisco (*) come carattere jolly, ad esempio `/music/*`. I caratteri jolly e la funzione **Elimina tutti** non sono ammessi con la **rete CDN di Azure fornita da Akamai**.
	
5. Fare clic sul pulsante **Elimina**.

	![Pulsante di eliminazione](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] L'elaborazione delle richieste di eliminazione dura circa 2-3 minuti per l'elaborazione con la **rete CDN di Azure fornita da Verizon** (Standard e Premium) e circa 7 minuti con la **rete CDN di Azure fornita da Akamai**. La rete CDN di Azure ha un limite di 50 richieste di eliminazione simultanee in qualsiasi momento.

## Vedere anche
- [Precaricamento di risorse in un endpoint della rete CDN di Azure](cdn-preload-endpoint.md)
- [Riferimento API REST della rete CDN di Azure - Ripulire o precaricare un endpoint](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0803_2016-->