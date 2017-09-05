---
title: 'Limitare l''accesso usando le firme di accesso condiviso: Azure HDInsight | Microsoft Docs'
description: Informazioni su come usare le firme di accesso condiviso per limitare l'accesso di HDInsight ai dati archiviati nei BLOB di archiviazione di Azure.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/11/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 470d4a57adf161b41a0b41061bec3840ddbf7f27
ms.contentlocale: it-it
ms.lasthandoff: 08/15/2017

---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Usare le firme di accesso condiviso di archiviazione di Azure per limitare l'accesso ai dati in HDInsight

HDInsight ha accesso completo ai dati negli account di archiviazione di Azure associati al cluster. È possibile usare le firme di accesso condiviso nel contenitore BLOB per limitare l'accesso ai dati, ad esempio per fornire accesso di sola lettura ai dati. Le firme di accesso condiviso sono una funzionalità degli account di archiviazione di Azure che consente di limitare l'accesso ai dati. Ad esempio, concedendo l'accesso in sola lettura ai dati.

> [!IMPORTANT]
> Per una soluzione che usi Apache Ranger, considerare la possibilità di usare HDInsight aggiunto al dominio. Per altre informazioni, vedere il documento [Configurare i cluster HDInsight aggiunti al dominio](hdinsight-domain-joined-configure.md).

> [!WARNING]
> HDInsight deve avere accesso completo alla risorsa di archiviazione predefinita per il cluster.

## <a name="requirements"></a>Requisiti

* Una sottoscrizione di Azure.
* C# o Python. Il codice di esempio in C# viene fornito come soluzione di Visual Studio.

  * Visual Studio versione 2013, 2015 o 2017
  * Python versione 2.7 o successiva.

