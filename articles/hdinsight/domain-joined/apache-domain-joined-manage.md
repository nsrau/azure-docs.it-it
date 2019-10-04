---
title: Gestire i cluster HDInsight con Enterprise Security Package - Azure
description: Informazioni su come gestire i cluster HDInsight di Azure con Enterprise Security Package.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: b98c62908885bc13cd5f473967cc70709af693d2
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034109"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Gestire i cluster HDInsight con Enterprise Security Package
Informazioni su utenti e ruoli in HDInsight Enterprise Security Package (ESP) e su come gestire i cluster ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Usare VSCode per collegarsi a un cluster aggiunto al dominio

È possibile collegare un cluster normale usando un nome utente Apache Ambari gestito e anche collegare un cluster Apache Hadoop di sicurezza usando un nome utente di dominio (ad esempio: `user1@contoso.com`).

1. Aprire il riquadro comandi premendo **CTRL+MAIUSC+P**, quindi immettere **HDInsight: Link a cluster** (HDInsight: Collega un cluster).

   ![riquadro comandi, collegare un cluster](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Immettere l'URL del cluster HDInsight -> immettere il nome utente -> immettere la password -> selezionare il tipo di cluster -> se la verifica viene superata, vengono visualizzate le informazioni sull'esito positivo.

   ![finestra di dialogo collega passaggi processo cluster](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > Vengono usati il nome utente e la password collegati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster.

3. È possibile visualizzare un cluster collegato usando il comando **List cluster** (Elenca cluster). È ora possibile inviare uno script al cluster collegato.

   ![elencare la verifica dell'output del comando cluster](./media/apache-domain-joined-manage/hdinsight-linked-cluster.png "cluster collegato")

4. È anche possibile scollegare un cluster immettendo **HDInsight: Unlink a cluster** (HDInsight: Scollega un cluster) nel riquadro comandi.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Usare IntelliJ per collegarsi a un cluster aggiunto al dominio

È possibile collegare un cluster normale usando un nome utente Ambari gestito e anche collegare un cluster Hadoop di sicurezza usando un nome utente di dominio (ad esempio: `user1@contoso.com`).

1. Fare clic su **Link a cluster** (Collega un cluster) in **Azure Explorer** (Esplora Azure).

   ![menu di scelta rapida del cluster di collegamento IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Immettere i valori nei campi **Cluster Name** (Nome cluster), **User Name** (Nome utente) e **Password**. Se viene restituito un errore di autenticazione, è necessario verificare il nome utente e la password. Facoltativamente, aggiungere l'account di archiviazione e la chiave di archiviazione e quindi selezionare un contenitore dal contenitore di archiviazione. Le informazioni di archiviazione vengono specificate per Storage Explorer nella struttura ad albero a sinistra.

   ![Finestra di dialogo del cluster di collegamenti di Azure Explorer IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Vengono usati la chiave di archiviazione, il nome utente e la password collegati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster.
   > 
   > ![Account di archiviazione di Azure Explorer in IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

3. È possibile visualizzare un cluster collegato nel nodo di **HDInsight** se le informazioni di input sono corrette. È ora possibile inviare un'applicazione al cluster collegato.

   ![Cluster collegato di Azure Explorer IntelliJ](./media/apache-domain-joined-manage/linked-cluster-intellij.png "IntelliJ del cluster collegato]")

4. È inoltre possibile scollegare un cluster in **Azure Explorer** (Esplora Azure).

   ![IntelliJ del cluster scollegato da Azure Explorer](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Usare Eclipse per collegarsi a un cluster aggiunto al dominio

È possibile collegare un cluster normale usando un nome utente Ambari gestito e anche collegare un cluster Hadoop di sicurezza usando un nome utente di dominio (ad esempio: `user1@contoso.com`).

1. Fare clic su **Link a cluster** (Collega un cluster) in **Azure Explorer** (Esplora Azure).

   ![menu di scelta rapida del cluster di collegamento Eclipse](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Immettere i valori nei campi **Cluster Name** (Nome cluster), **User Name** (Nome utente) e **Password** e quindi fare clic su OK per collegare il cluster. Facoltativamente, immettere l'account di archiviazione e la chiave di archiviazione e quindi selezionare il contenitore di archiviazione per Storage Explorer in modo da usare la visualizzazione struttura ad albero a sinistra.

   ![Finestra di dialogo cluster di collegamento di Azure Explorer Eclipse](./media/apache-domain-joined-manage/link-cluster-dialog1.png)

   > [!NOTE]  
   > Vengono usati la chiave di archiviazione, il nome utente e la password collegati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster.
   > 
   > ![Account di archiviazione di Azure Explorer in Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. È possibile visualizzare un cluster collegato nel nodo di **HDInsight** dopo avere fatto clic su OK, se le informazioni di input sono corrette. È ora possibile inviare un'applicazione al cluster collegato.

   ![Eclipse cluster collegato di Azure Explorer](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. È inoltre possibile scollegare un cluster in **Azure Explorer** (Esplora Azure).
   
   ![Eclipse cluster scollegato da Azure Explorer](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Accedere al cluster con il pacchetto di sicurezza aziendale.

Il pacchetto di sicurezza aziendale (noto in precedenza come HDInsight Premium) fornisce l'accesso multiutente al cluster, con l'autenticazione eseguita da Active Directory e l'autorizzazione concessa da Apache Ranger e ACL di archiviazione (ACL ADLS). L'autorizzazione fornisce limiti sicuri tra più utenti e consente solo agli utenti con privilegi di accedere ai dati in base ai criteri di autorizzazione.

La sicurezza e l'isolamento degli utenti sono importanti per un cluster HDInsight con il pacchetto di sicurezza aziendale. Per soddisfare questi requisiti, l'accesso SSH al cluster con il pacchetto di sicurezza aziendale è bloccato. La tabella seguente mostra i metodi di accesso consigliati per ogni tipo di cluster:

|Carico di lavoro|Scenario|Metodo di accesso|
|--------|--------|-------------|
|Apache Hadoop|Hive - query/processi interattivi  |<ul><li>[Beeline](#beeline)</li><li>[Vista di Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Strumenti di Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Query/processi interattivi, PySpark interattivo|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin con Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Vista di Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Strumenti di Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Scenari batch - Spark-submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactive Query (LLAP)|Interattivo|<ul><li>[Beeline](#beeline)</li><li>[Vista di Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Strumenti di Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Any|Installazione applicazione personalizzata|<ul><li>[Azioni Script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter non è installato/supportato in Enterprise Security Package.

L'uso delle API standard aiuta dal punto di vista della sicurezza. Si ottengono inoltre i vantaggi seguenti:

- **Gestione**: è possibile gestire il codice e automatizzare i processi usando API standard, come Livy, HS2 e così via.
- **Controllo**: SSH non consente di controllare gli utenti che si sono connessi al cluster tramite SSH. Questo non avviene con i processi costruiti tramite endpoint standard, che vengono eseguiti nel contesto dell'utente. 



### <a name="beeline"></a>Usare Beeline 
Installare Beeline nel computer e stabilire la connessione tramite la rete Internet pubblica, usando i parametri seguenti: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Se Beeline è installato in locale e si esegue la connessione tramite una rete virtuale di Azure, usare i parametri seguenti: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Per trovare il nome di dominio completo di un nodo head, usare le informazioni contenute nel documento Gestire i cluster HDInsight mediante l'API REST Ambari.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Utenti dei cluster HDInsight con ESP
Un cluster HDInsight non ESP dispone di due account utente creati durante la creazione del cluster:

* **Amministratore Ambari**: questo account è denominato anche *Utente Hadoop* o *Utente HTTP*. Questo account può essere usato per accedere a Ambari in https://&lt;clustername >. azurehdinsight. NET. Può anche essere usato per eseguire query su viste Ambari, eseguire processi tramite strumenti esterni (ad esempio, PowerShell, Templeton, Visual Studio) ed eseguire l'autenticazione con il driver ODBC hive e gli strumenti di Business Intelligence (ad esempio Excel, Power BI o Tableau).

Un cluster HDInsight con ESP ha tre nuovi utenti, oltre all'amministratore di Ambari.

* **Amministratore Ranger**:  questo account è l'account amministratore locale di Apache Ranger. Non si tratta di un utente del dominio di Active Directory. Questo account può essere usato per configurare criteri e creare altri utenti amministratori o amministratori con delega (i quali potranno gestire i criteri). Per impostazione predefinita, il nome utente è *admin* e la password corrisponde alla password dell'amministratore Ambari. La password può essere aggiornata dalla pagina delle impostazioni di Ranger.
* **Utente del dominio di amministrazione cluster**: questo account corrisponde a un utente del dominio di Active Directory designato come amministratore del cluster Hadoop che include Ambari e Ranger. Durante la creazione del cluster, è necessario fornire le credenziali dell'utente. L'utente dispone dei privilegi seguenti:

  * Aggiunta di computer al dominio e posizionamento degli stessi all'interno dell'unità organizzativa specificata in fase di creazione del cluster.
  * Creazione delle entità di servizio nell'unità organizzativa specificata in fase di creazione del cluster.
  * Creazione delle voci DNS inverse.

    Tenere presente che questi privilegi sono gli stessi di altri utenti di Active Directory.

    Il cluster contiene alcuni endpoint (ad esempio, Templeton) non gestiti tramite Ranger e pertanto non sicuri. Questi endpoint sono bloccati per tutti gli utenti, tranne l'utente del dominio di amministrazione del cluster.
* **Normale**: durante la creazione del cluster è possibile specificare più gruppi di Active Directory. Gli utenti di questi gruppi sono sincronizzati con Ranger e Ambari. Questi utenti sono utenti di dominio e possono accedere solo agli endpoint gestiti da Ranger (ad esempio, Hiveserver2). A questi utenti potranno essere applicati tutti i criteri di controllo e controllo degli accessi in base al ruolo.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Ruoli dei cluster HDInsight con ESP
Enterprise Security Package per HDInsight ha i seguenti ruoli:

* Amministratore del cluster
* Operatore del cluster
* Amministratore del servizio
* Operatore del servizio
* Utente del cluster

**Per visualizzare le autorizzazioni associate a questi ruoli**

1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Fare clic su **Roles** (Ruoli) nel menu a sinistra.
3. Fare clic sul punto interrogativo blu per visualizzare le autorizzazioni:

    ![Autorizzazioni dei ruoli HDInsight con ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Aprire l'interfaccia utente per la gestione di Ambari.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight.
3. Fare clic su **Dashboard** nel menu principale per aprire Ambari.
4. Accedere a Ambari usando il nome utente e la password di dominio di amministrazione del cluster.
5. Fare clic sul menu a discesa **Admin** (Amministratore) in alto a destra e quindi fare clic su **Manage Ambari** (Gestisci Ambari).

    ![ESP HDInsight gestire Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    L'interfaccia utente ha il seguente aspetto:

    ![Interfaccia utente di gestione di Apache Ambari per ESP HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Elencare gli utenti del dominio sincronizzati da Active Directory
1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Scegliere **Users**(Utenti) dal menu a sinistra. Verranno visualizzati tutti gli utenti sincronizzati da Active Directory al cluster HDInsight.

    ![Elenco degli utenti dell'interfaccia utente per la gestione di Ambari in HDInsight per ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Elencare gli utenti del dominio sincronizzati da Active Directory
1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Scegliere **Groups** (Gruppi) dal menu a sinistra. Verranno visualizzati tutti i gruppi sincronizzati da Active Directory al cluster HDInsight.

    ![Elenco dei gruppi dell'interfaccia utente per la gestione di Ambari in HDInsight per ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurare le autorizzazioni delle viste Hive
1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Fare clic su **Views** (Viste) nel menu a sinistra.
3. Fare clic su **HIVE** per visualizzare i dettagli.

    ![Viste Hive dell'interfaccia utente per la gestione di Ambari in HDInsight per ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Fare clic sul collegamento **Hive View** (Vista Hive) per configurare le viste Hive.
5. Scorrere verso il basso fino alla sezione **Permissions** Autorizzazioni.

    ![Configurare le autorizzazioni delle viste Hive nell'interfaccia utente per la gestione di Ambari in HDInsight per ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Fare clic su **Add User** (Aggiungi utente) o **Add group** (Aggiungi gruppo), quindi specificare gli utenti o i gruppi autorizzati a usare le viste Hive.

## <a name="configure-users-for-the-roles"></a>Configurare i ruoli degli utenti
 Per visualizzare un elenco dei ruoli con le relative autorizzazioni, vedere Ruoli dei cluster HDInsight con ESP.

1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Fare clic su **Roles** (Ruoli) nel menu a sinistra.
3. Fare clic su **Add User** (Aggiungi utente) o su **Add Group** (Aggiungi gruppo) per assegnare utenti e gruppi a ruoli diversi.

## <a name="next-steps"></a>Passaggi successivi
* Per configurare un cluster HDInsight con Enterprise Security Package, vedere [Configurare i cluster HDInsight con ESP](apache-domain-joined-configure.md).
* Per configurare i criteri Hive ed eseguire le query Hive, vedere [Configurare criteri Apache Hive per i cluster HDInsight con ESP](apache-domain-joined-run-hive.md).
