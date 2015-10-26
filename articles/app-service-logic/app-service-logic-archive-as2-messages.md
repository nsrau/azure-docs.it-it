<properties 
   pageTitle="Archiviazione dei messaggi del connettore AS2 | Microsoft Azure App Service" 
   description="Come archiviare o memorizzare i messaggi del connettore AS2 in Azure App Service" 
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
   ms.date="08/23/2015"
   ms.author="rajram"/>


# Panoramica sull'archiviazione dei messaggi del connettore AS2

Il **connettore AS2** espone la possibilità di archiviare messaggi. L'archiviazione consente di archiviare il messaggio nel **contenitore BLOB di Azure** che fa parte delle impostazioni del pacchetto.

L'archiviazione è esposta in due punti per i messaggi e gli acknowledgement (MDN):

1. **Ricezione/decodifica del trigger**: il messaggio viene archiviato non appena viene ricevuto dall'istanza dell'app per le API 
2. **Azione di codifica/invio**: il messaggio codificato viene archiviato al termine di tutte le operazioni di elaborazione e immediatamente prima di essere inviato al partner 

## Procedura: Recuperare l'URL archiviato del messaggio

Passare all'istanza dell'app per le API relativa al connettore AS2 e fare clic su 'Rilevamento'. Limitare le informazioni di verifica mediante i parametri di filtro. Quando il messaggio è visibile, fare clic per visualizzarne i dettagli. L'URL archiviato per il messaggio verrà visualizzato in questa vista dettagliata: ![][1]

## Procedura: Recuperare il messaggio archiviato

Usare l'URL recuperato sopra per recuperare il messaggio archiviato dall'archiviazione BLOB di Azure.


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg
 

<!---HONumber=Oct15_HO3-->