* Un cluster HDInsight basato su Linux OPPURE [Azure PowerShell][powershell]. Se è disponibile un cluster basato su Linux esistente, è possibile usare Ambari per aggiungere una firma di accesso condiviso al cluster. In caso contrario, è possibile usare Azure PowerShell per creare un cluster e aggiungere una firma di accesso condiviso durante la creazione del cluster.

    > [!IMPORTANT]
    > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* I file di esempio da [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Il repository contiene gli elementi seguenti:

  * Un progetto di Visual Studio che può creare un contenitore di archiviazione, i criteri archiviati e la firma di accesso condiviso da usare con HDInsight.
  * Uno script di Python che può creare un contenitore di archiviazione, i criteri archiviati e la firma di accesso condiviso da usare con HDInsight.
  * Uno script di PowerShell in grado di creare un cluster HDInsight e configurarlo per l'uso della firma di accesso condiviso.

## <a name="shared-access-signatures"></a>Firme di accesso condiviso

Esistono due tipi di firme di accesso condiviso:

* Ad hoc: l'ora di inizio, la scadenza e le autorizzazioni per la firma di accesso condiviso vengono tutte specificate nell'URI corrispondente.

* Criteri di accesso archiviati: i criteri di accesso archiviati vengono definiti per un contenitore di risorse, ovvero un contenitore BLOB. I criteri di accesso archiviati possono essere usati per gestire i vincoli per una o più firme di accesso condiviso. Quando si associa una firma di accesso condiviso a criteri di accesso archiviati, la firma eredita i vincoli, ovvero ora di inizio, scadenza e autorizzazioni, definiti per i criteri di accesso archiviati.

La differenza tra le due forme è importante un unico scenario chiave, la revoca. Una firma di accesso condiviso è un URL, pertanto chiunque la ottiene può utilizzarla indipendentemente da chi l'ha richiesta per iniziare. Se la firma di accesso condiviso è stata pubblicata e resa pubblica, può essere usata da chiunque in tutto il mondo. Una forma di accesso condiviso rimane valida finché non si verifica una delle quattro condizioni seguenti:

1. Viene raggiunta la scadenza specificata nella firma.

2. Viene raggiunta la scadenza specificata nei criteri di accesso archiviati a cui fa riferimento la firma di accesso condiviso. Gli scenari seguenti portano a raggiungere la scadenza:

    * L'intervallo di tempo è trascorso.
    * Per i criteri di accesso archiviati è stata impostata una scadenza nel passato. Modificando la scadenza è possibile revocare la firma di accesso condiviso.

3. I criteri di accesso archiviati cui viene fatto riferimento nella firma di accesso condiviso vengono eliminati e ciò corrisponde a un altro modo per revocare la firma. Se si ricreano i criteri di accesso archiviati specificando lo stesso nome, tutti i token di firma di accesso condiviso relativi ai criteri precedenti restano validi, a condizione che l'ora di scadenza indicata nella firma di accesso condiviso non sia trascorsa. Se si intende revocare la firma di accesso condiviso, verificare di usare un nome diverso per ricreare i criteri di accesso archiviati con scadenza nel futuro.

4. La chiave dell'account utilizzata per creare la firma di accesso condiviso viene rigenerata. Se si rigenera la chiave, l'autenticazione di tutte le applicazioni che usano la chiave precedente avrà esito negativo. Aggiornare tutti i componenti con la nuova chiave.

> [!IMPORTANT]
> L'URI di una firma di accesso condiviso è associato alla chiave dell'account usata per creare la firma e ai relativi criteri di accesso archiviati (se presenti). Se non sono specificati criteri di accesso archiviati, l'unico modo per revocare una firma di accesso condiviso consiste nel modificare la chiave dell'account.

È consigliabile usare sempre i criteri di accesso archiviati. Con i criteri archiviati, è possibile revocare le firme o estendere la scadenza in base alle esigenze. I passaggi illustrati in questo documento permettono di usare i criteri di accesso archiviati per generare firme di accesso condiviso.

Per altre informazioni sulle firme di accesso condiviso, vedere [Informazioni sul modello di firma di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Creare un criterio archiviato e una firma di accesso condiviso con C\#

1. Aprire la soluzione in Visual Studio.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SASToken** e scegliere **Proprietà**.

3. Selezionare **Impostazioni** e aggiungere i valori per le voci seguenti:

   * StorageConnectionString: stringa di connessione per l'account di archiviazione per cui si vuole creare un criterio archiviato e una firma di accesso condiviso. Il formato deve essere `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`, dove `myaccount` è il nome dell'account di archiviazione e `mykey` è la chiave dell'account di archiviazione.

   * ContainerName: contenitore nell'account di archiviazione a cui si vuole limitare l'accesso.

   * SASPolicyName: nome da usare per i criteri archiviati da creare.

   * FileToUpload: percorso di un file caricato nel contenitore.

4. Eseguire il progetto. Dopo la generazione della firma di accesso condiviso, vengono visualizzate informazioni simili al testo seguente:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Salvare il token dei criteri di firma di accesso condiviso, il nome dell'account di archiviazione e il nome del contenitore. Questi valori vengono usati quando si associa l'account di archiviazione al cluster HDInsight.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Creare un criterio archiviato e una firma di accesso condiviso con Python

1. Aprire il file SASToken.py e modificare i valori seguenti:

   * policy\_name: nome da usare per i criteri archiviati da creare.

   * storage\_account\_name: nome del proprio account di archiviazione.

   * storage\_account\_key: chiave per l'account di archiviazione.

   * storage\_container\_name: contenitore nell'account di archiviazione a cui si vuole limitare l'accesso.

   * example\_file\_path: percorso di un file caricato nel contenitore.

2. Eseguire lo script. Al termine dello script viene visualizzato un token della firma di accesso condiviso simile al testo seguente:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Salvare il token dei criteri di firma di accesso condiviso, il nome dell'account di archiviazione e il nome del contenitore. Questi valori vengono usati quando si associa l'account di archiviazione al cluster HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Usare la firma di accesso condiviso con HDInsight

Quando si crea un cluster HDInsight è necessario specificare un account di archiviazione primario e, facoltativamente, è possibile specificare account di archiviazione aggiuntivi. Entrambi i metodi di aggiunta di risorse di archiviazione richiedono l'accesso completo agli account di archiviazione e ai contenitori usati.

Per usare una firma di accesso condiviso allo scopo di limitare l'accesso a un contenitore, aggiungere una voce personalizzata alla configurazione del **core-site** per il cluster.

* Per i cluster HDInsight **basati su Windows** o **basati su Linux**, è possibile aggiungere la voce durante la creazione del cluster con PowerShell.
* Per i cluster HDInsight **basati su Linux**, modificare la configurazione dopo la creazione del cluster con Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Creare un cluster che usa la firma di accesso condiviso

La directory `CreateCluster` del repository include un esempio di creazione di un cluster HDInsight che usa la firma di accesso condiviso. Per usarlo, seguire questa procedura:

1. Aprire il file `CreateCluster\HDInsightSAS.ps1` in un editor di testo e modificare i valori seguenti all'inizio del documento.

    ```powershell
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
    $clusterSizeInNodes = 3
    ```

    Ad esempio, sostituire `'mycluster'` con il nome del cluster che si vuole creare. I valori della firma di accesso condiviso devono corrispondere ai valori usati nei passaggi precedenti durante la creazione di un token dell'account di archiviazione e della firma di accesso condiviso.

    Dopo aver modificato i valori, salvare il file.

2. Aprire un nuovo prompt dei comandi di Azure PowerShell. Se non si ha familiarità con Azure PowerShell o non è stato installato, vedere [Install and configure Azure PowerShell][powershell] (Installare e configurare Azure PowerShell).

1. Dal prompt dei comandi usare il comando seguente per eseguire l'autenticazione alla sottoscrizione di Azure:

    ```powershell
    Login-AzureRmAccount
    ```

    Quando richiesto, accedere con l'account associato alla sottoscrizione di Azure.

    Se l'account è associato a più sottoscrizioni di Azure, può essere necessario usare `Select-AzureRmSubscription` per selezionare la sottoscrizione da usare.

4. Dal prompt dei comandi, passare alla directory `CreateCluster` che contiene il file HDInsightSAS.ps1. Usare quindi il comando seguente per eseguire lo script

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Durante l'esecuzione dello script, mentre vengono creati il gruppo di risorse e gli account di archiviazione, l'output viene registrato nel prompt di PowerShell. Viene chiesto di immettere l'utente HTTP per il cluster HDInsight. Questo account viene usato per proteggere l'accesso HTTP/s al cluster.

    Se si sta creando un cluster basato su Linux, viene chiesto di specificare anche un nome e una password per l'account utente SSH. Questo account viene usato per accedere in remoto al cluster.

   > [!IMPORTANT]
   > Quando vengono richiesti un nome e una password per HTTP/S o SSH, è necessario fornire una password che soddisfi i criteri seguenti:
   >
   > * La lunghezza non può essere inferiore a 10 caratteri
   > * Deve contenere almeno una cifra
   > * Deve contenere almeno un carattere non alfanumerico
   > * Deve contenere almeno una lettera maiuscola o minuscola

Il completamento dello script richiede in genere circa 15 minuti. Se lo script viene completato senza errori, il cluster è stato creato.

### <a name="use-the-sas-with-an-existing-cluster"></a>Usare la firma di accesso condiviso con un cluster esistente

Se è disponibile un cluster basato su Linux esistente, è possibile aggiungere la firma di accesso condiviso alla configurazione del **core-site** seguendo questa procedura:

1. Aprire l'interfaccia utente Web di Ambari per il cluster. L'indirizzo di questa pagina è https://NOMECLUSTER.azurehdinsight.net. Quando richiesto, eseguire l'autenticazione al cluster con il nome amministratore (admin) e la password usati durante la creazione del cluster.

2. Nel lato sinistro dell'interfaccia utente Web di Ambari selezionare **HDFS** e quindi selezionare la scheda **Configs** al centro della pagina.

3. Selezionare la scheda **Advanced** e scorrere fino alla sezione **Custom core-site**.

4. Espandere la sezione **Custom core-site**, quindi scorrere fino alla fine e selezionare il collegamento **Add property**. Usare i valori seguenti per i campi **Key** e **Value**:

   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Value**: la firma di accesso condiviso restituita dall'applicazione C# o Python eseguita in precedenza

     Sostituire **CONTAINERNAME** con il nome del contenitore usato con l'applicazione C# o della firma di accesso condiviso. Sostituire **STORAGEACCOUNTNAME** con il nome dell'account di archiviazione usato.

5. Fare clic sul pulsate **Add** per salvare la chiave e il valore, quindi fare clic sul pulsante **Save** per salvare le modifiche alla configurazione. Quando richiesto, aggiungere una descrizione della modifica, ad esempio "aggiunta di accesso alle risorse di archiviazione per le firme di accesso condiviso", e quindi fare clic su **Save** (Salva).

    Al termine delle modifiche, fare clic su **OK** .

   > [!IMPORTANT]
   > Perché le modifiche siano effettive, è necessario riavviare diversi servizi.

6. Nell'interfaccia utente Web di Ambari selezionare **HDFS** dall'elenco a sinistra e quindi selezionare **Restart All** dall'elenco a discesa **Service Actions** a destra. Quando richiesto, selezionare **Turn on maintenance mode** e quindi selezionare __Conform Restart All".

    Ripetere il processo per MapReduce2 e YARN.

7. Dopo il riavvio di questi servizi, selezionarli uno alla volta e disabilitare la modalità di manutenzione dall'elenco a discesa **Service Actions** (Azioni servizio).

## <a name="test-restricted-access"></a>Testare l'accesso limitato

Per verificare che l'accesso sia effettivamente limitato, usare i metodi seguenti:

* Per i cluster HDInsight **basati su Windows** , usare Desktop remoto per connettersi al cluster. Per altre informazioni, vedere [Connettersi a HDInsight con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    Dopo aver stabilito la connessione, usare l'icona della **riga di comando di Hadoop** sul desktop per aprire il prompt dei comandi.

* Per i cluster HDInsight **basati su Linux** , usare SSH per connettersi al cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo aver stabilito la connessione al cluster, usare la procedura seguente per verificare che nell'account di archiviazione della firma di accesso condiviso sia solo possibile leggere ed elencare gli elementi:

1. Per elencare il contenuto del contenitore, usare il comando seguente dal prompt: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Sostituire **SASCONTAINER** con il nome del contenitore creato per l'account di archiviazione della firma di accesso condiviso. Sostituire **SASACCOUNTNAME** con il nome dell'account di archiviazione usato per la firma di accesso condiviso.

    L'elenco include il file caricato quando sono stati creati il contenitore e la firma di accesso condiviso.

2. Usare il comando seguente per verificare che sia possibile leggere il contenuto del file. Sostituire **SASCONTAINER** e **SASACCOUNTNAME** come indicato nel passaggio precedente. Sostituire **FILENAME** con il nome del file visualizzato nel comando precedente:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Verrà visualizzato il contenuto del file.

3. Usare il comando seguente per scaricare il file nel file system locale:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Il file verrà scaricato in un file locale denominato **testfile.txt**.

4. Usare il comando seguente per caricare il file locale in un nuovo file denominato **testupload.txt** nella risorsa di archiviazione della firma di accesso condiviso:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Verrà visualizzato un messaggio simile al testo seguente:

        put: java.io.IOException

    Questo errore si verifica perché il percorso di archiviazione è di sola lettura+elenco. Usare il comando seguente per inserire i dati nella risorsa di archiviazione predefinita per il cluster, accessibile in scrittura:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

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

**Causa**: questo errore può verificarsi se si usa una password per l'utente admin/HTTP per il cluster oppure, nei cluster basati su Linux, per l'utente SSH.

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

[powershell]: /powershell/azureps-cmdlets-docs

