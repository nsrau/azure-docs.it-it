---
title: Autorizzare gli utenti per le viste di Ambari - Azure HDInsight
description: Come gestire le autorizzazioni utente e di gruppo per Ambari per cluster HDInsight con ESP abilitato.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 976bb44ae81a6c002d2503407a4d74d513a76042
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725270"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorizzare gli utenti per le visualizzazioni di Apache Ambari

I [cluster HDInsight con Enterprise Security Package (ESP) abilitato](./domain-joined/apache-domain-joined-introduction.md) offrono funzionalità di livello aziendale, ad esempio l'autenticazione basata su Azure Active Directory. È possibile [sincronizzare i nuovi utenti](hdinsight-sync-aad-users-to-cluster.md) aggiunti a gruppi di Azure AD a cui è stato concesso l'accesso al cluster, consentendo loro di eseguire determinate azioni. L'utilizzo di utenti, gruppi e autorizzazioni in [Apache Ambari](https://ambari.apache.org/) è supportato sia per cluster HDInsight con ESP sia per cluster HDInsight standard.

Gli utenti di Active Directory possono accedere ai nodi del cluster tramite le proprie credenziali di dominio che consentono anche di autenticare le interazioni tra cluster con antri endpoint approvati, ad esempio [Hue](https://gethue.com/), viste di Ambari, ODBC, JDBC, PowerShell e API REST.

> [!WARNING]  
> Non modificare la password del watchdog Ambari (hdinsightwatchdog) nel cluster HDInsight basato su Linux. Se si modifica la password, non sarà più possibile usare azioni script o eseguire operazioni di ridimensionamento con il cluster.

Se non è già stato fatto, seguire [queste istruzioni](./domain-joined/apache-domain-joined-configure.md) per effettuare il provisioning di un nuovo cluster ESP.

## <a name="access-the-ambari-management-page"></a>Accedere alla pagina di gestione di Ambari

Per accedere alla **pagina di gestione di Ambari**, nell'[interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md) passare a **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Immettere il nome utente e la password di amministratore del cluster definiti durante la creazione del cluster. Nel dahsboard di Ambari selezionare **Manage Ambari** (Gestisci Ambari) nel menu **admin** (amministratore):

![Gestire Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Concedere le autorizzazioni per le viste Apache Hive

Ambari è disponibile con le istanze di viste per [Apache Hive](https://hive.apache.org/) e [Apache TEZ](https://tez.apache.org/), tra gli altri. Per concedere l'accesso a una o più istanze di viste Hive, andare alla **pagina di gestione di Ambari**.

1. Nella pagina di gestione selezionare il collegamento **Views** (Viste) nell'intestazione del menu **Views** (Viste) a sinistra.

    ![Collegamento alle viste](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Nella pagina Views (Viste) espandere la riga **HIVE**. È disponibile una vista Hive predefinita creata quando il servizio Hive viene aggiunto al cluster. È anche possibile creare più istanze di viste Hive in base alle esigenze. Selezionare una vista Hive:

    ![Viste - Vista Hive](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Scorrere verso il basso nella pagina View (Vista). Nella sezione *Permissions* (Autorizzazioni) sono presenti due opzioni per concedere agli utenti del dominio le autorizzazioni sulla vista:

**Grant permission to these users** ![Grant permission to these users](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png) (Concedi autorizzazione a questi utenti)

**Grant permission to these groups** ![Grant permission to these groups](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png) (Concedi autorizzazione a questi gruppi)

4. Per aggiungere un utente, selezionare il pulsante **Add User** (Aggiungi utente).

    * Iniziare a digitare il nome utente. Verrà visualizzato un elenco a discesa dei nomi definiti in precedenza.

    ![Completamento automatico utente](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

    * Selezionare il nome utente o terminarne la digitazione. Per aggiungere il nome utente come un nuovo utente, selezionare il pulsante **New** (Nuovo).

    * Per salvare le modifiche, selezionare la **casella di controllo blu**.

    ![Utente immesso](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

5. Per aggiungere un gruppo, selezionare il pulsante **Add Group** (Aggiungi gruppo).

    * Iniziare a digitare il nome del gruppo. Il processo di selezione di un nome di gruppo esistente o di aggiunta di un nuovo gruppo è analogo a quello per l'aggiunta di utenti.
    * Per salvare le modifiche, selezionare la **casella di controllo blu**.

    ![Gruppo immesso](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

L'aggiunta di nuovi utenti direttamente a una vista è utile quando si desidera assegnare autorizzazioni a un utente a usare tale vista, ma non si desidera che l'utente sia un membro di un gruppo che dispone di autorizzazioni aggiuntive. Per ridurre la quantità di sovraccarico amministrativo, può risultare più semplice assegnare autorizzazioni ai gruppi.

## <a name="grant-permissions-to-apache-tez-views"></a>Concedere le autorizzazioni per le viste Apache TEZ

Le istanze di viste [Apache TEZ](https://tez.apache.org/) consentono agli utenti di monitorare ed eseguire il debug di tutti i processi Tez, inviati da query [Apache Hive](https://hive.apache.org/) e script [Apache Pig](https://pig.apache.org/). È presente una sola istanza di viste Tez predefinita creata quando viene eseguito il provisioning del cluster.

Per assegnare utenti e gruppi a un'istanza di viste Tez, espandere la riga **TEZ** nella pagina Views (Viste), come descritto in precedenza.

![Viste - Vista Tez](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Per aggiungere utenti o gruppi, ripetere i passaggi da 3 a 5 nella sezione precedente.

## <a name="assign-users-to-roles"></a>Assegnare utenti ai ruoli

Esistono cinque ruoli di sicurezza per utenti e gruppi, elencati in ordine decrescente delle autorizzazioni di accesso:

* Amministratore del cluster
* Operatore del cluster
* Amministratore del servizio
* Operatore del servizio
* Utente del cluster

Per gestire i ruoli, andare alla **pagina di gestione di Ambari** e quindi selezionare il collegamento **Roles** (Ruoli) nel gruppo di menu *Clusters* (Cluster) a sinistra.

![Collegamento nel menu dei ruoli](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Per visualizzare l'elenco delle autorizzazioni assegnate a ogni ruolo, fare clic sul punto interrogativo blu accanto all'intestazione di tabella **Roles** (Ruoli) nella pagina relativa.

![Collegamento nel menu dei ruoli](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

In questa pagina sono disponibili due viste diverse che consentono di gestire i ruoli per gli utenti e gruppi, ovvero Block (Blocco) e List (Elenco).

### <a name="block-view"></a>Vista Blocco

La vista Block (Blocco) visualizza ogni ruolo nella propria riga e contiene le opzioni **Assign roles to these users** (Assegna ruoli a questi utenti) e **Assign roles to these groups** (Assegna ruoli a questi gruppi) come descritto in precedenza.

![Vista Blocco dei ruoli](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Vista Elenco

Nella vista List (Elenco) sono disponibili funzionalità di modifica rapida in due categorie, ovvero Users (Utenti) e Groups (Gruppi).

* La categoria Users (Utenti) visualizza un elenco di tutti gli utenti, che consente di selezionare un ruolo per ogni utente nell'elenco a discesa.

    ![Vista Elenco dei ruoli - Utenti](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  La categoria Groups (Gruppi) visualizza tutti i gruppi e il ruolo e il ruolo assegnato a ogni gruppo. In questo esempio l'elenco di gruppi è sincronizzato da gruppi di Azure AD specificati nella proprietà **Access user group** (Gruppo utenti di accessi) delle impostazioni di dominio del cluster. Vedere [Creazione di un cluster HDInsight con ESP abilitato](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Vista Elenco dei ruoli - Gruppi](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Nell'immagine precedente il gruppo "hiveusers" è assegnato il ruolo *Cluster User* (Utente del cluster). Questo è un ruolo di sola lettura che consente agli utenti di tale gruppo di visualizzare le configurazioni del servizio e le metriche del cluster, ma non di modificarle.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Accedere al Ambari come un utente di sola visualizzazione

All'utente di dominio di Azure AD "hiveuser1" sono state assegnate le autorizzazioni sulle viste Hive e Tez. Quando si avvia l'interfaccia utente Web Ambari e si immettono le credenziali di dominio dell'utente (nome utente di Azure AD in formato di indirizzo di posta elettronica e password), l'utente viene reindirizzato alla pagina Views (Viste) di Ambari. A questo punto l'utente può selezionare qualsiasi vista accessibile. L'utente non può altre parti del sito, ad esempio pagine di dashboard, servizi, host, avvisi o amministrazione.

![Utente con autorizzazioni solo per le viste](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Accedere ad Ambari come un utente del cluster

All'utente di dominio Azure AD "hiveuser2" è stato assegnato il ruolo *Cluster User* (Utente del cluster). Questo ruolo consente di accedere al dashboard e a tutte le voci di menu. Un utente del cluster dispone di un numero leggermente minore di opzioni consentite rispetto a un amministratore. L'utente hiveuser2, ad esempio, può visualizzare configurazioni per ognuno dei servizi, ma non modificarle.

![Utente con ruolo di utente del cluster](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare criteri Apache Hive in HDInsight con ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Gestire i cluster HDInsight ESP](./domain-joined/apache-domain-joined-manage.md)
* [Usare la vista Apache Hive con Apache Hadoop in HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Sincronizzare gli utenti di Azure AD con il cluster](hdinsight-sync-aad-users-to-cluster.md)
