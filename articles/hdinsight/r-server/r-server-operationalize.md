---
title: Rendere operativo R Server in HDInsight - Azure | Microsoft Docs
description: Informazioni su come rendere operativo R Server in Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 6de6e78d9b4ad68d268b59cff18c75fbdd7be757
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412842"
---
# <a name="operationalize-r-server-cluster-on-azure-hdinsight"></a>Rendere operativo un cluster R Server in Azure HDInsight

Dopo avere usato il cluster R Server in HDInsight per completare la modellazione dei dati, è possibile rendere operativo il modello per eseguire stime. Questo articolo include istruzioni per eseguire questa attività.

## <a name="prerequisites"></a>prerequisiti

* **Un cluster R Server in HDInsight**: per istruzioni, vedere [Introduzione a R Server in HDInsight](r-server-get-started.md).

* **Un client Secure Shell (SSH)**: il client SSH viene usato per connettersi da remoto al cluster HDInsight ed eseguire i comandi direttamente nel cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-r-server-cluster-with-one-box-configuration"></a>Rendere operativo un cluster R Server con una configurazione One-box

1. Accedere tramite SSH al nodo perimetrale.  

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Per istruzioni su come usare SSH con Azure HDInsight, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Passare alla directory della versione corrispondente ed eseguire sudo sulla DLL di .NET: 

    - Per Microsoft R Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Per Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Vengono presentate le opzioni tra cui scegliere. Scegliere la prima opzione, come illustrato nello screenshot seguente, **Configure R Server for Operationalization**.

    ![Opzione per una casella](./media/r-server-operationalize/admin-util-one-box-1.png)

4. Viene ora presentata l'opzione per scegliere come si vuole rendere operativo R Server. Tra le opzioni presentate scegliere la prima immettendo **A**.

    ![Opzione per una casella](./media/r-server-operationalize/admin-util-one-box-2.png)

5. Quando richiesto, immettere due volte la password per un utente amministratore locale.

6. Verranno visualizzati gli output indicanti che l'operazione è riuscita. Viene anche chiesto di selezionare un'altra opzione dal menu. Selezionare E per tornare al menu principale.

    ![Opzione per una casella](./media/r-server-operationalize/admin-util-one-box-3.png)

7. È facoltativamente possibile effettuare controlli diagnostici eseguendo un test di diagnostica, come illustrato di seguito:

    a. Dal menu principale selezionare **6** per eseguire i test diagnostici.

    ![Opzione per una casella](./media/r-server-operationalize/diagnostic-1.png)

    b. Dal menu Diagnostic Tests selezionare **A**. Quando richiesto, immettere la password specificata per l'utente amministratore locale.

    ![Opzione per una casella](./media/r-server-operationalize/diagnostic-2.png)

    c. Verificare che l'output indichi che il valore dell'integrità generale è pass.

    ![Opzione per una casella](./media/r-server-operationalize/diagnostic-3.png)

    d. Tra le opzioni di menu presentate immettere **E** per tornare al menu principale e quindi immettere **8** per uscire dall'utilità di amministrazione.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Ritardi considerevoli quando si utilizza il servizio Web in Spark

Se si riscontrano ritardi considerevoli quando si prova a utilizzare un servizio Web creato con le funzioni mrsdeploy in un contesto di calcolo di Spark, potrebbe essere necessario aggiungere alcune cartelle mancanti. L'applicazione Spark appartiene a un utente chiamato "*rserve2*" quando viene richiamata da un servizio Web usando le funzioni mrsdeploy. Per risolvere questo problema:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


A questo punto la configurazione per la messa in funzione è completata. È ora possibile usare il pacchetto `mrsdeploy` in RClient per connettersi all'operazionalizzazione sul nodo perimetrale e iniziare a usarne le funzionalità, ad esempio l'[esecuzione remota](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) e i [servizi Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). A seconda che il cluster sia configurato o meno su una rete virtuale, potrebbe essere necessario impostare il tunneling di inoltro alla porta tramite l'accesso SSH. Le sezioni seguenti illustrano come configurare questo tunnel.

### <a name="r-server-cluster-on-virtual-network"></a>Cluster R Server nella rete virtuale

Verificare che sia consentito il traffico attraverso la porta 12800 verso il nodo perimetrale. In questo modo è possibile usare tale nodo per la connessione alla funzionalità di messa in funzione.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Se `remoteLogin()` non può connettersi al nodo perimetrale ma è possibile accedere a tale nodo tramite SSH, è necessario verificare se la regola che consente il traffico sulla porta 12800 è stata impostata correttamente. Se il problema persiste, può essere risolto configurando il tunneling di port forwarding tramite SSH. Per istruzioni, vedere la sezione seguente:

### <a name="r-server-cluster-not-set-up-on-virtual-network"></a>Cluster R Server non configurato nella rete virtuale

