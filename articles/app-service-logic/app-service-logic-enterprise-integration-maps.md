<properties 
	pageTitle="Panoramica delle mappe di Enterprise Integration Pack | Servizio app di Microsoft Azure" 
	description="Informazioni su come usare le mappe con Enterprise Integration Pack e le app per la logica" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Informazioni sulle mappe ed Enterprise Integration Pack

## Panoramica
Enterprise Integration usa le mappe per trasformare i dati XML da un formato all'altro.

## Che cos'è una mappa?
Una mappa è un documento XML che definisce il modo in cui i dati di un documento devono essere trasformati in un altro formato.

## Perché usare le mappe?
Si supponga di ricevere regolarmente fatture o ordini B2B da un partner che usa il formato AAAMMGG per le date. In azienda però le date vengono archiviare nel formato MMGGAAA. È possibile usare una mappa per *trasformare* il formato di data AAAMMGG in MMGGAAA prima di archiviare i dettagli dell'ordine o della fattura nel database relativo.

## Come caricare una mappa?
Nel portale di Azure:
1. Selezionare **Esplora** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Integration Accounts** (Account di integrazione) dall'elenco dei risultati ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selezionare l'**account di integrazione** a cui aggiungere la mappa ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Selezionare il riquadro **Mappe** ![](./media/app-service-logic-enterprise-integration-maps/map-1.png)
5. Selezionare il pulsante **Aggiungi** nel pannello Mappe che si apre ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)
6. Immettere il **nome** per la mappa, quindi selezionare l'icona della cartella sul lato destro della casella di testo **Mappa** per caricare il file dello schema. Dopo aver completato il processo di caricamento, selezionare il pulsante **OK**. ![](./media/app-service-logic-enterprise-integration-maps/map-3.png)
7. La mappa viene aggiunta all'account di integrazione. Verrà visualizzata una notifica sullo schermo con l'avviso dell'esito positivo o negativo dell'aggiunta del file di mappa. Dopo aver ricevuto la notifica, selezionare il riquadro **Mappe** e verrà visualizzata la mappa appena aggiunta nel pannello Mappe: ![](./media/app-service-logic-enterprise-integration-maps/map-4.png)

## Come modificare una mappa?
Per modificare una mappa, è necessario caricare un nuovo file di mappa con le modifiche desiderate. È anche possibile scaricare la mappa e poi modificarla.

Per caricare una nuova mappa che sostituisce una mappa esistente, seguire questi passaggi:
1. Selezionare il riquadro **Mappe**
2. Quando si apre il pannello Mappe selezionare la mappa da modificare
3. Nel pannello **Mappe** selezionare il collegamento **Aggiorna** ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)
4. Selezionare il file di mappa da caricare usando la finestra selettore file che si apre e poi selezionare **Apri** nel selettore file ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)
5. Dopo il caricamento della mappa verrà visualizzato un popup di notifica.


## Come eliminare una mappa?
1. Selezionare il riquadro **Mappe**
2. Quando si apre il pannello Mappe selezionare la mappa da eliminare
3. Selezionare il collegamento **Elimina** ![](./media/app-service-logic-enterprise-integration-maps/delete.png)
4. Confermare che si intende eliminare la mappa. ![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)


## Passaggi successivi
- [Altre informazioni sui contratti](./app-service-logic-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")

<!---HONumber=AcomDC_0706_2016-->