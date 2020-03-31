---
title: 'Esercitazione: Configurare le notifiche tramite posta elettronica di Apache Ambari in Azure HDInsight'
description: Questo articolo descrive come usare SendGrid con Apache Ambari per le notifiche tramite posta elettronica.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 21376eb40fb40abe67f7e03d15aabd7d89ea62f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80081900"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Esercitazione: Configurare le notifiche tramite posta elettronica di Apache Ambari in Azure HDInsight

In questa esercitazione verranno configurate le notifiche tramite posta elettronica di Apache Ambari usando SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) semplifica la gestione e il monitoraggio di un cluster HDInsight grazie a un'interfaccia utente Web e a un'API REST di facile utilizzo. Ambari è incluso nei cluster HDInsight e viene usato per monitorare il cluster e apportare modifiche di configurazione. [SendGrid](https://sendgrid.com/solutions/) è un servizio di posta elettronica basato sul cloud che offre recapito affidabile di messaggi di posta elettronica transazionali, scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. I clienti di Azure possono sbloccare 25.000 messaggi di posta elettronica gratuiti ogni mese.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Ottenere il nome utente SendGrid
> * Configurare le notifiche tramite posta elettronica di Apache Ambari

## <a name="prerequisites"></a>Prerequisiti

* Account di posta elettronica SendGrid. Per le istruzioni, vedere [Come inviare messaggi di posta elettronica usando SendGrid con Azure](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email).

* Un cluster HDInsight. Vedere [Creare i cluster di Apache Hadoop nel portale di Azure](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Ottenere il nome utente SendGrid

1. Nel [portale di Azure](https://portal.azure.com) passare alla risorsa SendGrid.

1. Nella pagina Panoramica selezionare **Gestisci** per passare alla pagina Web di SendGrid per l'account.

    ![Panoramica di SendGrid nel portale di Azure](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. Dal menu a sinistra passare al nome dell'account e quindi a **Dettagli account**.

    ![Menu di spostamento del dashboard SendGrid](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Dalla pagina **Dettagli account** registrare il **Nome utente**.

    ![Dettagli account SendGrid](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Configurare la notifica tramite posta elettronica di Ambari

1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts` dove `CLUSTERNAME` è il nome del cluster.

1. Dall'elenco a discesa **Azioni** selezionare **Gestisci notifiche**.

1. Nella finestra **Manage Alert Notifications** (Gestisci notifiche di avviso) selezionare l'icona **+** .

    ![Creazione notifica di avviso Ambari](./media/apache-ambari-email/azure-portal-create-notification.png)

1. Nella finestra di dialogo **Create Alert Notification** (Crea notifica di avviso) specificare le informazioni seguenti:

    |Proprietà |Descrizione |
    |---|---|
    |Nome|Specificare un nome per la notifica.|
    |Gruppi|Configurare in base alle esigenze.|
    |Gravità|Configurare in base alle esigenze.|
    |Descrizione|Facoltativa.|
    |Metodo|Lasciare **POSTA ELETTRONICA**.|
    |Destinatario|Specificare gli indirizzi di posta elettronica per ricevere le notifiche, separati da una virgola.|
    |Server SMTP|`smtp.sendgrid.net`|
    |Porta SMTP|25 o 587 (per le connessioni non crittografate/TLS).|
    |Mittente|Specificare un indirizzo di posta elettronica. Non è necessario che l'indirizzo sia autentico.|
    |Usa autenticazione|Selezionare questa casella di controllo.|
    |Username|Specificare il nome utente SendGrid.|
    |Password|Specificare la password usata durante la creazione della risorsa SendGrid in Azure.|
    |Conferma password|Reimmettere la password.|
    |Avvia TLS|Selezionare questa casella di controllo|

    ![Creazione notifica di avviso Ambari](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Selezionare **Salva**. Si verrà reindirizzati alla finestra **Manage Alert Notifications** (Gestisci notifiche di avviso).

1. Nella finestra **Manage Alert Notifications** (Gestisci notifiche di avviso) selezionare **Chiudi**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come configurare le notifiche tramite posta elettronica di Apache Ambari usando SendGrid. Per altre informazioni Apache Ambari, vedere:

* [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](./hdinsight-hadoop-manage-ambari.md)

* [Creare una notifica di avviso](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
