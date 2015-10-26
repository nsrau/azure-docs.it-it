<properties 
   pageTitle="Tracciare i messaggi B2B" 
   description="Questo argomento illustra il rilevamento dell'elaborazione B2B" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/01/2015"
   ms.author="rajram"/>


# Tracciare i messaggi B2B

## Informazioni sul rilevamento B2B
La comunicazione B2B implica l'elaborazione dei messaggi tra due partner commerciali. Le relazioni sono definite come contratti tra due partner commerciali. Dopo aver stabilito la comunicazione, sarà necessario ricorrere a un metodo per monitorarne il corretto funzionamento.

È stato implementato il rilevamento dei messaggi per i seguenti scenari B2B: AS2, EDIFACT e X12.

## AS2
Dopo aver creato un'istanza dell'app per le API AS2, passare a tale istanza e selezionare Rilevamento. In quest'area è possibile visualizzare e filtrare tutte le informazioni di rilevamento di AS2.

![][1]

## EDIFACT
Dopo aver creato un'istanza dell'app per le API EDIFACT, passare a tale istanza e selezionare Rilevamento. In quest'area è possibile visualizzare e filtrare tutte le informazioni di rilevamento di EDIFACT. È inoltre possibile visualizzare i dati a livello di interscambio, di gruppo e di set di transazioni.

Se vengono creati dei batch come parte dei contratti EDIFACT nell'app per le API Gestione dei partner commerciali associata, nella sezione Invio in batch verranno elencati tutti i batch. È possibile selezionare un batch per visualizzare il messaggio attivo (se presente) e anche le informazioni per il completamento.

![][2]

## X12
Dopo aver creato un'istanza dell'app per le API X12, passare a tale istanza e selezionare Rilevamento. In quest'area è possibile visualizzare e filtrare tutte le informazioni di rilevamento di X12. È inoltre possibile visualizzare i dati a livello di interscambio, di gruppo e di set di transazioni.

Se vengono creati dei batch come parte dei contratti X12 nell'app per le API Gestione dei partner commerciali associata, nella parte Invio in batch verranno elencati tutti i batch. È possibile selezionare un batch per visualizzare il messaggio attivo (se presente) e le informazioni per i batch completati.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!---HONumber=Oct15_HO3-->