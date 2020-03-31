---
title: Gestire l'accesso SSH per gli account di dominio in Azure HDInsightManage SSH access for domain accounts in Azure HDInsight
description: Passaggi per gestire l'accesso SSH per gli account Azure AD in HDInsight.Steps to manage SSH access for Azure AD accounts in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472518"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Gestire l'accesso SSH per gli account di dominio in Azure HDInsightManage SSH access for domain accounts in Azure HDInsight

Nei cluster protetti, per impostazione predefinita, a tutti gli utenti del dominio in Servizi di [dominio Active Directory](../../active-directory-domain-services/overview.md) di Azure è consentito [sSH](../hdinsight-hadoop-linux-use-ssh-unix.md) nei nodi head e edge. Questi utenti non fanno parte del gruppo sudoers e non ottengono l'accesso root. L'utente SSH creato durante la creazione del cluster diverrà l'accesso root.

## <a name="manage-access"></a>Gestire l'accesso

Per modificare l'accesso SSH a `/etc/ssh/sshd_config` utenti o gruppi specifici, eseguire l'aggiornamento in ognuno dei nodi.

1. Usare [il comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster, quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aprire `ssh_confi`il file g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Modificare `sshd_config` il file come desiderato. Se si limitano gli utenti a determinati gruppi, gli account locali non possono sSH in tale nodo. Di seguito è riportato solo un esempio di sintassi:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Quindi salvare le modifiche: **Ctrl ì X**, **Y**, **Invio**.

1. Riavviare sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Ripetere i passaggi precedenti per ogni nodo.

## <a name="ssh-authentication-log"></a>Registro di autenticazione SSH

Il registro di `/var/log/auth.log`autenticazione SSH viene scritto in . Se vengono visualizzati errori di accesso tramite SSH per gli account locali o di dominio, sarà necessario esaminare il registro per eseguire il debug degli errori. Spesso il problema potrebbe essere correlato ad account utente specifici e di solito è consigliabile provare altri account utente o SSH utilizzando l'utente SSH predefinito (account locale) e quindi tentare un kinit.

## <a name="ssh-debug-log"></a>Registro di debug SSH

Per abilitare la registrazione dettagliata, `sshd` è `-d` necessario riavviare con l'opzione. Come `/usr/sbin/sshd -d` è anche `sshd` possibile eseguire in una porta personalizzata (ad esempio 2222) in modo che non è necessario arrestare il daemon SSH principale. È inoltre `-v` possibile utilizzare l'opzione con il client SSH per ottenere più registri (visualizzazione lato client degli errori).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight con Enterprise Security Package](./apache-domain-joined-manage.md)
* [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
