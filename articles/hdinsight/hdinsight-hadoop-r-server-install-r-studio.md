---
title: Installare RStudio con R Server su HDInsight - Azure | Documentazione Microsoft
description: Come installare RStudio con R Server su HDInsight.
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 416420d855505508735ebd8526e93efdb230ad53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>Installazione di RStudio con R Server su HDInsight

Questo articolo spiega come installare la versione Community (gratuita) di [RStudio Server](https://www.rstudio.com/products/rstudio-server/) nel nodo perimetrale di un cluster usando uno script personalizzato. RStudio Server offre un IDE basato sul browser per l'uso da parte di client remoti ed è ampiamente usato su Linux. Ad oggi esistono più ambienti di sviluppo integrato (IDE) disponibili per R, tra cui:

- [R Tools per Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) di Microsoft 
- [RStudio Server](https://www.rstudio.com/products/rstudio-server/) 
- [StatET](http://www.walware.de/goto/statet), basato su Eclipse, di Walware.

L'installazione di RStudio Server nel nodo perimetrale di un cluster HDInsight offre il vantaggio di un'esperienza IDE completa per lo sviluppo e l'esecuzione di script R con R Server nel cluster. Questa configurazione può essere notevolmente più produttiva rispetto all'uso predefinito della console R.

> [!NOTE]
> La procedura descritta in questo articolo è applicabile solo se si è scelto di non installare RStudio Server Community Edition durante il provisioning del cluster. Se è stato aggiunto durante il provisioning, accedervi facendo clic sul riquadro **Dashboard di R Server** nella voce del portale di Azure per il cluster e quindi sul riquadro **RStudio Server**. 

Se si preferisce usare la versione Pro con licenza commerciale di RStudio Server, è necessario seguire le istruzioni di installazione da [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [!NOTE]
> Se si usa un cluster HDInsight dove R è stato installato con l'[Installazione di script R](hdinsight-hadoop-r-scripts-linux.md), la procedura descritta in questo documento non funzionerà correttamente poiché richiede un R Server basato su un cluster HDInsight.
>
> 

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Azure HDInsight con R Server Per istruzioni, vedere [Introduzione all'uso di R Server in cluster HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Un client SSH. Per distribuzioni Linux e Unix o Macintosh OS X, il comando `ssh` viene offerto con il sistema operativo. Per Windows, si consiglia [Cygwin](http://www.redhat.com/services/custom/cygwin/) con l'[opzione OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU) o [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Installare RStudio nel cluster usando uno script personalizzato

Di seguito sono riportati i passaggi necessari:

1. Identificare il nodo perimetrale del cluster. Per un cluster HDInsight con R Server, di seguito è indicata la convenzione di denominazione per il nodo head e il nodo perimetrale.
   * Nodo head `CLUSTERNAME-ssh.azurehdinsight.net`
   * Nodo perimetrale `CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. SSH nel nodo perimetrale del cluster tramite il modello di denominazione fornito al passaggio 1. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Dopo aver eseguito la connessione, diventare un utente radice nel cluster. Nella sessione SSH usare il comando seguente:

        sudo su -

4. Scaricare lo script personalizzato per installare RStudio. Usare il comando seguente:

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Modificare le autorizzazioni nel file di script personalizzato ed eseguire lo script. Usare il seguente comando:

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Se si usa una password SSH durante la creazione di un cluster HDInsight con R Server, è possibile ignorare questo passaggio e procedere al successivo. Se invece si usa una chiave SSH per creare il cluster, è necessario impostare una password per l'utente SSH. Questa password è necessaria per la connessione a RStudio. Eseguire i comandi seguenti:

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. Quando viene richiesto di immettere la **password Kerberos corrente**, premere **INVIO**.  Si noti che è necessario sostituire `USERNAME` con un utente SSH per il cluster HDInsight. Se la password è impostata correttamente, viene visualizzato il messaggio seguente:

        passwd: password updated successfully

    Chiudere la sessione SSH.

8. Creare un tunnel SSH al cluster tramite il mapping di `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` sul cluster HDInsight per il computer client. Prima di aprire una nuova sessione del browser, è necessario creare un tunnel SSH.

   * Con [Cygwin](http://www.redhat.com/services/custom/cygwin/) aprire in un client Linux o Windows una sessione terminal e usare il comando seguente:

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       Sostituire **USERNAME** con un utente SSH per il cluster HDInsight e **CLUSTERNAME** con il nome del cluster HDInsight.
       È possibile anche usare una chiave SSH anziché una password mediante l'aggiunta di `-i id_rsa_key`.        
   * Se si utilizza un client Windows con PuTTY:

     1. Aprire PuTTY e immettere le informazioni di connessione.
     2. Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine selezionare **Tunnels**.
     3. Fornire le seguenti informazioni nel modulo **Options controlling SSH port forwarding** :

        * **Source port** : la porta del client che si desidera inoltrare. Ad esempio **8787**.
        * **Destination** (Destinazione): la destinazione che deve essere mappata al computer client locale. Ad esempio **localhost:8787**.

            ![Creare un tunnel SSH](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Creare un tunnel SSH")

     4. Fare clic su **Add** per aggiungere le impostazioni, quindi su **Open** per aprire una connessione SSH.
     5. Quando richiesto, accedere al server per stabilire una sessione SSH e abilitare il tunnel.

9. Aprire un Web browser e immettere l'URL seguente, in base alla porta specificata per il tunnel:

        http://localhost:8787/ 

10. Verrà richiesto di immettere il nome utente e la password SSH per connettersi al cluster. Se si usa una chiave SSH durante la creazione del cluster, è necessario immettere la password creata al passaggio 5.

    ![Connettersi a R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Creare un tunnel SSH")

11. Per verificare se l'installazione di RStudio è riuscita, eseguire uno script di test che esegue i processi MapReduce e Spark basati su R nel cluster. Per scaricare lo script di test da eseguire in RStudio, tornare alla console SSH e immettere i comandi seguenti:

    *    Se è stato creato un cluster Hadoop con R, usare questo comando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    Se è stato creato un cluster Spark con R, usare questo comando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. In RStudio verrà visualizzato lo script di test che è stato scaricato. Fare doppio clic sul file per aprirlo, selezionare il contenuto del file e quindi fare clic su **Esegui**. L'output verrà visualizzato nel riquadro **Console**:

   ![Eseguire i test sull'installazione](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Eseguire i test sull'installazione")

In alternativa è possibile digitare `source(testhdi.r)` o `source(testhdi_spark.r)` per eseguire lo script.

## <a name="see-also"></a>Vedere anche

* [Opzioni del contesto di calcolo per R Server in cluster HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opzioni di Archiviazione di Azure per R Server su HDInsight](hdinsight-hadoop-r-server-storage.md)

