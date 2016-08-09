<properties 
   pageTitle="Tracciare i messaggi B2B nelle app per la logica in Servizio app di Azure | Microsoft Azure" 
   description="Questo argomento illustra il rilevamento dell'elaborazione B2B" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>


# Tracciare i messaggi B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

## Informazioni sul rilevamento B2B
La comunicazione B2B implica l'elaborazione dei messaggi tra due partner commerciali. Le relazioni sono definite come contratti tra due partner commerciali. Dopo aver stabilito la comunicazione, sarà necessario ricorrere a un metodo per monitorarne il corretto funzionamento.

È stato implementato il rilevamento dei messaggi per i seguenti scenari B2B: AS2, EDIFACT e X12.

## AS2
Dopo aver creato un'istanza di un'app per le API AS2, passare a tale istanza e selezionare **Rilevamento**. In quest'area è possibile visualizzare e filtrare tutte le informazioni di rilevamento di AS2.

![][1]

## EDIFACT
Dopo aver creato un'istanza di un'app per le API EDIFACT, passare a tale istanza e selezionare **Rilevamento**. In quest'area è possibile visualizzare e filtrare tutte le informazioni di rilevamento di EDIFACT. È inoltre possibile consultare i dati a livello di interscambio, di gruppo e di set di transazioni, tutti in un'unica visualizzazione.

Se vengono creati dei batch nell'ambito dei contratti EDIFACT all'interno dell'app per le API Gestione dei partner commerciali associata, nella sezione Invio in batch verranno elencati tutti i batch. È possibile selezionare un batch per visualizzare il messaggio attivo (se presente) e le informazioni relative al completamento.

![][2]

## X12
Dopo aver creato un'istanza di un'app per le API X12, passare a tale istanza e selezionare **Rilevamento**. In quest'area è possibile visualizzare e filtrare tutte le informazioni di rilevamento di X12. È inoltre possibile consultare i dati a livello di interscambio, di gruppo e di set di transazioni, tutti in un'unica visualizzazione.

Se vengono creati dei batch nell'ambito dei contratti X12 all'interno dell'app per le API Gestione dei partner commerciali associata, nella sezione Invio in batch verranno elencati tutti i batch. È possibile selezionare un batch per visualizzare il messaggio attivo (se presente) e le informazioni per i batch completati.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.png
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.png

<!---HONumber=AcomDC_0727_2016-->