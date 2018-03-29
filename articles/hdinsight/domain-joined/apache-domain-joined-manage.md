---
title: Gestire cluster HDInsight aggiunti al dominio - Azure | Microsoft Docs
description: Come configurare i cluster HDInsight aggiunti al dominio
services: hdinsight
documentationcenter: ''
author: bprakash
manager: jhubbard
editor: cgronlun
tags: ''
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2018
ms.author: bhanupr
ms.openlocfilehash: 44202541557a7513e0068f52289a637f6e48f43f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Gestione dei cluster HDInsight aggiunti al dominio
Informazioni su utenti e ruoli nei cluster HDInsight aggiunti al dominio e su come gestire i cluster HDInsight aggiunti al dominio.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Accedere al cluster con il pacchetto di sicurezza aziendale.

Il pacchetto di sicurezza aziendale (noto in precedenza come HDInsight Premium) fornisce l'accesso multiutente al cluster, con l'autenticazione eseguita da Active Directory e l'autorizzazione concessa da Apache Ranger e ACL di archiviazione (ACL ADLS). L'autorizzazione fornisce limiti sicuri tra più utenti e consente solo agli utenti con privilegi di accedere ai dati in base ai criteri di autorizzazione.

La sicurezza e l'isolamento degli utenti sono importanti per un cluster HDInsight con il pacchetto di sicurezza aziendale. Per soddisfare questi requisiti, l'accesso SSH al cluster con il pacchetto di sicurezza aziendale è bloccato. La tabella seguente mostra i metodi di accesso consigliati per ogni tipo di cluster:

