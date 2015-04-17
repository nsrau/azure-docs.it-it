<properties 
	pageTitle="Personalizzazione di cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati | Azure" 
	description="Personalizzazione di cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati"
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="hangzh;bradsev" />

# Personalizzazione di cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati

In questo articolo viene descritto come personalizzare un cluster Hadoop di HDInsight mediante l'installazione di Anaconda a 64 bit (Python 2.7) in ogni nodo quando viene eseguito il provisioning del cluster nel servizio HDInsight. Viene inoltre illustrato come accedere al nodo head per inviare i processi personalizzati al cluster.

Questa personalizzazione rende molti moduli Python comuni inclusi in Anaconda facilmente disponibili per l'utilizzo nelle funzioni definite dall'utente (UDF) progettate per elaborare i record di Hive nel cluster. Per istruzioni sulle procedure utilizzate in questo scenario, vedere [Invio di query Hive ai cluster di Hadoop di HDInsight nel processo di analisi scientifica dei dati cloud](machine-learning-data-science-hive-queries.md).

## <a name="customize"></a>Personalizzazione dei cluster Hadoop di Azure HDInsight

Per creare un cluster Hadoop di HDInsight personalizzato, gli utenti devono accedere al [**portale di gestione di Azure**](https://manage.windowsazure.com/), fare clic su **Nuovo** nell'angolo inferiore sinistro, quindi selezionare SERVIZI DATI -> HDINSIGHT -> **CREAZIONE PERSONALIZZATA** per visualizzare la finestra relativa ai **dettagli del cluster**. 

![Create workspace][1]

Immettere il nome del cluster da creare nella pagina 1 della configurazione e accettare i valori predefiniti per gli altri campi. Fare clic sulla freccia per passare alla pagina di configurazione successiva. 

![Create workspace][2]

Nella pagina 2 della configurazione, immettere il numero di **NODI DEI DATI**, selezionare **RETE LOCALE/VIRTUALE**, quindi selezionare le dimensioni del **NODO HEAD** e del **NODO DATI**. Fare clic sulla freccia per passare alla pagina di configurazione successiva.

>[AZURE.NOTE] La **RETE LOCALE/VIRTUALE** deve essere corrispondere all'area dell'account di archiviazione che verrà utilizzato per il cluster Hadoop di HDInsight. In caso contrario, nella quarta pagina della configurazione, l'account di archiviazione che si desidera utilizzare non verrà visualizzato nell'elenco a discesa **NOME ACCOUNT**.

![Create workspace][3]

Nella pagina di configurazione 3, fornite un nome utente e una password per il cluster Hadoop di HDInsight. **Non** selezionare _Immettere metastore Hive/Oozie_. Quindi, fare clic sulla freccia per passare alla pagina di configurazione successiva. 

![Create workspace][4]

Nella pagina di configurazione 4, specificare il nome dell'account di archiviazione, il contenitore predefinito del cluster Hadoop di HDInsight. Se gli utenti selezionano _Crea contenitore predefinito_ nell'elenco a discesa **CONTENITORE PREDEFINITO**, verrà creato un contenitore con lo stesso nome del cluster. Fare clic sulla freccia per passare alla pagina di configurazione successiva.

![Create workspace][5]

Nell'ultima pagina di configurazione **Azioni script**, fare clic sul pulsante per **aggiungere azioni script** e compilare i campi di testo con i seguenti valori.
 
* **NOME** - qualsiasi stringa con il nome dell'azione script. 
* **TIPO DI NODO** - selezionare **Tutti i nodi**. 
* **URI SCRIPT** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
	* *publicscripts* è un contenitore pubblico nell'account di archiviazione 
	* *getgoing* utilizzato per condividere file di script di PowerShell per facilitare agli utenti l'uso di Azure. 
* **PARAMETRI** - (lasciare vuoto)

Infine, fare clic sul segno di spunta per avviare la creazione del cluster Hadoop di HDInsight personalizzato. 

![Create workspace][6]

## <a name="headnode"></a> Accedere al nodo head del cluster Hadoop

Gli utenti devono abilitare l'accesso remoto al cluster Hadoop in Azure prima di poter accedere al nodo head del cluster Hadoop tramite RDP. 

1. Accedere al [**portale di gestione di Azure**](https://manage.windowsazure.com/), selezionare **HDInsight** a sinistra, selezionare il cluster Hadoop nell'elenco dei cluster, fare clic sulla scheda **CONFIGURAZIONE**, quindi fare clic sull'icona **ABILITA MODALITÀ REMOTA** nella parte inferiore della pagina.
	
	![Create workspace][7]

2. Nella finestra **Configura desktop remoto**, compilare i campi NOME UTENTE e PASSWORD, quindi selezionare la data di scadenza dell'accesso remoto. Quindi fare clic sul segno di spunta per abilitare l'accesso remoto al nodo head del cluster Hadoop.
	
	>[AZURE.NOTE] 
	>
	>1. Il nome utente e la password per l'accesso remoto non sono il nome utente e la password utilizzati per la creazione del cluster Hadoop. Si tratta di un set separato di credenziali
	>
	>2. La data di scadenza dell'accesso remoto non deve superare i 7 giorni dalla data corrente.

	![Create workspace][8]

3. Dopo aver abilitato l'accesso remoto, fare clic su **CONNETTI** nella parte inferiore della pagina per accedere in remoto al nodo head. Si accede al nodo head del cluster Hadoop immettendo le credenziali per l'utente di accesso remoto specificato in precedenza.

	 ![Create workspace][9]

Successivamente, vedere [Invio di query Hive ai cluster di Hadoop di HDInsight nel processo di analisi scientifica dei dati cloud](machine-learning-data-science-hive-queries.md) per istruzioni sull'accesso ai moduli di Python inclusi in Anaconda dal nodo head del cluster nelle funzioni definite dall'utente (UDF) che consentono di elaborare i record di Hive archiviati nel cluster.

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png


<!--HONumber=49-->