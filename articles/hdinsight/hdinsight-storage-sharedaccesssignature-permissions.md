---
title: Limitare l'accesso di HDInsight ai dati con le firme di accesso condiviso
description: Informazioni su come usare le firme di accesso condiviso per limitare l'accesso di HDInsight ai dati archiviati nei BLOB di archiviazione di Azure.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2016
ms.author: larryfr

---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>Usare le firme di accesso condiviso di Archiviazione di Azure per limitare l'accesso ai dati con HDInsight
HDInsight usa i BLOB di archiviazione di Azure per l'archiviazione dei dati. HDInsight deve avere accesso completo al BLOB usato come risorsa di archiviazione predefinita per il cluster, ma è possibile limitare le autorizzazioni per i dati archiviati in altri BLOB usati dal cluster. Ad esempio, è possibile fare in modo che alcuni dati siano di sola lettura usando le firme di accesso condiviso.

Le firme di accesso condiviso sono una funzionalità degli account di archiviazione di Azure che consente di limitare l'accesso ai dati. Ad esempio, concedendo l'accesso in sola lettura ai dati. In questo documento si apprenderà come usare le firme di accesso condiviso per abilitare l'accesso in sola lettura e solo elenco a un contenitore BLOB da HDInsight.

## <a name="requirements"></a>Requisiti
* Una sottoscrizione di Azure.
* C# o Python. Il codice di esempio in C# viene fornito come soluzione di Visual Studio.
  
  * Visual Studio versione 2013 o 2015.
  * Python versione 2.7 o successiva.
