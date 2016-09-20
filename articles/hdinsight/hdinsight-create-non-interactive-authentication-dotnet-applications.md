<properties
	pageTitle="Creare applicazioni .NET HDInsight di autenticazione non interattive | Microsoft Azure"
	description="Informazioni su come creare applicazioni .NET HDInsight di autenticazione non interattive."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="jgao"/>  

# Creare applicazioni .NET HDInsight di autenticazione non interattive

È possibile eseguire l'applicazione .NET Azure HDInsight con l'identità specifica dell'applicazione (non interattiva) o con l'identità dell'utente che ha eseguito l'accesso all'applicazione (interattiva). Per un esempio dell'applicazione interattiva, vedere [Invio di processi Hive/Pig/Sqoop tramite HDInsight .NET SDK](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Questo articolo descrive come creare un'applicazione .NET di autenticazione non interattiva per connettersi ad Azure HDInsight e inviare un processo Hive.

Nell'applicazione .NET, saranno necessari:

- l'ID tenant della sottoscrizione di Azure
- l'ID client dell'applicazione della directory Azure
- la chiave privata dell'applicazione della directory Azure.

Il processo principale include i passaggi seguenti:

2. Creare un'applicazione della directory Azure.
2. Assegnare i ruoli all'applicazione AD.
3. Sviluppare l'applicazione client.


##Prerequisiti

- Cluster HDInsight. È possibile crearne uno seguendo le istruzioni dell'[esercitazione introduttiva](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).




## Creare un'applicazione della directory Azure 
Quando si crea un'applicazione Active Directory vengono create sia l'applicazione che un'entità servizio. È possibile eseguire l'applicazione con l'identità dell'applicazione.

Attualmente, è necessario usare il portale di Azure classico per creare una nuova applicazione Active Directory. Questa funzionalità verrà aggiunta al portale di Azure in una versione successiva. È inoltre possibile eseguire questi passaggi tramite Azure PowerShell o l’Interfaccia della riga di comando di Azure. Per altre informazioni sull'uso di PowerShell o dell'interfaccia della riga di comando con l'entità servizio, vedere [Autenticazione di un'entità servizio con Azure Resource Manager](../resource-group-authenticate-service-principal.md).

**Per creare un'applicazione della directory Azure**

1.	Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2.	Selezionare **Active Directory** dal riquadro di sinistra.

    ![active directory del portale di Azure classico](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.	Selezionare la directory da usare per la creazione della nuova applicazione. Selezionare la directory esistente.
4.	Fare clic su **Applicazioni** in alto per visualizzare un elenco delle applicazioni esistenti.
5.	Fare clic su **Aggiungi** in basso per aggiungere una nuova applicazione.
6.	Immettere **Nome**, selezionare **Applicazione Web e/o API Web**, quindi fare clic su **Avanti**.

    ![nuova applicazione azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.	Immettere **URL accesso** e **URI ID app**. Per **URL ACCESSO** specificare l'URI a un sito Web che descrive l'applicazione. L'esistenza del sito Web non viene convalidata. Per URI ID app, specificare l'URI che identifica l'applicazione. Quindi fare clic su **Completa**. La creazione dell'applicazione richiede alcuni minuti. Dopo aver creato l'applicazione, il portale visualizza la pagina Riepilogo rapido della nuova applicazione. Non chiudere il portale.

    ![proprietà della nuova applicazione azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Per ottenere l'ID client e la chiave privata dell'applicazione**

1.	Nella pagina della nuova applicazione AD, fare clic su **Configura** nel menu in alto.
2.	Creare una copia dell'**ID client**. Sarà necessario usarla nell'applicazione .NET.
3.	In **Chiavi** fare clic sul menu a discesa **Seleziona durata** e selezionare **1 anno** o **2 anni**. Il valore della chiave viene visualizzato solo dopo aver salvato la configurazione.
4.	Fare clic su **Salva** nella parte inferiore della pagina. Quando viene visualizzata la chiave privata, creare una copia della chiave. Sarà necessario usarla nell'applicazione .NET.

##Assegnare l'applicazione AD a un ruolo

È necessario assegnare l'applicazione a un [ruolo](../active-directory/role-based-access-built-in-roles.md) per concedere le autorizzazioni per l'esecuzione delle azioni. È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione è in grado di leggere il gruppo di risorse e le risorse in esso contenute. In questa esercitazione verrà impostato l'ambito a livello di gruppo di risorse. Poiché il portale di Azure classico non supporta i gruppi di risorse, queste operazioni devono essere eseguite nel portale di Azure.

**Per aggiungere il ruolo Proprietario all'applicazione AD**

1.	Accedere al [portale di Azure](https://portal.azure.com).
2.	Fare clic su **Gruppo di risorse** nel pannello di sinistra.
3.	Fare clic sul gruppo di risorse contenente il cluster HDInsight in cui verrà eseguita la query Hive più avanti in questa esercitazione. Se è presente un numero eccessivo di gruppi di risorse, è possibile usare il filtro.
4.	Fare clic su **Accesso** nel pannello del cluster.

    ![icona cloud e thunderbolt = guida rapida](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.	Fare clic su **Aggiungi** nel pannello **Utenti**.
6.	Seguire le istruzioni per aggiungere il ruolo **Proprietario** all'applicazione AD creata nella procedura precedente. Al termine, l'applicazione verrà visualizzata nel pannello Utenti con il ruolo Proprietario.


##Sviluppare l'applicazione client HDInsight

Creare un'applicazione console C# .net seguendo le istruzioni disponibili in [Inviare processi Hadoop in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Sostituire quindi il metodo GetTokenCloudCredentials con il codice seguente:

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Per recuperare l'ID tenant usando PowerShell:

    Get-AzureRmSubscription

Oppure l'interfaccia della riga di comando di Azure:

    azure account show --json

      
## Vedere anche

- [Inviare processi Hadoop in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Creare un'applicazione e un'entità servizio di Active Directory tramite il portale](../resource-group-create-service-principal-portal.md)
- [Autenticazione di un'entità servizio con Azure Resource Manager](../resource-group-authenticate-service-principal.md)
- [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0907_2016-->