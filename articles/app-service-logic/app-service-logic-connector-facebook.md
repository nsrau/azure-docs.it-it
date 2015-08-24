<properties
   pageTitle="Utilizzando il connettore Facebook nell'applicazione per la logica nel servizio App di Azure"
   description="Come utilizzare il connettore Facebook in un'applicazione per la logica"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/10/2015"
   ms.author="andalmia"/>


# Connettore Facebook

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

- Il trigger del connettore Facebook recupererà i nuovi post nella sequenza temporale utente o i nuovi post nella pagina. Quando un nuovo tweet viene recuperato, attiva una nuova istanza del flusso e passa i dati ricevuti nella richiesta al flusso per l'elaborazione.
- Le azioni del connettore Facebook consentono di pubblicare un post, pubblicare una foto e così via. Queste azioni ricevono una risposta e la rendono disponibile per le azioni nel flusso per l'utilizzo.

## Trigger e azioni

Trigger | Azioni
--- | ---
<ul><li>Nuovo Post nella sequenza temporale utente</li><li>Nuovo Post nella pagina</li></ul> | <ul><li>Pubblicare Post</li><li>Pubblicare foto</li></ul>



## Creazione di un connettore Facebook per l'app per la logica
Per usare il connettore Facebook, prima è necessario creare un'istanza dell'app per le API Facebook Connector. Attenersi alla procedura riportata di seguito:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Connettore Facebook", selezionarla e scegliere **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà: ![][1]
4.	Selezionare **Crea**.


## Uso del connettore Facebook nell'app per la logica
Una volta creata l'app per le API, è possibile usare il connettore Facebook come trigger/azione per l'app per la logica. A questo scopo, è necessario:

1.	Nell’app per la logica, aprire **Trigger e azioni** per aprire la finestra di progettazione delle app per la logica e configurare il flusso: ![][3]
2.	Il connettore Facebook è elencato nella raccolta:![][4]
3. Selezionare il connettore Facebook da aggiungere automaticamente alla finestra di progettazione. Selezionare **Authorize**, immettere le credenziali e selezionare **Allow**:![][5] ![][6] ![][7] ![][8]
4.	Selezionare un trigger. ![][9]

Ora è possibile usare i post recuperati dal trigger Facebook nelle altre azioni. Nel flusso seguente, quando un post viene pubblicato nella sequenza temporale di Facebook dell'utente, lo stesso post viene inviato alla sequenza temporale di Twitter dell'utente: ![][10]

In modo simile, è possibile creare flussi usando le azioni del connettore Facebook. Il flusso seguente recupererà un nuovo messaggio pubblicato nel gruppo Yammer e pubblicherà lo stesso post nella pagina Facebook gestita dall'utente: ![][11]

> [AZURE.TIP]Per ottenere l'ID della pagina di Facebook o l'ID del gruppo di Yammer, cercare il codice numerico nell'URL.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Creare app per le API con le API REST. Vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!---HONumber=August15_HO7-->