<properties 
	pageTitle="Personalizzare i cluster Hadoop per Advanced Analytics Process and Technology | Microsoft Azure"
	description="Moduli di Python più diffusi resi disponibili nei cluster personalizzati Hadoop di Azure HDInsight."
	services="machine-learning"
	documentationCenter=""
	authors="hangzh-msft"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="hangzh;bradsev"/>

# Personalizzare i cluster Hadoop di Azure HDInsight per Advanced Analytics Process and Technology

Questo articolo descrive come personalizzare un cluster Hadoop di HDInsight mediante l'installazione di Anaconda a 64 bit (Python 2.7) in ogni nodo quando viene eseguito il provisioning del cluster nel servizio HDInsight. Questa personalizzazione prepara il cluster da usare con Advanced Analytics Process and Technology (ADAPT) in Azure Machine Learning. L'articolo illustra inoltre come accedere al nodo head per inviare i processi personalizzati al cluster.

Questa personalizzazione rende molti moduli Python comuni inclusi in Anaconda facilmente disponibili per l'uso nelle funzioni definite dall'utente (UDF) progettate per elaborare i record di Hive nel cluster. Per istruzioni sulle procedure usate in questo scenario, vedere [Invio di query Hive ai cluster Hadoop di HDInsight nel processo di analisi scientifica dei dati cloud](machine-learning-data-science-hive-queries.md).


## <a name="customize"></a>Personalizzare i cluster Hadoop di Azure HDInsight

Per creare un cluster Hadoop di HDInsight personalizzato, gli utenti devono accedere al [**portale di gestione di Azure**](https://manage.windowsazure.com/), fare clic su **Nuovo** nell'angolo inferiore sinistro, quindi selezionare SERVIZI DATI -> HDINSIGHT -> **CREAZIONE PERSONALIZZATA** per visualizzare la finestra **Dettagli cluster**.

![Creare un'area di lavoro][1]

Immettere il nome del cluster da creare nella pagina 1 della configurazione e accettare i valori predefiniti per gli altri campi. Fare clic sulla freccia per passare alla pagina di configurazione successiva.

![Creare un'area di lavoro][2]

Nella pagina 2 della configurazione, immettere il numero di **NODI DEI DATI**, selezionare **RETE LOCALE/VIRTUALE**, quindi selezionare le dimensioni del **NODO HEAD** e del **NODO DATI**. Fare clic sulla freccia per passare alla pagina di configurazione successiva.

>[AZURE.NOTE]La **RETE LOCALE/VIRTUALE** deve essere corrispondere all'area dell'account di archiviazione che verrà usato per il cluster Hadoop di HDInsight. In caso contrario, nella quarta pagina della configurazione, l'account di archiviazione che si desidera usare non verrà visualizzato nell'elenco a discesa **NOME ACCOUNT**.

![Creare un'area di lavoro][3]

Nella pagina di configurazione 3, fornire un nome utente e una password per il cluster Hadoop di HDInsight. **Non** selezionare _Immettere metastore Hive/Oozie_. Quindi, fare clic sulla freccia per passare alla pagina di configurazione successiva.

![Creare un'area di lavoro][4]

Nella pagina di configurazione 4, specificare il nome dell'account di archiviazione, il contenitore predefinito del cluster Hadoop di HDInsight. Se gli utenti selezionano _Crea contenitore predefinito_ nell'elenco a discesa **CONTENITORE PREDEFINITO**, verrà creato un contenitore con lo stesso nome del cluster. Fare clic sulla freccia per passare all'ultima pagina di configurazione.

![Creare un'area di lavoro][5]

Nell'ultima pagina di configurazione **Azioni script**, fare clic sul pulsante **aggiungi script azione** e compilare i campi di testo con i valori seguenti.
 
* **NOME**: qualsiasi stringa con il nome dello script azione 
* **TIPO DI NODO**: selezionare **Tutti i nodi** 
* **URI SCRIPT** - **http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1*
	* *publicscripts* è un contenitore pubblico nell'account di archiviazione 
	* *getgoing* viene usato per condividere file di script di PowerShell per facilitare agli utenti l'uso di Azure 
* **PARAMETRI**: lasciare vuoto

Infine, fare clic sul segno di spunta per avviare la creazione del cluster Hadoop di HDInsight personalizzato.

![Creare un'area di lavoro][6]

## <a name="headnode"></a> Accedere al nodo head del cluster Hadoop

Gli utenti devono abilitare l'accesso remoto al cluster Hadoop in Azure prima di poter accedere al nodo head del cluster Hadoop tramite RDP.

1. Accedere al [**portale di gestione di Azure**](https://manage.windowsazure.com/), selezionare **HDInsight** a sinistra, selezionare il cluster Hadoop nell'elenco dei cluster, fare clic sulla scheda **CONFIGURAZIONE** e quindi fare clic sull'icona **ABILITA MODALITÀ REMOTA** nella parte inferiore della pagina.
	
	![Creare un'area di lavoro][7]

2. Nella finestra **Configura desktop remoto** compilare i campi NOME UTENTE e PASSWORD e quindi selezionare la data di scadenza dell'accesso remoto. Quindi fare clic sul segno di spunta per abilitare l'accesso remoto al nodo head del cluster Hadoop.
	
	>[AZURE.NOTE]
	>
	>1. Il nome utente e la password per l'accesso remoto non sono il nome utente e la password usati per la creazione del cluster Hadoop. Si tratta di un set separato di credenziali
	>
	>2. La data di scadenza dell'accesso remoto non deve superare i 7 giorni dalla data corrente.

	![Creare un'area di lavoro][8]

3. Dopo aver abilitato l'accesso remoto, fare clic su **CONNETTI** nella parte inferiore della pagina per accedere in remoto al nodo head. Si accede al nodo head del cluster Hadoop immettendo le credenziali per l'utente di accesso remoto specificato in precedenza.

	 ![Creare un'area di lavoro][9]

I passaggi successivi del processo di analisi avanzata dei dati sono illustrati in [Guida alla formazione: Elaborazione dati avanzata in Azure](machine-learning-data-science-advanced-data-processing.md) e possono includere le procedure per lo spostamento, l'elaborazione e il campionamento dei dati in HDInsight in preparazione dell'apprendimento dei dati con Azure Machine Learning.

Vedere [Invio di query Hive ai cluster Hadoop di HDInsight nel processo di analisi scientifica dei dati cloud](machine-learning-data-science-process-hive-tables.md) per istruzioni sull'accesso ai moduli di Python inclusi in Anaconda dal nodo head del cluster nelle funzioni definite dall'utente (UDF) che consentono di elaborare i record di Hive archiviati nel cluster.

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png

 

<!---HONumber=September15_HO1-->