<properties 
	pageTitle="Panoramica degli schemi e di Enterprise Integration Pack | Servizio app di Microsoft Azure | Microsoft Azure" 
	description="Informazioni su come usare gli schemi con Enterprise Integration Pack e le app per la logica" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/> 

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2016" 
	ms.author="deonhe"/>

# Informazioni sugli schemi ed Enterprise Integration Pack  

## Perché usare uno schema
Gli schemi vengono usati per verificare che i documenti XML ricevuti siano validi, ovvero che contengano i dati previsti in un formato predefinito.

## Procedura: Aggiungere uno schema
Nel portale di Azure:

1. Selezionare **Altri servizi** ![](./media/app-service-logic-enterprise-integration-overview/overview-11.png)
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** dall'elenco dei risultati ![](./media/app-service-logic-enterprise-integration-overview/overview-21.png)
3. Selezionare l'**account di integrazione** a cui aggiungere lo schema ![](./media/app-service-logic-enterprise-integration-overview/overview-31.png)
4. Selezionare il riquadro **Schemi** ![](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)

#### Per aggiungere un file di schema di dimensione minore di 2 MB  

5. Selezionare il pulsante **Aggiungi** nel pannello Schemi visualizzato ![](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)
6. Immettere il **Name** (Nome) per lo schema, quindi selezionare l'icona della cartella sul lato destro della casella di testo **Schema** per caricare il file dello schema. Dopo aver completato il processo di caricamento, selezionare il pulsante **OK**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)

#### Per aggiungere file di schema di dimensione maggiore di 2 MB (massimo 8 MB)  

7. Se il livello di accesso della sicurezza del BLOB è **Pubblico** ![](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)

  * Caricare lo schema nella risorsa di archiviazione e copiare l'URI ![](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)

  * Selezionare file di grandi dimensioni in Aggiungi schema e specificare l'URI in URI del contenuto ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)

8. Se il livello di accesso della sicurezza del BLOB è **Nessun accesso anonimo** ![](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)

  * Caricare lo schema nella risorsa di archiviazione ![](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

  * Generare una firma di accesso condiviso per lo schema ![](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

  * Selezionare file di grandi dimensioni in Aggiungi schema e specificare l'URI della firma di accesso condiviso in URI del contenuto ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)

9. Dovrebbe essere visualizzato lo schema appena aggiunto ![](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## Procedura: Usare gli schemi
- Gli schemi vengono usati per convalidare i messaggi scambiati in uno scenario B2B.

## Come modificare gli schemi
1. Selezionare il riquadro **Schemi**
2. Selezionare lo schema che si desidera modificare dal pannello Schemi che si apre
3. Selezionare il collegamento **Carica** nel pannello Schemi ![](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)
4. Selezionare il file dello schema da caricare usando la finestra di dialogo di selezione dei file che si apre.
5. Selezionare **Apri** nel selettore di file ![](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)
6. Si riceve una notifica che indica l'esito positivo del caricamento

## Procedura: Eliminare gli schemi
1. Selezionare il riquadro **Schemi**
2. Selezionare lo schema da eliminare dal pannello Schemi che si apre
3. Selezionare il collegamento **Elimina** dalla barra dei menu del pannello Schemi ![](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)
4. Se si vuole eliminare lo schema selezionato, scegliere **Sì** nella finestra di dialogo Elimina schema per confermare la scelta ![](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)
5. Si noti infine che l'elenco degli schemi nel pannello Schemi si aggiorna e lo schema eliminato non viene più visualizzato ![](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)

## Passaggi successivi

- [Altre informazioni su Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")

<!---HONumber=AcomDC_0921_2016-->