---
title: Sincronizzare gli utenti di Azure Active Directory nel cluster HDInsightSynchronize Azure Active Directory users to HDInsight cluster
description: Sincronizzare gli utenti autenticati da Azure Active Directory a un cluster HDInsight.Synchronize authenticated users from Azure Active Directory to an HDInsight cluster.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 299d242c38152db6a471159d1f3d2803598c1832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744855"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Sincronizzare gli utenti di Azure Active Directory con un cluster HDInsight

I [cluster HDInsight Enterprise Security Package (ESP)](hdinsight-domain-joined-introduction.md) possono usare l'autenticazione avanzata con gli utenti di Azure Active Directory (Azure AD) e criteri di *controllo degli accessi in base al ruolo*. Quando si aggiungono utenti e gruppi ad Azure AD, è possibile sincronizzare gli utenti che devono accedere al cluster.

## <a name="prerequisites"></a>Prerequisiti

Se non esiste già, [creare un cluster HDInsight con Enterprise Security Package](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Aggiungere nuovi utenti di Azure AD

Per visualizzare gli host, aprire l'interfaccia utente Web di Ambari. Ogni nodo verrà aggiornato con le nuove impostazioni di aggiornamento automatiche.

1. Dal [portale di Azure](https://portal.azure.com)passare alla directory di Azure AD associata al cluster ESP.

2. Selezionare **Tutti gli utenti** nel menu a sinistra e quindi selezionare **Nuovo utente**.

    ![Utenti e gruppi del portale di Azure](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Completare il modulo del nuovo utente. Selezionare i gruppi creati per l'assegnazione delle autorizzazioni basate sui cluster. In questo esempio, creare un gruppo denominato "HiveUsers", a cui è possibile assegnare nuovi utenti. Le [istruzioni di esempio](hdinsight-domain-joined-configure.md) per la creazione di un cluster ESP includono l'aggiunta di due gruppi, `HiveUsers` e `AAD DC Administrators`.

    ![Riquadro utente del portale di Azure selezionare i gruppiAzure portal user pane select groups](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Selezionare **Crea**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Usare l'API REST di Apache Ambari per sincronizzare gli utenti

I gruppi di utenti specificati durante il processo di creazione del cluster vengono sincronizzati in quel momento. La sincronizzazione degli utenti avviene automaticamente una volta all'ora. Per sincronizzare immediatamente gli utenti o per sincronizzare un gruppo diverso dai gruppi specificati durante la creazione del cluster, usare l'API REST di Ambari.

Il metodo seguente usa POST con l'API REST di Ambari. Per altre informazioni, vedere [Gestire i cluster HDInsight usando l'API REST di Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Usare [il comando ssh](hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo `CLUSTERNAME` con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Dopo l'autenticazione, immettere il comando seguente:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    La risposta dovrebbe avere l'aspetto seguente:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Per visualizzare lo stato di sincronizzazione, eseguire un nuovo comando `curl`:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    La risposta dovrebbe avere l'aspetto seguente:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Questo risultato mostra che lo stato è **COMPLETE**, è stato creato un nuovo utente e all'utente è stata assegnata un'appartenenza. In questo esempio, l'utente viene assegnato al gruppo LDAP sincronizzato "HiveUsers", dato che è stato aggiunto allo stesso gruppo in Azure AD.

    > [!NOTE]  
    > Il metodo precedente sincronizza solo i gruppi di Azure AD specificati nella proprietà **Gruppo utenti di accesso** delle impostazioni di dominio durante la creazione del cluster. Per altre informazioni, vedere la procedura per [creare un cluster HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Verificare l'utente di Azure AD appena aggiunto

Aprire l'[interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md) per verificare che il nuovo utente di Azure AD sia stato aggiunto. Accedere all'interfaccia utente Web **`https://CLUSTERNAME.azurehdinsight.net`** di Ambari accedendo a . Immettere il nome utente e la password dell'amministratore del cluster.

1. Nel dahsboard di Ambari selezionare **Manage Ambari** (Gestisci Ambari) nel menu **admin** (amministratore).

    ![Dashboard Apache Ambari Gestione Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Selezionare **Users** (Utenti) nel gruppo di menu **User + Group Management** (Gestione utenti e gruppi) sul lato sinistro della pagina.

    ![Menu Utenti e gruppi di HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Il nuovo utente dovrebbe essere elencato nella tabella Users (Utenti). Il tipo è impostato su `LDAP` anziché `Local`.

    ![Panoramica della pagina degli utenti di HDInsight aad](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Accedere ad Ambari come il nuovo utente

Quando il nuovo utente (o qualsiasi altro utente di dominio) accede ad Ambari, usa le credenziali complete di nome utente e dominio di Azure AD.  Ambari visualizza un alias dell'utente, ovvero il nome visualizzato dell'utente in Azure AD.
Il nuovo utente di esempio ha il nome utente `hiveuser3@contoso.com`. In Ambari, il nuovo utente viene visualizzato come `hiveuser3` ma l'utente accede ad Ambari come `hiveuser3@contoso.com`.

## <a name="see-also"></a>Vedere anche

* [Configurare criteri Apache Hive in HDInsight con ESP](hdinsight-domain-joined-run-hive.md)
* [Gestire i cluster HDInsight con ESP](hdinsight-domain-joined-manage.md)
* [Autorizzare gli utenti per le viste di Apache Ambari](hdinsight-authorize-users-to-ambari.md)
