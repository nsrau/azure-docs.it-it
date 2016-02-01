<properties 
	pageTitle="Precaricamento di risorse in un endpoint della rete CDN di Azure" 
	description="Informazioni su come precaricare il contenuto memorizzato nella cache in un endpoint della rete CDN." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>
	
# Precaricamento di risorse in un endpoint della rete CDN di Azure

Questa esercitazione illustra in modo dettagliato il precaricamento di contenuto memorizzato nella cache in tutti i nodi perimetrali della rete CDN di Azure.

## Procedura dettagliata

1. Nel [portale di Azure](http://portal.azure.com) passare al profilo di rete CDN contenente l'endpoint che si vuole precaricare. Viene visualizzato il pannello del profilo.

2. Fare clic sull'endpoint nell'elenco. Viene visualizzato il pannello dell'endpoint.

3. Nel pannello dell'endpoint della rete CDN fare clic sul pulsante Carica.
	
	![Pannello dell'endpoint della rete CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
	
	Viene visualizzato il pannello Carica.
	
	![Pannello di caricamento della rete CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
	
4. Immettere il percorso completo di ogni asset da caricare, ad esempio */pictures/kitten.png*, nella casella di testo **Percorso**.

	> [AZURE.TIP]Dopo l'immissione di testo verranno visualizzate altre caselle di testo **Percorso** che consentono di compilare un elenco di più asset. È possibile eliminare gli asset dall'elenco facendo clic sul pulsante con i puntini di sospensione (...).
	>
	> I percorsi devono essere URL relativi. L'asterisco (*) può essere usato come carattere jolly.
	
    ![Pulsante Carica](./media/cdn-preload-endpoint/cdn-load-paths.png)
    
5. Fare clic sul pulsante **Carica**.

	![Pulsante Carica](./media/cdn-preload-endpoint/cdn-load-button.png)
	

## Vedere anche
- [Ripulire un endpoint della rete CDN di Azure](cdn-purge-endpoint.md)
- [Riferimento API REST della rete CDN di Azure - Ripulire o precaricare un endpoint](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0121_2016-->