---
title: Gestire l'accesso SSH per gli account di dominio in Azure HDInsight
description: Procedura per gestire l'accesso SSH per gli account Azure AD in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472518"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Gestire l'accesso SSH per gli account di dominio in Azure HDInsight

Nei cluster protetti, per impostazione predefinita, tutti gli utenti del dominio in [Azure AD DS](../../active-directory-domain-services/overview.md) sono autorizzati a eseguire [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) nei nodi head e Edge. Questi utenti non fanno parte del gruppo sudoers e non ottengono l'accesso alla radice. L'utente SSH creato durante la creazione del cluster avrà accesso alla radice.

## <a name="manage-access"></a>Gestire l'accesso

Per modificare l'accesso SSH a utenti o gruppi specifici, aggiornare `/etc/ssh/sshd_config` in ogni nodo.

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERname con il nome del cluster e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aprire il file `ssh_confi`g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Modificare il file di `sshd_config` nel modo desiderato. Se si limitano gli utenti a determinati gruppi, gli account locali non possono eseguire SSH in tale nodo. Di seguito è riportato solo un esempio di sintassi:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Salvare quindi le modifiche: **CTRL + X**, **Y**, **invio**.

1. Riavviare sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Ripetere i passaggi precedenti per ogni nodo.

## <a name="ssh-authentication-log"></a>Log di autenticazione SSH

Il log di autenticazione SSH è scritto nel `/var/log/auth.log`. Se vengono visualizzati errori di accesso tramite SSH per gli account locali o di dominio, sarà necessario esaminare il log per eseguire il debug degli errori. Spesso il problema potrebbe essere correlato a account utente specifici ed è in genere consigliabile provare altri account utente o SSH usando l'utente SSH predefinito (account locale) e quindi provare a kinit.

## <a name="ssh-debug-log"></a>Log di debug SSH

Per abilitare la registrazione dettagliata, sarà necessario riavviare `sshd` con l'opzione `-d`. Analogamente a `/usr/sbin/sshd -d` è anche possibile eseguire `sshd` su una porta personalizzata, ad esempio 2222, in modo da non dover arrestare il daemon SSH principale. È anche possibile usare `-v` opzione con il client SSH per ottenere più log (visualizzazione lato client degli errori).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight con Enterprise Security Package](./apache-domain-joined-manage.md)
* [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
