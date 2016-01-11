<properties
   	pageTitle="Creare cluster Hadoop, HBase, Storm o Spark in Linux in HDInsight tramite l’SDK HDInsight .NET | Microsoft Azure"
   	description="Informazioni su come creare cluster Hadoop, HBase, Storm o Spark su Linux per HDInsight tramite l’SDK HDInsight .NET."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/08/2015"
   	ms.author="jgao"/>

#Creare cluster basati su Linux in HDInsight tramite il SDK di .NET

[AZURE.INCLUDE [selettore](../../includes/hdinsight-create-linux-cluster-selector.md)]

.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET Framework. In questo documento viene illustrato come creare un cluster HDInsight basato su Linux tramite l’SDK di .NET.

> [AZURE.IMPORTANT]I passaggi descritti in questo documento creano un cluster con un nodo di lavoro. Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14GB di RAM.
>
> Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Prerequisiti

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Visual Studio 2013 o 2015__

##Creare un'applicazione console

1. Aprire Visual Studio 2013 o 2015.

2. Creare un nuovo progetto di Visual Studio con le impostazioni seguenti

    |Proprietà|Valore|
    |--------|-----|
    |Modello|Templates/Visual C#/Windows/Console Application|
    |Nome|CreateHDICluster|

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.

6. Eseguire il comando seguente nella console per installare i pacchetti:

        Install-Package Microsoft.Azure.Common.Authentication -pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre

    Questi comandi aggiungono librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

6. Da Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo, incollare il codice seguente e fornire valori per le variabili:

        using System;
        using System.Security;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        
        namespace CreateHDICluster
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
                
                //Replace SUBSCRIPTIONID with your Azure subscription ID
                private static Guid SubscriptionId = new Guid(SUBSCRIPTIONID);
                
                //Replace GROUPNAME with the name of the resource group to create the cluster in
                private const string ResourceGroupName = "GROUPNAME";
                
                //Replace CLUSTERNAME with the name of the HDInsight cluster you wish to create
                private const string NewClusterName = "CLUSTERNAME";
                private const int NewClusterNumNodes = 1;
                
                //Replace LOCATION with the geographic region that the resource group, storage account, and HDInsight cluster are in
                private const string NewClusterLocation = "LOCATION";
                private const string NewClusterVersion = "3.2";
                
                //Replace STORAGENAME with the name of the storage account to use
                private const string ExistingStorageName = "STORAGENAME.blob.core.windows.net";
                
                //Replace STORAGEKEY with the key for the storage account
                private const string ExistingStorageKey = "STORAGEKEY";
                
                //Replace CONTAINERNAME with the container to use for HDInsight's default storage
                private const string ExistingContainer = "CONTAINTERNAME";
                private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
            private const OSType NewClusterOSType = OSType.Linux;
                private const string NewClusterUsername = "admin";
                
                //Replace ADMINPASSWORD with the password for the admin account
                private const string NewClusterPassword = "ADMINPASSWORD";
                
                //Replace SSHUSER with the user name you want to use with logging in to the cluster through SSH
                private const string NewClusterSshUserName = "SSHUSER";
                
                //Replace SSHPUBLICKEY with the public key certificate to use when authenticating the SSH user. For more information on generating and using SSH keys with HDInsight, see https://azure.microsoft.com/it-IT/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/ and https://azure.microsoft.com/it-IT/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/
                private const string NewClusterSshPublicKey = @"SSHPUBLICKEY";
        
                private static void Main(string[] args)
                {
                    System.Console.WriteLine("Running");
                    
                    //Authenticate to your subscription
                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    //Get an HDIManagement client
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    //Create a new cluster
                    CreateCluster();
                }
        
                public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();
        
                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };
        
                    if (username != null && password != null)
                        account.Id = username;
        
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
        
                    var accessToken =
                        authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                            .AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
        
        
                private static void CreateCluster()
                {
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        Location = NewClusterLocation,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOSType,
                        Version = NewClusterVersion,
        
                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingContainer,
        
                        UserName = NewClusterUsername,
                        Password = NewClusterPassword,
                        SshUserName = NewClusterSshUserName,
                        SshPublicKey = NewClusterSshPublicKey
                    };
        
                    ScriptAction rScriptAction = new ScriptAction("Install R",
                        new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");
        
                    parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
                parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });
                    
                    _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
                }
        
            }
        }

		
10. Sostituire i valori dei membri di classe.

7. Premere **F5** per eseguire l'applicazione. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. Verrà richiesto di immettere le credenziali dell'account Azure. Possono essere necessari alcuni minuti per creare un cluster HDInsight, in genere circa 15.

##Passaggi successivi

Dopo aver creato un cluster HDInsight, usare le informazioni seguenti per acquisire familiarità con il cluster.

###Cluster Hadoop

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Cluster HBase

* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-stared-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux)

###Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###Cluster Spark

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire i processi in modalità remota in un cluster Spark utilizzando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_1223_2015-->