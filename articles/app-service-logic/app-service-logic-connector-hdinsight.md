<properties 
   pageTitle="HDInsight Connector" 
   description="Come usare HDInsight Connector" 
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
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Microsoft HDInsight Connector #

I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un flusso. HDInsight Connector consente di creare un cluster Hadoop in Azure e di inviare diversi processi Hadoop, ad esempio Hive, Pig, MapReduce e Streaming MapReduce. Il servizio Azure HDInsight distribuisce ed esegue il provisioning dei cluster Apache Hadoop nel cloud, offrendo un framework software progettato per gestire, analizzare e creare report sui Big Data. La memoria centrale di Hadoop fornisce un'archiviazione dei dati affidabile grazie ad Hadoop Distributed File System (HDFS), nonché un modello di programmazione semplice, MapReduce, per elaborare e analizzare in parallelo i dati archiviati nel sistema distribuito. Con HDInsight Connector è possibile creare o eliminare un cluster, inviare un processo e attenderne il completamento.

###Azioni di base
		
- Creazione di un cluster
- Attesa della creazione di un cluster
- Invio di un processo Pig
- Invio di un processo Hive
- Invio di un processo MapReduce
- Attesa del completamento di un processo
- Eliminazione di un cluster


## Creare un'istanza dell'app per le API HDInsight Connector ##

Per usare HDInsight Connector è necessario creare un'istanza dell'app per le API HDInsight Connector. A questo scopo, procedere come segue:

1. Aprire Azure Marketplace con l'opzione '+ NUOVO' nella parte sinistra del portale di Azure e selezionare 'Marketplace'.
2. Passare ad 'App per le API', cercare "HDInsight Connector", selezionare l'app e quindi fare clic su 'Crea'.
3. Nel primo pannello specificare i dettagli generali, ad esempio nome, piano di servizio app e così via.
4. Nelle impostazioni del pacchetto specificare il nome utente e la password del cluster HDInsight e fare clic su OK.
5. Fare clic su 'Crea'.


 ![][1]

## Configurazione dei certificati (facoltativo) ##

Nota: questo passaggio è necessario solo per eseguire operazioni di gestione (creazione ed eliminazione di cluster) nell'app per la logica.

Passare all'app per le API HDInsight Connector appena creata. Nel componente 'Sicurezza' verrà visualizzato il valore 0, che indica che non è stato caricato alcun certificato di gestione.

![][2]

Per caricare il certificato di gestione nell'app per le API, è necessario eseguire le operazioni seguenti. 1. Fare clic sul componente Sicurezza 2. Nel pannello 'Sicurezza' fare clic su 'CARICA CERTIFICATO'. 3. Individuare e selezionare il file del certificato nel pannello successivo. 4. Dopo aver selezionato il certificato, fare clic su OK.

Una volta che il certificato è stato caricato, vengono visualizzati i relativi dettagli.

![][3]

Nota: se è necessario cambiare il certificato, è possibile caricarne un altro che sostituirà quello esistente.

## Utilizzo in un'app per la logica ##

HDInsight Connector può essere usato solo come azione in un'app per la logica. In questa sezione viene descritta una semplice app per la logica che crea un cluster, esegue un processo 'Hive' ed elimina il cluster dopo il completamento del processo.


- Nella scheda 'Avvia logica' fare clic su 'Esegui la logica manualmente'.
- Selezionare l'app per le API HDInsight Connector creata dalla raccolta per visualizzare tutte le azioni disponibili.

![][5]


- Selezionare 'Crea cluster', specificare tutti i parametri del cluster necessari e fare clic su ✓.

![][6]



- L'azione verrà ora visualizzata come configurata nell'app per la logica. Verranno visualizzati gli output dell'azione, che possono essere usati come input in azioni successive. 

![][7]



- Selezionare lo stesso HDInsight Connector dalla raccolta come azione. Selezionare l'azione 'Wait For Cluster Creation', specificare tutti i parametri necessari e fare clic su ✓.

![][8]



- Selezionare lo stesso HDInsight Connector dalla raccolta come azione. Selezionare l'azione 'Submit Hive Job', specificare tutti i parametri necessari e fare clic su ✓.

![][9]



- Selezionare lo stesso HDInsight Connector dalla raccolta come azione. Selezionare l'azione 'Wait For Job Completion', specificare tutti i parametri necessari e fare clic su ✓.

![][10]



- Selezionare lo stesso HDInsight Connector dalla raccolta come azione. Selezionare l'azione 'Delete Cluster', specificare tutti i parametri necessari e fare clic su ✓.

![][11]

- Salvare l'app per la logica usando il comando di salvataggio nella parte superiore dell'area di progettazione.

È possibile fare clic su 'Esegui adesso' per avviare l'app per la logica manualmente allo scopo di testare lo scenario.

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
 

<!---HONumber=62-->