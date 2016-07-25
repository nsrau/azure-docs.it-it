<properties 
	pageTitle="Panoramica degli schemi e di Enterprise Integration Pack | Servizio app di Microsoft Azure | Microsoft Azure" 
	description="Informazioni su come usare gli schemi con Enterprise Integration Pack e le app per la logica" 
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
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Informazioni sugli schemi ed Enterprise Integration Pack  

## Perché usare uno schema
Gli schemi vengono usati per verificare che i documenti XML ricevuti siano validi, ovvero che contengano i dati previsti in un formato predefinito.

## Procedura: Aggiungere uno schema
Nel portale di Azure:
1. Selezionare **Esplora** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Integration Accounts** (Account di integrazione) dall'elenco dei risultati ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selezionare l'**account di integrazione** a cui aggiungere lo schema ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Selezionare il riquadro **Schemas** (Schemi) ![](./media/app-service-logic-enterprise-integration-schemas/schema-1.png)
5. Selezionare il pulsante **Add** (Aggiungi) nel pannello Schemas (Schemi) che si apre ![](./media/app-service-logic-enterprise-integration-schemas/schema-2.png)
6. Immettere il **Name** (Nome) per lo schema, quindi selezionare l'icona della cartella sul lato destro della casella di testo **Schema** (Schema) per caricare il file dello schema. Dopo aver completato il processo di caricamento, selezionare il pulsante **OK**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-3.png)
7. Selezionare l'icona di notifica a forma di *campana* per visualizzare lo stato di avanzamento del processo di caricamento dello schema. ![](./media/app-service-logic-enterprise-integration-schemas/schema-4.png)
8. Selezionare il riquadro **Schemas** (Schemi). Questa operazione aggiorna il riquadro e consente la visualizzazione del numero progressivo dello schema, che indica che il nuovo schema è stato aggiunto correttamente. Dopo aver selezionato il riquadro **Schemas** (Schemi), anche lo schema appena aggiunto viene visualizzato nel pannello Schemas (Schemi) sulla destra. ![](./media/app-service-logic-enterprise-integration-schemas/schema-5.png)


## Procedura: Usare gli schemi
- Gli schemi vengono usati per convalidare i messaggi scambiati in uno scenario B2B.

## Procedura: Modificare gli schemi
1. Selezionare il riquadro **Schemas** (Schemi)
2. Selezionare lo schema che si desidera modificare dal pannello Schemas (Schemi) che si apre
3. Selezionare il collegamento **Upload** (Carica) nel pannello Schemas (schemi) ![](./media/app-service-logic-enterprise-integration-schemas/edit-1.png)
4. Selezionare il file dello schema da caricare usando la finestra di dialogo di selezione dei file che si apre.
5. Selezionare **Apri** nel selettore di file ![](./media/app-service-logic-enterprise-integration-schemas/edit-2.png)
6. Si riceve una notifica che indica l'esito positivo del caricamento ![](./media/app-service-logic-enterprise-integration-schemas/edit-3.png)

## Procedura: Eliminare gli schemi
1. Selezionare il riquadro **Schemas** (Schemi)
2. Selezionare lo schema da eliminare dal pannello Schemi che si apre
3. Selezionare il collegamento **Elimina** dalla barra dei menu del pannello Schemi ![](./media/app-service-logic-enterprise-integration-schemas/delete-1.png)
4. Se si desidera eliminare lo schema selezionato, scegliere **Sì** nella finestra di dialogo Delete schema (Elimina schema) per confermare la scelta ![](./media/app-service-logic-enterprise-integration-schemas/delete-2.png)
5. Si noti infine che l'elenco degli schemi nel pannello Schemi si aggiorna e lo schema eliminato non viene più visualizzato ![](./media/app-service-logic-enterprise-integration-schemas/delete-3.png)

## Passaggi successivi

- [Altre informazioni su Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")

      

<!---HONumber=AcomDC_0713_2016-->