|Carico di lavoro|Scenario|Metodo di accesso|
|--------|--------|-------------|
|Hadoop|Hive - query/processi interattivi |<ul><li>[Beeline](#beeline)</li><li>[Vista di Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Strumenti di Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Query/processi interattivi, PySpark interattivo|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin con Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Vista di Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Strumenti di Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Scenari batch - Spark-submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactive Query (LLAP)|Interattività|<ul><li>[Beeline](#beeline)</li><li>[Vista di Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Strumenti di Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Qualsiasi|Installazione applicazione personalizzata|<ul><li>[Azioni Script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|


L'uso delle API standard aiuta dal punto di vista della sicurezza. Si ottengono inoltre i vantaggi seguenti:

1.  **Gestione**: è possibile gestire il codice e automatizzare i processi usando API standard, come Livy, HS2 e così via.
2.  **Controllo**: SSH non consente di controllare gli utenti che si sono connessi al cluster tramite SSH. Questo non avviene con i processi costruiti tramite endpoint standard, che vengono eseguiti nel contesto dell'utente. 



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














## <a name="users-of-domain-joined-hdinsight-clusters"></a>Utenti dei cluster HDInsight aggiunti al dominio
Un cluster HDInsight non aggiunto al dominio dispone di due account utente creati durante la creazione del cluster:

* **Amministratore Ambari**: questo account è denominato anche *Utente Hadoop* o *Utente HTTP*. Può essere usato per accedere ad Ambari all'indirizzo https://&lt;nomecluster>.azurehdinsight.net. Può essere usato anche per eseguire query sulle viste Ambari, eseguire i processi tramite strumenti esterni (ad esempio PowerShell, Templeton, Visual Studio) ed eseguire l'autenticazione con Hive ODBC Driver e gli strumenti di business intelligence (ad esempio Excel, PowerBI o Tableau).

Un cluster HDInsight aggiunto a un dominio ha tre nuovi utenti, oltre all'amministratore di Ambari.

* **Amministrazione Ranger**: questo account è l'account di amministratore Ranger locale di Apache. Non si tratta di un utente del dominio di Active Directory. Questo account può essere usato per configurare criteri e creare altri utenti amministratori o amministratori con delega (i quali potranno gestire i criteri). Per impostazione predefinita, il nome utente è *admin* e la password corrisponde alla password dell'amministratore Ambari. La password può essere aggiornata dalla pagina delle impostazioni di Ranger.
* **Utente del dominio di amministrazione cluster**: questo account corrisponde a un utente del dominio di Active Directory designato come amministratore del cluster Hadoop che include Ambari e Ranger. Durante la creazione del cluster, è necessario fornire le credenziali dell'utente. L'utente dispone dei privilegi seguenti:

  * Aggiunta di computer al dominio e posizionamento degli stessi all'interno dell'unità organizzativa specificata in fase di creazione del cluster.
  * Creazione delle entità di servizio nell'unità organizzativa specificata in fase di creazione del cluster.
  * Creazione delle voci DNS inverse.

    Tenere presente che questi privilegi sono gli stessi di altri utenti di Active Directory.

    Il cluster contiene alcuni endpoint (ad esempio, Templeton) non gestiti tramite Ranger e pertanto non sicuri. Questi endpoint sono bloccati per tutti gli utenti, tranne l'utente del dominio di amministrazione del cluster.
* **Normale**: durante la creazione del cluster, è possibile specificare più gruppi di Active Directory. Gli utenti di questi gruppi sono sincronizzati con Ranger e Ambari. Questi utenti sono utenti di dominio e possono accedere solo agli endpoint gestiti da Ranger (ad esempio, Hiveserver2). A questi utenti potranno essere applicati tutti i criteri di controllo e controllo degli accessi in base al ruolo.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Ruoli dei cluster HDInsight aggiunti al dominio
I cluster HDInsight aggiunti al dominio includono i seguenti ruoli:

* Amministratore del cluster
* Operatore del cluster
* Amministratore del servizio
* Operatore del servizio
* Utente del cluster

**Per visualizzare le autorizzazioni associate a questi ruoli**

1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Fare clic su **Roles** (Ruoli) nel menu a sinistra.
3. Fare clic sul punto interrogativo blu per visualizzare le autorizzazioni:

    ![Autorizzazioni dei ruoli dei cluster HDInsight aggiunti al dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Aprire l'interfaccia utente per la gestione di Ambari.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight. Vedere [Elencare e visualizzare i cluster](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Fare clic su **Dashboard** nel menu principale per aprire Ambari.
4. Eseguire l'accesso usando il nome utente e la password di amministratore del cluster.
5. Fare clic sul menu a discesa **Admin** (Amministratore) in alto a destra e quindi fare clic su **Manage Ambari** (Gestisci Ambari).

    ![Gestire Ambari nel cluster HDInsight aggiunto al dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    L'interfaccia utente ha il seguente aspetto:

    ![Interfaccia utente della gestione di Ambari nel cluster HDInsight aggiunto al dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Elencare gli utenti del dominio sincronizzati da Active Directory
1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Scegliere **Users**(Utenti) dal menu a sinistra. Verranno visualizzati tutti gli utenti sincronizzati da Active Directory al cluster HDInsight.

    ![Elenco degli utenti dell'interfaccia per la gestione di Ambari nel cluster HDInsight aggiunto al dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Elencare gli utenti del dominio sincronizzati da Active Directory
1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Scegliere **Groups** (Gruppi) dal menu a sinistra. Verranno visualizzati tutti i gruppi sincronizzati da Active Directory al cluster HDInsight.

    ![Elenco dei gruppi dell'interfaccia utente per la gestione di Ambari nel cluster HDInsight aggiunto al dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurare le autorizzazioni delle viste Hive
1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Fare clic su **Views** (Viste) nel menu a sinistra.
3. Fare clic su **HIVE** per visualizzare i dettagli.

    ![Viste Hive dell'interfaccia utente per la gestione di Ambari nel cluster HDInsight aggiunto al dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Fare clic sul collegamento **Hive View** (Vista Hive) per configurare le viste Hive.
5. Scorrere verso il basso fino alla sezione **Permissions** Autorizzazioni.

    ![Configurare le autorizzazioni delle viste Hive nell'interfaccia utente per la gestione di Ambari nel cluster HDInsight aggiunto al dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Fare clic su **Add User** (Aggiungi utente) o **Add group** (Aggiungi gruppo), quindi specificare gli utenti o i gruppi autorizzati a usare le viste Hive.

## <a name="configure-users-for-the-roles"></a>Configurare i ruoli degli utenti
 Per visualizzare un elenco dei ruoli con le relative autorizzazioni, vedere [Roles of Domain-joined HDInsight clusters](#roles-of-domain---joined-hdinsight-clusters) (Ruoli dei cluster HDInsight aggiunti al dominio).

1. Aprire l'interfaccia utente per la gestione di Ambari.  Vedere [Open the Ambari Management UI](#open-the-ambari-management-ui) (Aprire l'interfaccia utente per la gestione di Ambari).
2. Fare clic su **Roles** (Ruoli) nel menu a sinistra.
3. Fare clic su **Add User** (Aggiungi utente) o su **Add Group** (Aggiungi gruppo) per assegnare utenti e gruppi a ruoli diversi.

## <a name="next-steps"></a>Passaggi successivi
* Per configurare un cluster HDInsight aggiunto al dominio, vedere [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Configurare i cluster HDInsight aggiunti al dominio).
* Per configurare i criteri ed eseguire query Hive, vedere [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configurare criteri Hive per cluster HDInsight aggiunti al dominio).
