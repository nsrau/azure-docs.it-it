<properties
	pageTitle="Precaricare asset in un endpoint della rete CDN di Azure | Microsoft Azure"
	description="Informazioni su come precaricare il contenuto memorizzato nella cache in un endpoint della rete CDN."
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

# Precaricamento di risorse in un endpoint della rete CDN di Azure

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Per impostazione predefinita, gli asset vengono prima di tutto memorizzati nella cache quando vengono richiesti. Questo significa che la prima richiesta da ogni area potrebbe richiedere più tempo, poiché i server perimetrali non avranno il contenuto memorizzato nella cache e la richiesta dovrà essere inoltrata al server di origine. Il precaricamento del contenuto consente di evitare questa latenza della prima richiesta.

Oltre a fornire una migliore esperienza utente, il precaricamento di asset memorizzati nella cache può inoltre ridurre il traffico di rete sul server di origine.

> [AZURE.NOTE] Il precaricamento degli asset è utile per eventi di grandi dimensioni o il contenuto che diventa disponibile contemporaneamente per un numero elevato di utenti, ad esempio la nuova versione di un film o un aggiornamento software.

Questa esercitazione illustra in modo dettagliato il precaricamento di contenuto memorizzato nella cache in tutti i nodi perimetrali della rete CDN di Azure.

## Procedura dettagliata

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo di rete CDN contenente l'endpoint che si vuole precaricare. Viene visualizzato il pannello del profilo.

2. Fare clic sull'endpoint nell'elenco. Viene visualizzato il pannello dell'endpoint.

3. Nel pannello dell'endpoint della rete CDN fare clic sul pulsante Carica.

	![Pannello dell'endpoint della rete CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)

	Viene visualizzato il pannello Carica.

	![Pannello di caricamento della rete CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)

4. Immettere il percorso completo di ogni asset da caricare, ad esempio `/pictures/kitten.png`, nella casella di testo **Percorso**.

	> [AZURE.TIP] Dopo l'immissione di testo verranno visualizzate altre caselle di testo **Percorso** che consentono di compilare un elenco di più asset. È possibile eliminare gli asset dall'elenco facendo clic sul pulsante con i puntini di sospensione (...).
	>
	> I percorsi devono essere un URL relativo che soddisfi la seguente [espressione regolare](https://msdn.microsoft.com/library/az24scfc.aspx): `^(?:\/[a-zA-Z0-9-_.\u0020]+)+$`. Ogni asset deve avere il proprio percorso. Non esiste alcuna funzionalità con caratteri jolly per il pre-caricamento degli asset.

    ![Pulsante Carica](./media/cdn-preload-endpoint/cdn-load-paths.png)

5. Fare clic sul pulsante **Carica**.

	![Pulsante Carica](./media/cdn-preload-endpoint/cdn-load-button.png)

> [AZURE.NOTE] Le richieste di caricamento sono limitate a un massimo di 10 al minuto per ogni profilo di rete CDN.

## Vedere anche
- [Ripulire un endpoint della rete CDN di Azure](cdn-purge-endpoint.md)
- [Riferimento API REST della rete CDN di Azure - Ripulire o precaricare un endpoint](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0803_2016-->