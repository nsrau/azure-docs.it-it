<properties 
	pageTitle="Informazioni sui partner e su Enterprise Integration Pack | Servizio app di Microsoft Azure | Microsoft Azure" 
	description="Informazioni su come usare i partner con Enterprise Integration Pack e le app per la logica" 
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
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Informazioni sui partner ed Enterprise Integration Pack

## Panoramica
Prima di poter creare un partner, l'utente e l'organizzazione con cui si intende intrattenere attività commerciali devono condividere informazioni che consentiranno di identificare e convalidare i messaggi scambiati. Dopo avere completato questa procedura, è possibile avviare le relazioni aziendali e creare un *partner* nell'account di integrazione.

## Informazioni sui partner
I partner sono le entità che partecipano alle transazioni e ai messaggi Business-To-Business (B2B).

## Uso dei partner
I partner vengono usati per creare i contratti. Un contratto definisce i dettagli relativi ai messaggi che verranno scambiati tra i partner.

Prima di creare un contratto è necessario aggiungere almeno due partner all'account di integrazione. Uno dei partner del contratto deve essere l'organizzazione dell'utente. Il partner che rappresenta l'organizzazione è definito come **partner host**. Il secondo partner rappresenta l'organizzazione con cui l'organizzazione dell'utente scambia messaggi. Il secondo partner viene definito **partner guest**. Il partner guest può essere un'altra società o anche un reparto all'interno della stessa organizzazione dell'utente.

Dopo aver aggiunto i partner, usarli per creare un contratto.

Le impostazioni per la ricezione e l'invio sono configurate dal punto di vista del partner host. Le impostazioni di ricezione del contratto, ad esempio, determinano la modalità con cui il partner host riceve i messaggi inviati da un partner guest. Analogamente, le impostazioni di invio del contratto indicano in che modo il partner host invia messaggi al partner guest.

## Procedura: Creare un partner
Nel portale di Azure:
1. Selezionare **Esplora** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** dall'elenco dei risultati ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selezionare l'**account di integrazione** a cui aggiungere i partner ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Selezionare il riquadro **Partner** ![](./media/app-service-logic-enterprise-integration-partners/partner-1.png)
5. Selezionare il pulsante **Add** (Aggiungi) nel pannello Partner che si apre ![](./media/app-service-logic-enterprise-integration-partners/partner-2.png)
6. Immettere il **Name** (Nome), quindi selezionare **Qualificatore** e immettere il **Value** (Valore). Il valore viene usato per identificare i documenti ricevuti dalle app. ![](./media/app-service-logic-enterprise-integration-partners/partner-3.png)
7. Selezionare l'icona di notifica a forma di *campana* per visualizzare lo stato di avanzamento del processo di creazione del partner. ![](./media/app-service-logic-enterprise-integration-partners/partner-4.png)
8. Selezionare il riquadro **Partner**. Questa operazione aggiorna il riquadro e consente la visualizzazione del numero progressivo del partner, che indica che il nuovo partner è stato aggiunto correttamente. ![](./media/app-service-logic-enterprise-integration-partners/partner-5.png)
10. Dopo aver selezionato il riquadro Partner, anche il partner appena aggiunto viene visualizzato nel pannello. ![](./media/app-service-logic-enterprise-integration-partners/partner-6.png)

## Procedura: Come modificare un partner

Seguire la procedura in basso per modificare un partner già esistente nell'account di integrazione:
1. Selezionare il riquadro **Partner**
2. Selezionare il partner che si desidera modificare quando si apre il pannello Partner
3. Nel riquadro **Aggiorna partner** apportare le modifiche necessarie
4. Se si è soddisfatti delle modifiche, selezionare il collegamento **Save** (Salva) o, in caso contrario, scegliere il collegamento **Discard** (Ignora) per eliminare le modifiche. ![](./media/app-service-logic-enterprise-integration-partners/edit-1.png)

## Procedura: Eliminare un partner
1. Selezionare il riquadro **Partner**
2. Selezionare il partner che si desidera modificare quando si apre il pannello Partner
3. Selezionare il collegamento **Elimina** ![](./media/app-service-logic-enterprise-integration-partners/delete-1.png)

## Passaggi successivi
- [Altre informazioni sui contratti](./app-service-logic-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")

<!---HONumber=AcomDC_0803_2016-->