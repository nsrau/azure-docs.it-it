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
   ms.date="07/01/2015"
   ms.author="rajram"/>


# Tracciare i messaggi B2B

## Informazioni sul rilevamento B2B
La comunicazione B2B implica l'elaborazione dei messaggi tra due partner commerciali. Le relazioni sono definite come contratti tra due partner commerciali. Una volta stabilita la comunicazione, sarà necessario ricorrere a un metodo per monitorarne il corretto funzionamento. Relativamente all'introduzione delle app per le API B2B nei Servizi app di Azure, è stato abilitato il rilevamento dei dati, anche attraverso il portale di Azure.

## AS2
Dopo aver creato un'istanza dell'app per le API AS2, passare alla sezione Rilevamento dell'istanza. Qui sarà possibile visualizzare tutte le informazioni di rilevamento AS2 e anche filtrarle tramite i pannelli di filtro definiti.

![][1]

## EDIFACT
Dopo aver creato un'istanza dell'app per le API EDIFACT, passare alla sezione Rilevamento dell'istanza. Qui sarà possibile visualizzare tutte le informazioni di rilevamento EDIFACT e anche filtrarle tramite i filtri definiti. Sarà anche possibile visualizzare i dati a livello di interscambio, i dati a livello di gruppo e i dati a livello del set di transazioni.

Se sono stati creati dei batch come parte dei contratti EDIFACT nell'app per le API Gestione dei partner commerciali associata, nella parte Invio in batch verranno elencati tutti i batch. Sarà quindi possibile accedere a un batch per visualizzare l'eventuale messaggio attivo, ma anche alle informazioni relative ai batch completati in passato.

![][2]

## X12
Dopo aver creato un'istanza dell'app per le API X12, passare alla sezione Rilevamento dell'istanza. Qui sarà possibile visualizzare tutte le informazioni di rilevamento X12 e anche filtrarle tramite i filtri definiti. Sarà anche possibile visualizzare i dati a livello di interscambio, i dati a livello di gruppo e i dati a livello del set di transazioni.

Se sono stati creati dei batch come parte dei contratti X12 nell'app per le API Gestione dei partner commerciali associata, nella parte Invio in batch verranno elencati tutti i batch. Sarà quindi possibile accedere a un batch per visualizzare l'eventuale messaggio attivo, ma anche alle informazioni relative ai batch completati in passato.

X12 e EDIFACT hanno visualizzazioni di rilevamento simili.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!---HONumber=July15_HO3-->