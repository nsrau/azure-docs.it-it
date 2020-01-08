---
title: Gestire i cluster di Enterprise Security Package-Azure HDInsight
description: Informazioni su come gestire i cluster HDInsight di Azure con Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435912"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Gestire i cluster HDInsight con Enterprise Security Package

Informazioni su utenti e ruoli in HDInsight Enterprise Security Package (ESP) e su come gestire i cluster ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Usare VSCode per collegarsi a un cluster aggiunto al dominio

È possibile collegare un cluster normale usando un nome utente Apache Ambari gestito e anche collegare un cluster Apache Hadoop di sicurezza usando un nome utente di dominio (ad esempio: `user1@contoso.com`).

1. Aprire [Visual Studio Code](https://code.visualstudio.com/). Verificare che sia installata l'estensione [Spark & hive Tools](../hdinsight-for-vscode.md) .

1. Attenersi alla procedura descritta in [collegare un cluster](../hdinsight-for-vscode.md#link-a-cluster) per Visual Studio Code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Usare IntelliJ per collegarsi a un cluster aggiunto al dominio

È possibile collegare un cluster normale usando un nome utente Ambari gestito e anche collegare un cluster Hadoop di sicurezza usando un nome utente di dominio (ad esempio: `user1@contoso.com`).

1. Aprire IntelliJ IDEA. Verificare che tutti i [prerequisiti](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) siano soddisfatti.

1. Attenersi alla procedura descritta in [collegare un cluster](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) per IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Usare Eclipse per collegarsi a un cluster aggiunto al dominio

È possibile collegare un cluster normale usando un nome utente Ambari gestito e anche collegare un cluster Hadoop di sicurezza usando un nome utente di dominio (ad esempio: `user1@contoso.com`).

1. Aprire Eclipse. Verificare che tutti i [prerequisiti](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) siano soddisfatti.

1. Attenersi alla procedura descritta in [collegare un cluster](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) per Eclipse.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Accedere ai cluster con Enterprise Security Package

Il pacchetto di sicurezza aziendale (noto in precedenza come HDInsight Premium) fornisce l'accesso multiutente al cluster, con l'autenticazione eseguita da Active Directory e l'autorizzazione concessa da Apache Ranger e ACL di archiviazione (ACL ADLS). L'autorizzazione fornisce limiti sicuri tra più utenti e consente solo agli utenti con privilegi di accedere ai dati in base ai criteri di autorizzazione.

La sicurezza e l'isolamento degli utenti sono importanti per un cluster HDInsight con il pacchetto di sicurezza aziendale. Per soddisfare questi requisiti, l'accesso SSH al cluster con il pacchetto di sicurezza aziendale è bloccato. La tabella seguente mostra i metodi di accesso consigliati per ogni tipo di cluster:

|Carico di lavoro|Scenario|Metodo di accesso|
|--------|--------|-------------|
|Apache Hadoop|Hive - query/processi interattivi  |<ul><li>[Beeline](#beeline)</li><li>[Vista di Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Strumenti di Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Query/processi interattivi, PySpark interattivo|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin con Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Vista di Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Strumenti di Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Scenari batch - Spark-submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactive Query (LLAP)|Interattività|<ul><li>[Beeline](#beeline)</li><li>[Vista di Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Strumenti di Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Qualsiasi|Installazione applicazione personalizzata|<ul><li>[Azioni Script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter non è installato/supportato in Enterprise Security Package.

L'uso delle API standard aiuta dal punto di vista della sicurezza. Si ottengono anche i vantaggi seguenti:

- **Gestione**: è possibile gestire il codice e automatizzare i processi usando API standard, come Livy, HS2 e così via.
- **Audit** : con SSH non è possibile controllare, che gli utenti ssh avrebbero al cluster. Questo non avviene con i processi costruiti tramite endpoint standard, che vengono eseguiti nel contesto dell'utente.

### <a name="beeline"></a>Usare Beeline

Installare Beeline nel computer e stabilire la connessione tramite la rete Internet pubblica, usando i parametri seguenti:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Se Beeline è installato in locale e si esegue la connessione tramite una rete virtuale di Azure, usare i parametri seguenti:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Per trovare il nome di dominio completo di un nodo head, usare le informazioni contenute nel documento Gestire i cluster HDInsight mediante l'API REST Ambari.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Utenti dei cluster HDInsight con ESP

Un cluster HDInsight non ESP dispone di due account utente creati durante la creazione del cluster:

- **Amministratore Ambari**: questo account è denominato anche *Utente Hadoop* o *Utente HTTP*. Questo account può essere usato per accedere a Ambari all'`https://CLUSTERNAME.azurehdinsight.net`. Può anche essere usato per eseguire query su viste Ambari, eseguire processi tramite strumenti esterni (ad esempio, PowerShell, Templeton, Visual Studio) ed eseguire l'autenticazione con il driver ODBC hive e gli strumenti di Business Intelligence (ad esempio Excel, Power BI o Tableau).

Un cluster HDInsight con ESP ha tre nuovi utenti, oltre all'amministratore di Ambari.

- **Amministrazione Ranger**: questo account è l'account di amministratore Ranger locale di Apache. Non è un utente di dominio Active Directory. Questo account può essere usato per configurare criteri e creare altri utenti amministratori o amministratori con delega (i quali potranno gestire i criteri). Per impostazione predefinita, il nome utente è *admin* e la password corrisponde alla password dell'amministratore Ambari. La password può essere aggiornata dalla pagina delle impostazioni di Ranger.

- **Utente del dominio di amministrazione cluster**: questo account corrisponde a un utente del dominio di Active Directory designato come amministratore del cluster Hadoop che include Ambari e Ranger. Durante la creazione del cluster, è necessario fornire le credenziali dell'utente. L'utente dispone dei privilegi seguenti:
    - Aggiunta di computer al dominio e posizionamento degli stessi all'interno dell'unità organizzativa specificata in fase di creazione del cluster.
    - Creazione delle entità di servizio nell'unità organizzativa specificata in fase di creazione del cluster.
    - Creazione delle voci DNS inverse.

    Tenere presente che questi privilegi sono gli stessi di altri utenti di Active Directory.

    Sono presenti alcuni endpoint all'interno del cluster (ad esempio, Templeton) che non sono gestiti da Ranger e pertanto non sono sicuri. Questi endpoint sono bloccati per tutti gli utenti, tranne l'utente del dominio di amministrazione del cluster.

- **Normale**: durante la creazione del cluster, è possibile specificare più gruppi di Active Directory. Gli utenti di questi gruppi sono sincronizzati con Ranger e Ambari. Questi utenti sono utenti di dominio e possono accedere solo agli endpoint gestiti da Ranger (ad esempio, Hiveserver2). A questi utenti potranno essere applicati tutti i criteri di controllo e controllo degli accessi in base al ruolo.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Ruoli dei cluster HDInsight con ESP

Enterprise Security Package per HDInsight ha i seguenti ruoli:

- Amministratore del cluster
- Operatore del cluster
- Amministratore del servizio
- Operatore del servizio
- Utente del cluster

**Per visualizzare le autorizzazioni associate a questi ruoli**

1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Nel menu a sinistra selezionare **ruoli**.
3. Selezionare il punto interrogativo blu per visualizzare le autorizzazioni:

    ![Autorizzazioni dei ruoli HDInsight con ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Aprire l'interfaccia utente per la gestione di Ambari.

1. Passare a `https://CLUSTERNAME.azurehdinsight.net/` dove CLUSTERname è il nome del cluster.
1. Accedere a Ambari usando il nome utente e la password di dominio di amministrazione del cluster.
1. Selezionare il menu a discesa **amministratore** nell'angolo superiore destro e quindi selezionare **Gestisci Ambari**.

    ![ESP HDInsight gestire Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    L'interfaccia utente ha il seguente aspetto:

    ![Interfaccia utente di gestione di Apache Ambari per ESP HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Elencare gli utenti del dominio sincronizzati da Active Directory

1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Nel menu a sinistra selezionare **utenti**. Verranno visualizzati tutti gli utenti sincronizzati da Active Directory al cluster HDInsight.

    ![Elenco degli utenti dell'interfaccia utente per la gestione di Ambari in HDInsight per ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Elencare gli utenti del dominio sincronizzati da Active Directory

1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Scegliere **gruppi**dal menu a sinistra. Verranno visualizzati tutti i gruppi sincronizzati da Active Directory al cluster HDInsight.

    ![Elenco dei gruppi dell'interfaccia utente per la gestione di Ambari in HDInsight per ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurare le autorizzazioni delle viste Hive

1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Scegliere **visualizzazioni**dal menu a sinistra.
3. Selezionare **hive** per visualizzare i dettagli.

    ![Viste Hive dell'interfaccia utente per la gestione di Ambari in HDInsight per ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Selezionare il collegamento **visualizzazione hive** per configurare le visualizzazioni hive.
5. Scorrere verso il basso fino alla sezione **Permissions** Autorizzazioni.

    ![Configurare le autorizzazioni delle viste Hive nell'interfaccia utente per la gestione di Ambari in HDInsight per ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Selezionare **Aggiungi utente** o **Aggiungi gruppo**e quindi specificare gli utenti o i gruppi che possono usare le visualizzazioni hive.

## <a name="configure-users-for-the-roles"></a>Configurare i ruoli degli utenti

 Per visualizzare un elenco dei ruoli con le relative autorizzazioni, vedere Ruoli dei cluster HDInsight con ESP.

1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Nel menu a sinistra selezionare **ruoli**.
3. Selezionare **Aggiungi utente** o **Aggiungi gruppo** per assegnare utenti e gruppi a ruoli diversi.

## <a name="next-steps"></a>Passaggi successivi

- Per configurare un cluster HDInsight con Enterprise Security Package, vedere [Configurare i cluster HDInsight con ESP](apache-domain-joined-configure.md).
- Per configurare i criteri Hive ed eseguire le query Hive, vedere [Configurare criteri Apache Hive per i cluster HDInsight con ESP](apache-domain-joined-run-hive.md).
