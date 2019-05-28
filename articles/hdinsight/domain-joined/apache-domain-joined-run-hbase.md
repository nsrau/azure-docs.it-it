---
title: Configurare i criteri di Apache HBase in HDInsight con Enterprise Security Package - Azure
description: Informazioni su come configurare i criteri di Apache Ranger per HBase in Azure HDInsight con Enterprise Security Package.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: tutorial
ms.date: 02/01/2019
ms.openlocfilehash: d1f2a2b24e6f1856d021911e6f2e9c107bd38b72
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926104"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Esercitazione: Configurare i criteri di Apache HBase in HDInsight con Enterprise Security Package

Informazioni su come configurare i criteri di Apache Ranger per i cluster Apache HBase Enterprise Security Package (ESP). Gli ESP cluster sono connessi a un dominio che consente agli utenti di eseguire l'autenticazione con le credenziali di dominio. In questa esercitazione vengono creati due criteri Ranger per limitare l'accesso a diverse famiglie di colonne in una tabella HBase.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare utenti del dominio
> * Creazione dei criteri di Ranger
> * Creare tabelle in un cluster HBase
> * Testare i criteri di Ranger

## <a name="before-you-begin"></a>Prima di iniziare

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).

* Accedere al [portale di Azure](https://portal.azure.com/).

* Creare un [cluster HDInsight HBase con Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Connettersi all'interfaccia utente di amministrazione di Apache Ranger

1. Da un browser, connettersi all'interfaccia utente di amministrazione di Ranger usando l'URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Ricordarsi di sostituire `<ClusterName>` con il nome del cluster HBase.

    > [!NOTE]  
    > Le credenziali di Ranger non sono le stesse usate del cluster Hadoop. Per evitare che i browser usino credenziali memorizzate nella cache di Hadoop, usare una nuova finestra del browser InPrivate per connettersi all'interfaccia utente di Ranger Admin.

2. Accedere usando le credenziali di amministratore di Azure Active Directory (AD). Le credenziali di amministratore di Azure AD non sono le stesse del cluster HDInsight o del nodo SSH di Linux HDInsight.

## <a name="create-domain-users"></a>Creare utenti del dominio

Vedere [Creare un cluster HDInsight con Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds) per informazioni su come creare gli utenti di dominio **sales_user1** e **marketing_user1**. In uno scenario di produzione, gli utenti del dominio provengono dal tenant di Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Creare tabelle HBase e importare dati di esempio

È possibile usare SSH per connettersi ai cluster HBase e usare la [shell di Apache HBase](https://hbase.apache.org/0.94/book/shell.html) per creare tabelle HBase, inserire dati ed eseguire query sui dati. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Per usare la shell HBase

1. In SSH eseguire il comando HBase seguente:
   
    ```bash
    hbase shell
    ```

2. Creare una tabella HBase `Customers` con famiglie a due colonne: `Name` e `Contact`.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Inserire alcuni dati:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. Visualizzare il contenuto della tabella:
    
    ```hbaseshell
    scan 'Contacts'
    ```
    ![Shell HBase Hadoop di HDInsight](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Creazione dei criteri di Ranger

Creare un criterio di Ranger per **sales_user1** e **marketing_user1**.

1. Aprire l’**interfaccia utente di amministrazione di Ranger**. Fai clic su **\<ClusterName>_hbase** in **HBase**.

   ![Interfaccia utente di amministrazione di Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. La schermata **List of Policies** (Elenco di criteri) mostrerà tutti i criteri Ranger creati per questo cluster. Potrebbe essere presente un criterio preconfigurato. Fare clic su **Add New Policy** (Aggiungi nuovi criteri).

    ![Creazione di un criterio nell'interfaccia utente di amministrazione di Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. Nella schermata **Create Policy** (Crea criterio) inserire i valori seguenti:

   |**Impostazione**  |**Valore consigliato**  |
   |---------|---------|
   |Nome criterio  |  sales_customers_name_contact   |
   |HBase Table (Tabella HBase)   |  Clienti |
   |HBase Column-family (Famiglia di colonne HBase)   |  Nome, contatto |
   |HBase Column (Colonna HBase)   |  * |
   |Select Group (Seleziona gruppo)  | |
   |Seleziona utente  | sales_user1 |
   |Autorizzazioni  | Lettura |

   I seguenti caratteri jolly possono essere inclusi nel nome dell'argomento:

   * `*` indica zero o più occorrenze di caratteri.
   * `?` indica un singolo carattere.

   ![Creazione di un criterio nell'interfaccia utente di amministrazione di Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Attendere alcuni istanti per eseguire la sincronizzazione con Azure AD, se un utente di dominio non viene popolato automaticamente per **Seleziona utente**.

4. Fare clic su **Aggiungi** per salvare il criterio.

5. Fare clic su **Aggiungi nuovo criterio**, quindi immettere i valori seguenti:

   |**Impostazione**  |**Valore consigliato**  |
   |---------|---------|
   |Nome criterio  |  marketing_customers_contact   |
   |HBase Table (Tabella HBase)   |  Clienti |
   |HBase Column-family (Famiglia di colonne HBase)   |  Contatto |
   |HBase Column (Colonna HBase)   |  * |
   |Select Group (Seleziona gruppo)  | |
   |Seleziona utente  | marketing_user1 |
   |Autorizzazioni  | Lettura |

   ![Creazione di un criterio nell'interfaccia utente di amministrazione di Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Fare clic su **Aggiungi** per salvare il criterio.

## <a name="test-the-ranger-policies"></a>Testare i criteri di Ranger

In base ai criteri Ranger configurati, **sales_user1** può visualizzare tutti i dati per le colonne in entrambe le famiglie di colonne `Name` e `Contact`. **marketing_user1** può solo visualizzare i dati nella famiglia di colonne `Contact`.

### <a name="access-data-as-salesuser1"></a>Accedere ai dati come sales_user1

1. Aprire una nuova connessione SSH al cluster. Eseguire questo comando per accedere al cluster:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Usare il comando kinit per passare al contesto dell'utente desiderato.

   ```bash
   kinit sales_user1
   ```

2. Aprire la shell hbase e analizzare la tabella `Customers`.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Si noti che sales_user può visualizzare tutte le colonne della tabella `Customers`, incluse le due colonne della famiglia di colonne `Name` nonché le cinque colonne nella famiglia di colonne `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketinguser1"></a>Accedere ai dati come marketing_user1

1. Aprire una nuova connessione SSH al cluster. Eseguire questo comando per accedere come **marketing_user1**:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Usare il comando kinit per passare al contesto dell'utente desiderato.

   ```bash
   kinit marketing_user1
   ```

2. Aprire la shell hbase e analizzare la tabella `Customers`:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

3. Si noti che marketing_user può solo visualizzare le cinque colonne della famiglia di colonne `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

9. Visualizzare gli eventi di accesso di controllo dall'interfaccia utente di Ranger.

   ![Controllo dei criteri di Ranger dell'interfaccia utente](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il cluster HBase creato con i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella casella **Ricerca** in alto digitare **HDInsight**. 
1. Selezionare **Cluster HDInsight** in **Servizi**.
1. Nell'elenco di cluster HDInsight visualizzato, fare clic su **...** accanto al cluster creato per questa esercitazione. 
1. Fare clic su **Elimina**. Fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione ad Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
