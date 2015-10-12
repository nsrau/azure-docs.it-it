<properties
   pageTitle="Uso del connettore HDInsight nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore HDInsight e usarlo in un'app per la logica in Azure App Service"
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
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# Uso del connettore HDInsight e aggiunta all'app per la logica
Il connettore HDInsight consente di creare un cluster Hadoop in Azure e di inviare diversi processi Hadoop, ad esempio Hive, Pig, MapReduce e Streaming MapReduce. Il servizio Azure HDInsight distribuisce ed esegue il provisioning dei cluster Apache Hadoop nel cloud, offrendo un framework software progettato per gestire, analizzare e creare report sui Big Data. La memoria centrale di Hadoop fornisce un'archiviazione dei dati affidabile grazie ad Hadoop Distributed File System (HDFS), nonché un modello di programmazione semplice, MapReduce, per elaborare e analizzare in parallelo i dati archiviati nel sistema distribuito. Con il connettore HDInsight è possibile creare o eliminare un cluster, inviare un processo e attenderne il completamento.

I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un flusso. È possibile aggiungere il connettore HDInsight al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

### Azioni di base

- Creazione di un cluster
- Attesa della creazione di un cluster
- Invio di un processo Pig
- Invio di un processo Hive
- Invio di un processo MapReduce
- Attesa del completamento di un processo
- Eliminazione di un cluster


## Creare l'app per le API del connettore HDInsight ##

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "HDInsight Connector", selezionarlo e fare clic su **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà.
4. Nelle impostazioni del pacchetto immettere il nome utente e la password del cluster HDInsight. Selezionare **OK**.
5. Selezionare **Create**: ![][1]  

## Configurazione dei certificati (facoltativo) ##

> [AZURE.NOTE]questo passaggio è necessario solo se si desidera eseguire operazioni di gestione (creazione ed eliminazione di cluster) nell'app per la logica.

Passare all'app per le API del connettore HDInsight appena creata. Nel componente 'Sicurezza' è visualizzato il valore 0, a indicare che non è stato caricato alcun certificato di gestione: ![][2]

Per caricare il certificato di gestione nell'app per le API:

1. Selezionare il componente 'Sicurezza'.
2. Nel pannello 'Sicurezza' selezionare **CARICA CERTIFICATO**.
3. Individuare e selezionare il file del certificato nel pannello successivo.
4. Dopo aver selezionato il certificato, fare clic su **OK**.

Dopo che il certificato è stato caricato, ne vengono visualizzati i dettagli: ![][3]

> [AZURE.NOTE]Se si vuole cambiare il certificato, è possibile caricarne un altro che sostituirà quello esistente.

## Uso del connettore in un'app per la logica ##

Il connettore HDInsight può essere usato solo come azione in un'app per la logica. In questa sezione viene descritta una semplice app per la logica che crea un cluster, esegue un processo 'Hive' ed elimina il cluster dopo il completamento del processo.


1. Nella scheda 'Start logic' fare clic su 'Run this logic manually'.
2. Selezionare l'app per le API del connettore HDInsight creata dalla raccolta. Vengono elencate le azioni disponibili: ![][5]

3. Selezionare 'Create cluster', specificare tutti i parametri del cluster necessari e selezionare ✓: ![][6]

4. L'azione ora viene visualizzata come configurata nell'app per la logica. Vengono visualizzati gli output dell'azione, che possono essere usati come input in azioni successive: ![][7]

5. Selezionare lo stesso connettore HDInsight dalla raccolta come azione. Selezionare l'azione 'Wait For Cluster Creation', specificare tutti i parametri necessari e selezionare ✓: ![][8]

6. Selezionare lo stesso connettore HDInsight dalla raccolta come azione. Selezionare l'azione 'Submit Hive Job', specificare tutti i parametri necessari e selezionare ✓: ![][9]

7. Selezionare lo stesso connettore HDInsight dalla raccolta come azione. Selezionare l'azione 'Wait For Job Completion', specificare tutti i parametri necessari e selezionare ✓: ![][10]

8. Selezionare lo stesso connettore HDInsight dalla raccolta come azione. Selezionare l'azione 'Delete Cluster', specificare tutti i parametri necessari e selezionare ✓: ![][11]

9. Salvare l'app per la logica usando il comando corrispondente nella parte superiore dell'area di progettazione.

Per testare lo scenario, selezionare **Run Now** per avviare l'app per la logica manualmente.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.jpg
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.jpg
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.jpg
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.jpg

<!---HONumber=Oct15_HO1-->