<properties 
	pageTitle="Panoramica dei partner ed Enterprise Integration Pack | Servizio app di Microsoft Azure" 
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
	ms.date="06/29/2016" 
	ms.author="deonhe"/>  


# Informazioni sui contratti ed Enterprise Integration Pack

## Overview
I contratti sono il fulcro delle comunicazioni Business to Business (B2B) e consentono alle entità aziendali di comunicare in modo ottimale mediante i protocolli standard di settore.

## Cos'è un contratto?

Un contratto, per quanto riguarda Enterprise Integration Pack, è un contratto di comunicazione fra partner commerciali B2B. Un contratto si basa sulla comunicazione che i partner vogliono realizzare ed è specifico del protocollo o del trasporto.

Enterprise Integration supporta tre standard di protocollo/trasporto:

- [AS2](./app-service-logic-enterprise-integration-as2.md)
- [X12](./app-service-logic-enterprise-integration-x12.md)
- [EDIFACT](./app-service-logic-enterprise-integration-edifact.md)

## Perché usare i contratti
Alcuni vantaggi comuni dell'uso dei contratti sono:
- Consentono alle aziende di scambiare informazioni in un formato noto.
- Migliorano l'efficienza delle transazioni B2B
- È facile crearli, gestirli e usarli quando si creano app di integrazione aziendali

## Come creare contratti
- [Creare un contratto AS2](./app-service-logic-enterprise-integration-as2.md)
- [Creare un contratto X12](./app-service-logic-enterprise-integration-x12.md)

## Come usare un contratto
Dopo aver creato un contratto, è possibile usarlo mediante il portale di Azure per creare [app per la logica](./app-service-logic-what-are-logic-apps.md "Informazioni sulle app per la logica") con funzionalità B2B.

## Come modificare un contratto
È possibile modificare qualsiasi contratto seguendo questa procedura:
1. Selezionare l'account di integrazione che contiene il contratto da modificare.
2. Selezionare il riquadro **Agreements** (Contratti)
3. Selezionare il contratto da modificare nel pannello **Agreements** (Contratti)
4. Selezionare **Modifica** dal menu sopra
5. Nel menu Modifica che si apre eseguire le modifiche desiderate e poi selezionare il pulsante **OK** per salvarle

## Come eliminare un contratto
È possibile eliminare un contratto seguendo questa procedura all'interno dell'account di integrazione che contiene il contratto da eliminare:
1. Selezionare il riquadro **Agreements** (Contratti)
2. Selezionare il contratto da eliminare nel pannello **Agreements** (Contratti)
3. Selezionare **Elimina** dal menu sopra
4. Confermare che si desideri eliminare il contratto
5. Si noti che il contratto non è più visualizzato nel pannello Agreements (Contratti)
 

## Passaggi successivi
- [Creare un contratto AS2](./app-service-logic-enterprise-integration-as2.md)

<!---HONumber=AcomDC_0810_2016-->