* Un cluster HDInsight basato su Linux OPPURE [Azure PowerShell][powershell]. Se è disponibile un cluster basato su Linux esistente, è possibile usare Ambari per aggiungere una firma di accesso condiviso al cluster. In caso contrario, è possibile usare Azure PowerShell per creare un nuovo cluster e aggiungere una firma di accesso condiviso durante la creazione del cluster.
* I file di esempio da [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Questo repository contiene:
  
  * Un progetto di Visual Studio che può creare un contenitore di archiviazione, i criteri archiviati e la firma di accesso condiviso da usare con HDInsight.
  * Uno script di Python che può creare un contenitore di archiviazione, i criteri archiviati e la firma di accesso condiviso da usare con HDInsight.
  * Uno script di PowerShell che può creare un nuovo cluster HDInsight e configurarlo per l'uso della firma di accesso condiviso.

## <a name="shared-access-signatures"></a>Firme di accesso condiviso
Esistono due tipi di firme di accesso condiviso:

* Ad hoc: l'ora di inizio, la scadenza e le autorizzazioni della firma di accesso condiviso vengono tutte specificate nell'URI corrispondente oppure sono implicite, nel caso in cui l'ora di inizio venga omessa.
* Criteri di accesso archiviati: i criteri di accesso archiviati vengono definiti per un contenitore di risorse, ovvero un contenitore BLOB, una tabella, una coda o una condivisione file, e possono essere usati per gestire i vincoli per una o più firme di accesso condiviso. Quando si associa una firma di accesso condiviso a criteri di accesso archiviati, la firma eredita i vincoli, ovvero ora di inizio, scadenza e autorizzazioni, definiti per i criteri di accesso archiviati.

La differenza tra le due forme è importante un unico scenario chiave, la revoca. Una firma di accesso condiviso è un URL, pertanto chiunque la ottiene può utilizzarla indipendentemente da chi l'ha richiesta per iniziare. Se la firma di accesso condiviso è stata pubblicata e resa pubblica, può essere usata da chiunque in tutto il mondo. Una forma di accesso condiviso rimane valida finché non si verifica una delle quattro condizioni seguenti:

1. Viene raggiunta la scadenza specificata nella firma.
2. Viene raggiunta la scadenza specificata nei criteri di accesso archiviati cui viene fatto riferimento nella firma (se viene fatto riferimento a criteri di accesso archiviati e se questi indicano una scadenza). Tale situazione può verificarsi alla scadenza dell'intervallo oppure perché i criteri di accesso archiviati sono stati modificati in modo che la scadenza ricorra nel passato e ciò corrisponde a un modo per revocare la firma di accesso condiviso.
3. I criteri di accesso archiviati cui viene fatto riferimento nella firma di accesso condiviso vengono eliminati e ciò corrisponde a un altro modo per revocare la firma. Si noti che se si ricreano i criteri di accesso archiviati con lo stesso nome, tutti i token esistenti della firma di accesso condiviso saranno ancora validi in base alle autorizzazioni associate ai criteri (presupponendo che non sia stata superata la scadenza indicata nella firma). Se si intende revocare la firma di accesso condiviso, assicurarsi di usare un nome diverso per ricreare i criteri di accesso archiviati con scadenza nel futuro.
4. La chiave dell'account utilizzata per creare la firma di accesso condiviso viene rigenerata. Si noti che in seguito a tale operazione tutti i componenti dell'applicazione che utilizzano la chiave dell'account non verranno più autenticati finché non verranno aggiornati in modo da usare l'altra chiave dell'account valida oppure la chiave dell'account appena rigenerata.

> [!IMPORTANT]
> L'URI di una firma di accesso condiviso è associato alla chiave dell'account usata per creare la firma e ai relativi criteri di accesso archiviati (se presenti). Se non sono specificati criteri di accesso archiviati, l'unico modo per revocare una firma di accesso condiviso consiste nel modificare la chiave dell'account. 
> 
> 

È consigliabile usare sempre i criteri di accesso archiviati, per poter revocare le firme o estendere la data di scadenza in base alle esigenze. I passaggi illustrati in questo documento permettono di usare i criteri di accesso archiviati per generare firme di accesso condiviso.

Per altre informazioni sulle firme di accesso condiviso, vedere [Informazioni sul modello di firma di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-generate-a-sas"></a>Creare un criterio archiviato e generare una firma di accesso condiviso
Attualmente i criteri archiviati devono essere creati a livello di codice. Gli esempi di creazione di criteri archiviati e firme di accesso condiviso in C# e in Python sono disponibili all'indirizzo [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

### <a name="create-a-stored-policy-and-sas-using-c\#"></a>Creare un criterio archiviato e una firma di accesso condiviso con C\
1. Aprire la soluzione in Visual Studio.
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SASToken** e scegliere **Proprietà**.
3. Selezionare **Impostazioni** e aggiungere i valori per le voci seguenti:
   
   * StorageConnectionString: stringa di connessione per l'account di archiviazione per cui si vuole creare un criterio archiviato e una firma di accesso condiviso. Il formato deve essere `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`, dove `myaccount` è il nome dell'account di archiviazione e `mykey` è la chiave dell'account di archiviazione.
   * ContainerName: contenitore nell'account di archiviazione a cui si vuole limitare l'accesso.
   * SASPolicyName: nome da usare per il criterio archiviato che viene creato.
   * FileToUpload: percorso di un file da caricare nel contenitore.
4. Eseguire il progetto. Dopo che è stata generata la firma di accesso condiviso viene visualizzata una finestra della console e vengono visualizzate informazioni simili alle seguenti:
   
        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
   
    Salvare il token dei criteri di firma di accesso condiviso, perché sarà necessario per associare l'account di archiviazione al cluster di HDInsight. Saranno necessari anche il nome dell'account di archiviazione e il nome del contenitore.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Creare un criterio archiviato e una firma di accesso condiviso con Python
1. Aprire il file SASToken.py e modificare i valori seguenti:
   
   * policy\_name: nome da usare per il criterio archiviato che viene creato.
   * storage\_account\_name: nome del proprio account di archiviazione.
   * storage\_account\_key: chiave per l'account di archiviazione.
   * storage\_container\_name: contenitore nell'account di archiviazione a cui si vuole limitare l'accesso.
   * example\_file\_path: percorso di un file da caricare nel contenitore.
2. Eseguire lo script. Al termine dello script viene visualizzato un token della firma di accesso condiviso simile al seguente:
   
        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
   
    Salvare il token dei criteri di firma di accesso condiviso, perché sarà necessario per associare l'account di archiviazione al cluster di HDInsight. Saranno necessari anche il nome dell'account di archiviazione e il nome del contenitore.

## <a name="use-the-sas-with-hdinsight"></a>Usare la firma di accesso condiviso con HDInsight
Quando si crea un cluster HDInsight è necessario specificare un account di archiviazione primario e, facoltativamente, è possibile specificare account di archiviazione aggiuntivi. Entrambi i metodi di aggiunta di risorse di archiviazione richiedono l'accesso completo agli account di archiviazione e ai contenitori usati.

Per usare una firma di accesso condiviso allo scopo di limitare l'accesso a un contenitore, è necessario aggiungere una voce personalizzata alla configurazione del **core-site** per il cluster.

* Per i cluster HDInsight **basati su Windows** o **basati su Linux**, è possibile eseguire questa operazione durante la creazione del cluster con PowerShell.
* Per i cluster HDInsight **basati su Linux** , è possibile modificare la configurazione dopo la creazione del cluster con Ambari.

### <a name="create-a-new-cluster-that-uses-the-sas"></a>Creare un nuovo cluster che usa la firma di accesso condiviso
La directory `CreateCluster` del repository include un esempio di creazione di un cluster HDInsight che usa la firma di accesso condiviso. Per usarlo, seguire questa procedura:

1. Aprire il file `CreateCluster\HDInsightSAS.ps1` in un editor di testo e modificare i valori seguenti all'inizio del documento.
   
        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
   
    Ad esempio, sostituire `'mycluster'` con il nome del cluster che si vuole creare. I valori della firma di accesso condiviso devono corrispondere ai valori usati nei passaggi precedenti durante la creazione di un token dell'account di archiviazione e della firma di accesso condiviso.
   
    Dopo aver modificato i valori, salvare il file.
2. Aprire un nuovo prompt dei comandi di Azure PowerShell. Se non si ha familiarità con Azure PowerShell o non è stato installato, vedere [Come installare e configurare Azure PowerShell][powershell].
3. Dal prompt dei comandi, usare quanto riportato di seguito per eseguire l'autenticazione alla sottoscrizione di Azure:
   
        Login-AzureRmAccount
   
    Se richiesto, accedere con l'account associato alla sottoscrizione di Azure.
   
    Se l'account di accesso è associato a più sottoscrizioni di Azure, potrebbe essere necessario usare `Select-AzureRmSubscription` per selezionare la sottoscrizione da usare.
4. Dal prompt dei comandi, passare alla directory `CreateCluster` che contiene il file HDInsightSAS.ps1. Usare quindi il comando seguente per eseguire lo script:
   
        .\HDInsightSAS.ps1
   
    Durante l'esecuzione, l'output dello script viene registrato nel prompt di PowerShell, man mano che vengono creati gli account di archiviazione e il gruppo di risorse. Verrà quindi richiesto di immettere l'utente HTTP per il cluster HDInsight. Si tratta dell'account utente usato per proteggere l'accesso HTTP/S al cluster.
   
    Se si sta creando un cluster basato su Linux, verranno anche richiesti un nome e una password per l'account utente SSH. Queste credenziali vengono usate per l'accesso in remoto al cluster.
   
   > [!IMPORTANT]
   > Quando vengono richiesti un nome e una password per HTTP/S o SSH, è necessario fornire una password che soddisfi i criteri seguenti:
   > 
   > * La lunghezza non può essere inferiore a 10 caratteri
   > * Deve contenere almeno una cifra
   > * Deve contenere almeno un carattere non alfanumerico
   > * Deve contenere almeno una lettera maiuscola o minuscola
   > 
   > 

Il completamento dello script richiede in genere circa 15 minuti. Se lo script viene completato senza errori, il cluster è stato creato.

### <a name="update-an-existing-cluster-to-use-the-sas"></a>Aggiornare un cluster esistente per l'uso della firma di accesso condiviso
Se è disponibile un cluster basato su Linux esistente, è possibile aggiungere la firma di accesso condiviso alla configurazione del **core-site** seguendo questa procedura:

1. Aprire l'interfaccia utente Web di Ambari per il cluster. L'indirizzo di questa pagina è https://NOMECLUSTER.azurehdinsight.net. Quando richiesto, eseguire l'autenticazione nel cluster con il nome utente amministratore (admin) e la password usati durante la creazione del cluster.
2. Nel lato sinistro dell'interfaccia utente Web di Ambari selezionare **HDFS** e quindi selezionare la scheda **Configs** al centro della pagina.
3. Selezionare la scheda **Advanced** e scorrere fino alla sezione **Custom core-site**.
4. Espandere la sezione **Custom core-site**, quindi scorrere fino alla fine e selezionare il collegamento **Add property**. Usare i valori seguenti per i campi **Key** e **Value**:
   
   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Value**: la firma di accesso condiviso restituita dall'applicazione C# o Python eseguita in precedenza
     
     Sostituire **CONTAINERNAME** con il nome del contenitore usato con l'applicazione C# o della firma di accesso condiviso. Sostituire **STORAGEACCOUNTNAME** con il nome dell'account di archiviazione usato.
5. Fare clic sul pulsate **Add** per salvare la chiave e il valore, quindi fare clic sul pulsante **Save** per salvare le modifiche alla configurazione. Quando richiesto, aggiungere una descrizione della modifica, ad esempio, "aggiunta di accesso alle risorse di archiviazione per le firme di accesso condiviso", e quindi fare clic su **Save**.
   
    Al termine delle modifiche, fare clic su **OK** .
   
   > [!IMPORTANT]
   > Questo permette di salvare le modifiche alla configurazione, ma perché le modifiche siano effettive è necessario riavviare diversi servizi.
   > 
   > 
6. Nell'interfaccia utente Web di Ambari selezionare **HDFS** dall'elenco a sinistra e quindi selezionare **Restart All** dall'elenco a discesa **Service Actions** a destra. Quando richiesto, selezionare **Turn on maintenance mode** e quindi selezionare __Conform Restart All".
   
    Ripetere questa procedura per le voci MapReduce2 e YARN dell'elenco a sinistra della pagina.
7. Dopo il riavvio di queste voci, selezionarle una alla volta e disabilitare la modalità di manutenzione dall'elenco a discesa **Service Actions** .

## <a name="test-restricted-access"></a>Testare l'accesso limitato
Per verificare che l'accesso sia effettivamente limitato, usare i metodi seguenti:

* Per i cluster HDInsight **basati su Windows** , usare Desktop remoto per connettersi al cluster. Per altre informazioni, vedere [Connettersi a HDInsight con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) .
  
    Dopo aver stabilito la connessione, usare l'icona della **riga di comando di Hadoop** sul desktop per aprire il prompt dei comandi.
* Per i cluster HDInsight **basati su Linux** , usare SSH per connettersi al cluster. Per informazioni sull'uso di SSH con i cluster basati su Linux, vedere uno degli argomenti seguenti:
  
  * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
  * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Dopo aver stabilito la connessione al cluster, usare la procedura seguente per verificare che nell'account di archiviazione della firma di accesso condiviso sia solo possibile leggere ed elencare gli elementi:

1. Dal prompt dei comandi, digitare quanto segue. Sostituire **SASCONTAINER** con il nome del contenitore creato per l'account di archiviazione della firma di accesso condiviso. Sostituire **SASACCOUNTNAME** con il nome dell'account di archiviazione usato per la firma di accesso condiviso:
   
        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
   
    Verrà elencato il contenuto del contenitore, che deve includere il file caricato durante la creazione del contenitore e della firma di accesso condiviso.
2. Usare il comando seguente per verificare che sia possibile leggere il contenuto del file. Sostituire **SASCONTAINER** e **SASACCOUNTNAME** come indicato nel passaggio precedente. Sostituire **FILENAME** con il nome del file visualizzato nel comando precedente:
   
        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
   
    Verrà elencato il contenuto del file.
3. Usare il comando seguente per scaricare il file nel file system locale:
   
        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
   
    Il file verrà scaricato in un file locale denominato **testfile.txt**.
4. Usare il comando seguente per caricare il file locale in un nuovo file denominato **testupload.txt** nella risorsa di archiviazione della firma di accesso condiviso:
   
        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
   
    Verrà visualizzato un messaggio simile al seguente:
   
        put: java.io.IOException
   
    Questo errore si verifica perché il percorso di archiviazione è di sola lettura+elenco. Usare il comando seguente per inserire i dati nella risorsa di archiviazione predefinita per il cluster, accessibile in scrittura:
   
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
   
    Questa volta l'operazione avrà esito positivo.

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="a-task-was-canceled"></a>Un'attività è stata annullata
**Sintomi**: durante la creazione di un cluster con lo script di PowerShell, si potrebbe ricevere il messaggio di errore seguente:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Causa**: questo errore si può verificare se si usa una password per l'utente amministratore o l'utente HTTP per il cluster oppure, nei cluster basati su Linux, per l'utente SSH.

**Risoluzione**: usare una password che soddisfi i criteri seguenti:

* La lunghezza non può essere inferiore a 10 caratteri
* Deve contenere almeno una cifra
* Deve contenere almeno un carattere non alfanumerico
* Deve contenere almeno una lettera maiuscola o minuscola

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come aggiungere risorse di archiviazione ad accesso limitato al cluster HDInsight, è possibile conoscere altri modi per usare i dati nel cluster:

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md



<!--HONumber=Oct16_HO2-->