Se il cluster non è configurato sulla rete virtuale o si riscontrano problemi relativi alla connettività tramite la rete virtuale, è possibile usare il tunneling di port forwarding SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Quando la sessione SSH è attiva, il traffico proveniente dalla porta 12800 del computer viene inoltrato alla porta 12800 del nodo perimetrale tramite la sessione SSH. Assicurarsi di usare `127.0.0.1:12800` nel metodo `remoteLogin()`. Viene così eseguito l'accesso all'operazionalizzazione del nodo perimetrale tramite port forwarding.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Ridimensionare i nodi di calcolo resi operativi nei nodi di lavoro HDInsight

Per ridimensionare i nodi di calcolo, prima si rimuovono le autorizzazioni dei nodi di lavoro e quindi si configurano i nodi di calcolo nei nodi di lavoro con le autorizzazioni rimosse.

### <a name="step-1-decommission-the-worker-nodes"></a>Passaggio 1: Rimuovere le autorizzazioni dei nodi di lavoro

Il cluster R Server non viene gestito tramite YARN. Se è necessario rimuovere le autorizzazioni dei nodi di lavoro, YARN Resource Manager non funziona come previsto perché non riesce a riconoscere le risorse impiegate dal server. Per evitare questa situazione, è consigliabile rimuovere le autorizzazioni dei nodi del ruolo di lavoro prima di ridimensionare i nodi di calcolo.

Seguire questi passaggi per rimuovere le autorizzazioni dei nodi di lavoro:

1. Accedere alla console Ambari del cluster e fare clic sulla scheda **Hosts** (Host).

2. Selezionare i nodi di lavoro (da cui rimuovere le autorizzazioni).

3. Fare clic su **Actions** (Azioni) > **Selected Hosts** (Host selezionati) > **Hosts** (Host) > **Turn ON Maintenance Mode** (Attiva modalità di manutenzione). Ad esempio nell'immagine seguente i nodi selezionati per la rimozione delle autorizzazioni sono wn3 e wn4.  

   ![Rimozione delle autorizzazioni dei nodi del ruolo di lavoro](./media/r-server-operationalize/get-started-operationalization.png)  

* Selezionare **Actions** (Azioni) > **Selected Hosts** (Host selezionati) > **DataNodes** > fare clic su **Decommission** (Rimuovi autorizzazioni).
* Selezionare **Actions** (Azioni) > **Selected Hosts** (Host selezionati) > **NodeManagers** > fare clic su **Decommission** (Rimuovi autorizzazioni).
* Selezionare **Actions** (Azioni) > **Selected Hosts** (Host selezionati) > **DataNodes** > fare clic su **Stop** (Arresta).
* Selezionare **Actions** (Azioni) > **Selected Hosts** (Host selezionati) > **NodeManagers** > fare clic su **Stop** (Arresta).
* Selezionare **Actions** (Azioni) > **Selected Hosts** (Host selezionati) > **Hosts** > fare clic su **Stop All Components** (Arresta tutti i componenti).
* Deselezionare i nodi del ruolo di lavoro e selezionare i nodi head.
* Selezionare **Actions** (Azioni) > **Selected Hosts** (Host selezionati) > "**Hosts** > **Restart All Components** (Riavvia tutti i componenti).

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Passaggio 2: Configurare i nodi di calcolo in ogni nodo di lavoro per il quale è stata rimossa l'autorizzazione

1. Accedere tramite SSH a ogni nodo del ruolo di lavoro per il quale è stata rimossa l'autorizzazione.

2. Eseguire l'utilità di amministrazione usando la DLL pertinente per il proprio cluster R Server. Per R Server 9.1, eseguire il codice seguente:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Immettere **1** per selezionare l'opzione **Configure R Server for Operationalization**.

4. Immettere **C** per selezionare l'opzione `C. Compute node`. Il nodo di calcolo viene configurato sul nodo del ruolo di lavoro.

5. Uscire dall'utilità di amministrazione.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Passaggio 3: Aggiungere i dettagli dei nodi di calcolo nel nodo Web

Dopo che tutti i nodi di lavoro per i quali è stata rimossa l'autorizzazione sono stati configurati per l'esecuzione del nodo di calcolo, tornare al nodo perimetrale e aggiungere gli indirizzi IP di tali nodi di lavoro nella configurazione del nodo Web R Server:

1. Accedere tramite SSH al nodo perimetrale.

2. Eseguire `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

3. Cercare la sezione "Uris" e aggiungere i dettagli relativi alla porta e all'IP del nodo di lavoro.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Passaggi successivi

* [Gestire cluster R Server in HDInsight](r-server-hdinsight-manage.md)
* [Opzioni del contesto di calcolo per il cluster R Server in HDInsight](r-server-compute-contexts.md)
* [Opzioni di Archiviazione di Azure per il cluster R Server in HDInsight](r-server-storage.md)
