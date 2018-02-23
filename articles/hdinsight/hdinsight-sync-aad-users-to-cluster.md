---
title: Sincronizzare gli utenti di Azure Active Directory con un cluster - Azure HDInsight | Microsoft Docs
description: Sincronizzare gli utenti autenticati da Azure Active Directory in un cluster.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: ad1586a6e358dfb1ca2391474ecdd9bee2f6226d
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Sincronizzare gli utenti di Azure Active Directory con un cluster HDInsight

I [cluster HDInsight aggiunti al dominio](hdinsight-domain-joined-introduction.md) possono usare l'autenticazione avanzata con gli utenti di Azure Active Directory (Azure AD) e criteri di *controllo degli accessi in base al ruolo*. Quando si aggiungono utenti e gruppi ad Azure AD, è possibile sincronizzare gli utenti che richiedono l'accesso al cluster.

## <a name="prerequisites"></a>Prerequisiti

Se non è stato già fatto, [creare un cluster HDInsight aggiunto al dominio](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Aggiungere nuovi utenti di Azure AD

Per visualizzare gli host, aprire l'interfaccia utente Web di Ambari. Ogni nodo verrà aggiornato con le nuove impostazioni di aggiornamento automatiche.

1. Nel [portale di Azure](https://portal.azure.com) passare alla directory di Azure AD associata al cluster aggiunto al dominio.

2. Selezionare **Tutti gli utenti** nel menu a sinistra e quindi selezionare **Nuovo utente**.

    ![Riquadro Tutti gli utenti](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Completare il modulo del nuovo utente. Selezionare i gruppi creati per l'assegnazione delle autorizzazioni basate sui cluster. In questo esempio, creare un gruppo denominato "HiveUsers", a cui è possibile assegnare nuovi utenti. Le [istruzioni di esempio](hdinsight-domain-joined-configure.md) per la creazione di un cluster di dominio includono l'aggiunta di due gruppi, `HiveUsers` e `AAD DC Administrators`.

    ![Riquadro per un nuovo utente](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Selezionare **Create**.

## <a name="use-the-ambari-rest-api-to-synchronize-users"></a>Usare l'API REST di Ambari per sincronizzare gli utenti

I gruppi di utenti specificati durante il processo di creazione del cluster vengono sincronizzati in quel momento. La sincronizzazione degli utenti avviene automaticamente una volta all'ora. Per sincronizzare immediatamente gli utenti o per sincronizzare un gruppo diverso dai gruppi specificati durante la creazione del cluster, usare l'API REST di Ambari.

Il metodo seguente usa POST con l'API REST di Ambari. Per altre informazioni, vedere [Gestire i cluster HDInsight usando l'API REST di Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Connettersi al cluster con SSH](hdinsight-hadoop-linux-use-ssh-unix.md). Nel riquadro Panoramica per il cluster nel portale di Azure selezionare il pulsante **Secure Shell (SSH)**.

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Copiare il comando `ssh` visualizzato e incollarlo nel client SSH. Immettere la password utente ssh quando richiesto.

3. Dopo l'autenticazione, immettere il comando seguente:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    La risposta dovrebbe avere l'aspetto seguente:

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. Per visualizzare lo stato di sincronizzazione, eseguire un nuovo comando `curl` usando il valore `href` restituito dal comando precedente:

    ```bash
    curl -u admin:<YOUR PASSWORD> http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1
    ```
    
    La risposta dovrebbe avere l'aspetto seguente:
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
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

5. Questo risultato indica che lo stato è **COMPLETE**, è stato creato un nuovo utente e all'utente è stata assegnata un'appartenenza. In questo esempio, l'utente viene assegnato al gruppo LDAP sincronizzato "HiveUsers", dato che è stato aggiunto allo stesso gruppo in Azure AD.

> [!NOTE]
> Il metodo precedente sincronizza solo i gruppi di Azure AD specificati nella proprietà **Gruppo utenti di accesso** delle impostazioni di dominio durante la creazione del cluster. Per altre informazioni, vedere la procedura per [creare un cluster HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Verificare l'utente di Azure AD appena aggiunto

Aprire l'[interfaccia utente Web di Ambari](hdinsight-hadoop-manage-ambari.md) per verificare che il nuovo utente di Azure AD sia stato aggiunto. Accedere all'interfaccia utente Web di Ambari passando a **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Immettere il nome utente e la password dell'amministratore del cluster.

1. Nel dahsboard di Ambari selezionare **Manage Ambari** (Gestisci Ambari) nel menu **admin** (amministratore).

    ![Gestire Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Selezionare **Users** (Utenti) nel gruppo di menu **User + Group Management** (Gestione utenti e gruppi) sul lato sinistro della pagina.

    ![Voce di menu Users (Utenti)](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Il nuovo utente dovrebbe essere elencato nella tabella Users (Utenti). Il tipo è impostato su `LDAP` anziché `Local`.

    ![Pagina Users (Utenti)](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Accedere ad Ambari come il nuovo utente

Quando il nuovo utente (o qualsiasi altro utente di dominio) accede ad Ambari, usa le credenziali complete di nome utente e dominio di Azure AD.  Ambari visualizza un alias dell'utente, ovvero il nome visualizzato dell'utente in Azure AD. Il nuovo utente di esempio ha il nome utente `hiveuser3@contoso.com`. In Ambari, il nuovo utente viene visualizzato come `hiveuser3` ma l'utente accede ad Ambari come `hiveuser3@contoso.com`.

## <a name="see-also"></a>Vedere anche

* [Configurare criteri Hive in HDInsight aggiunto al dominio](hdinsight-domain-joined-run-hive.md)
* [Gestire i cluster HDInsight aggiunti al dominio](hdinsight-domain-joined-manage.md)
* [Autorizzare gli utenti per le viste di Ambari](hdinsight-authorize-users-to-ambari.md)
