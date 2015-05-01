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

I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un flusso. HDInsight Connector consente di creare un cluster Hadoop in Azure e di inviare diversi processi Hadoop, ad esempio Hive, Pig, MapReduce e Streaming MapReduce. Il servizio Azure HDInsight distribuisce ed esegue il provisioning dei cluster Apache Hadoop nel cloud, offrendo un framework software progettato per gestire, analizzare e creare report sui Big Data. La memoria centrale di Hadoop fornisce un'archiviazione dei dati affidabile grazie ad Hadoop Distributed File System (HDFS), nonché un modello di programmazione semplice, MapReduce, per elaborare e analizzare in parallelo i dati archiviati nel sistema distribuito. Con HDInsight Connector è possibile avviare un cluster, inviare un processo e attenderne il completamento.

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

1. Aprire Azure Marketplace con l'opzione '+ NEW' in basso a sinistra nel portale di Azure
2. Andare a "Web e dispositivi mobili > App per le API" e cercare "HDInsight Connector".
3. Inserire i dettagli generici, come nome, piano di servizio app e così via nel primo pannello
4. Come parte delle impostazioni pacchetto, specificare la password e il nome utente del cluster HDInsight


 ![][1]  

## Configurazione dei certificati (facoltativo) ##

Nota: questo passaggio è necessario solo se si desidera eseguire operazioni di gestione (creazione ed eliminazione di cluster) nell'app per la logica.

Individuare l'applicazione API creata scegliendo Sfoglia -> App per le API -> <Nome dell'app per le API appena creata>. Verrà visualizzato il comportamento seguente. Nel componente  'Security' viene visualizzato 0, ad indicare che non esiste alcun certificato di gestione caricato.

![][2] 

Per caricare il certificato di gestione dell'app per le API, è necessario eseguire le operazioni seguenti
1. Fare clic sul componente  'Security'
2. Fare clic su 'Upload certificate' nel pannello 'Security' visualizzato
3. Individuare e selezionare il file del certificato nel pannello successivo
4. Dopo aver selezionato il certificato, fare clic su OK.

Una volta che il certificato è stato caricato, vengono visualizzati i relativi dettagli.

![][3] 

Nota: nel caso in cui si desideri modificare il certificato, è possibile caricarne un altro che sostituirà quello esistente.

## Utilizzo in un'app per la logica ##

HDInsight Connector può essere usato solo come azione in un'app per la logica. In questa sezione viene descritta una semplice app per la logica che crea un cluster, esegue un processo  'Hive' ed elimina il cluster dopo il completamento del processo.


- Durante la creazione o la modifica di un'app per la logica, scegliere come azione l'app per le API HDInsight Connector creata. Verranno visualizzate tutte le azioni disponibili.

![][5] 


- Selezionare 'Create Cluster', fornire tutti i parametri del cluster richiesti e fare clic su ✓.

![][6] 



- L'azione verrà ora visualizzata come configurata nell'app per la logica. Verranno visualizzati gli output dell'azione, che possono essere usati come input in azioni successive. 

![][7] 



- Selezionare lo stesso HDInsight Connector dalla raccolta come azione. Selezionare l'azione 'Wait For Cluster Creation', fornire tutti i parametri richiesti e fare clic su ✓.

![][8] 



- Selezionare lo stesso HDInsight Connector dalla raccolta come azione. Selezionare l'azione 'Submit Hive Job', fornire tutti i parametri richiesti e fare clic su ✓.

![][9] 



- Selezionare lo stesso HDInsight Connector dalla raccolta come azione. Selezionare l'azione 'Wait For Job Completion', fornire tutti i parametri richiesti e fare clic su ✓.

![][10] 



- Selezionare lo stesso HDInsight Connector dalla raccolta come azione. Selezionare l'azione 'Submit Hive Job', fornire tutti i parametri richiesti e fare clic su ✓.

![][11] 


È possibile fare clic su 'Run' per avviare l'app per la logica manualmente allo scopo di testare lo scenario.

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


<!--HONumber=52-->