---
title: Aggiornamento sul posto di immagini Red Hat Enterprise Linux in Azure
description: Informazioni su come eseguire un aggiornamento sul posto da immagini Red Hat Enterprise 7. x alla versione 8. x più recente.
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 4487aeba72cc71a31871169c0647efbff34ee068
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968622"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux gli aggiornamenti sul posto

Questo articolo fornisce istruzioni su come eseguire un aggiornamento sul posto da Red Hat Enterprise Linux (RHEL) 7 a Red Hat Enterprise Linux 8. Le istruzioni usano lo `leapp` strumento in Azure. Durante l'aggiornamento sul posto, il sistema operativo RHEL 7 esistente viene sostituito dalla versione RHEL 8.

>[!Note] 
> Le offerte di SQL Server Red Hat Enterprise Linux non supportano gli aggiornamenti sul posto in Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Cosa aspettarsi durante l'aggiornamento
Durante l'aggiornamento, il sistema viene riavviato alcune volte. Il riavvio finale aggiorna la macchina virtuale alla versione più recente di RHEL 8. 

Il processo di aggiornamento può richiedere da 20 minuti a 2 ore. Il tempo totale dipende da diversi fattori, ad esempio le dimensioni della macchina virtuale e il numero di pacchetti installati nel sistema.

## <a name="preparations"></a>Preparati
Red Hat e Azure consiglia di usare un aggiornamento sul posto per la transizione di un sistema alla versione principale successiva. 

Prima di iniziare l'aggiornamento, tenere presenti le considerazioni seguenti. 

>[!Important] 
> Eseguire uno snapshot dell'immagine prima di iniziare l'aggiornamento.

* Assicurarsi di usare la versione più recente di RHEL 7. Attualmente, la versione più recente è RHEL 7,9. Se si usa una versione bloccata e non è possibile eseguire l'aggiornamento a RHEL 7,9, seguire [questa procedura per passare a un repository non EUS (Extended Update Support)](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Eseguire il comando seguente per controllare l'aggiornamento e verificare se verrà completato correttamente. Il comando deve generare il file */var/log/leapp/leapp-report.txt* . Questo file spiega il processo, cosa accade e se l'aggiornamento è possibile.

    >[!NOTE]
    > Usare l'account radice per eseguire i comandi in questo articolo. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Verificare che la console seriale sia funzionante. Questa console verrà utilizzata per il monitoraggio durante il processo di aggiornamento.

* Abilitare l'accesso alla radice SSH in *nella/etc/ssh/sshd_config*:
    1. Aprire il file *nella/etc/ssh/sshd_config*.
    1. Cercare `#PermitRootLogin yes`.
    1. Rimuovere il simbolo di cancelletto ( `#` ) per rimuovere il commento dalla stringa.

## <a name="upgrade-steps"></a>Passaggi dell'aggiornamento

Seguire attentamente questi passaggi. È consigliabile provare l'aggiornamento in un computer di test prima di provarlo nelle istanze di produzione.

1. Eseguire un `yum` aggiornamento per recuperare i pacchetti client più recenti.
    ```bash
    yum update -y
    ```

1. Installare i `leapp-client-package`.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Nel [portale di Red Hat](https://access.redhat.com/articles/3664871), ottenere il file *Leapp-data. tar. gz* che contiene *repomap.csv* e *pes-events.jssu*. Estrarre il file *Leapp-data. tar. gz* .
    1. Scaricare il file *Leapp-data. tar. gz* .
    1. Estrarre il contenuto e rimuovere il file. Usare il comando seguente:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Aggiungere un `answers` file per `leapp` .
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Avviare l'aggiornamento.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Al termine del `leapp upgrade` comando, riavviare manualmente il sistema per completare il processo. Il sistema non è disponibile perché viene riavviato un paio di volte. Monitorare il processo usando la console seriale.

1.  Verificare che l'aggiornamento sia stato completato correttamente.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Al termine dell'aggiornamento, rimuovere l'accesso SSH radice:
    1. Aprire il file *nella/etc/ssh/sshd_config*.
    1. Cercare `#PermitRootLogin yes`.
    1. Aggiungere un simbolo di cancelletto ( `#` ) per commentare la stringa.

1. Riavviare il servizio SSHD per applicare le modifiche.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Problemi comuni

Quando il `leapp preupgrade` processo ha esito negativo o se il `leapp upgrade` processo non riesce, si verificano in genere gli errori seguenti:

* **Errore**: non sono state trovate corrispondenze per i modelli di plug-in disabilitati seguenti.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Soluzione**: disabilitare il plug-in Subscription-Manager. Disabilitarlo modificando il file */etc/yum/pluginconf.d/Subscription-Manager.conf* e modificando `enabled` in `enabled=0` .

    Questo errore si verifica quando il plug-in di gestione delle sottoscrizioni `yum` abilitato non viene usato per le `PAYG` macchine virtuali.

* **Errore**: possibili problemi con l'accesso remoto tramite la radice.

    Questo errore può essere visualizzato quando l' `leapp preupgrade` operazione ha esito negativo:

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **Soluzione**: abilitare l'accesso radice in */etc/sshd_config*.

    Questo errore si verifica quando l'accesso SSH radice non è abilitato in */etc/sshd_config*. Per altre informazioni, vedere la sezione [preparations](#preparations) in questo articolo. 


## <a name="next-steps"></a>Passaggi successivi
* Scopri di più sulle [Immagini Red Hat in Azure](./redhat-images.md).
* Altre informazioni su [Red Hat Update Infrastructure](./redhat-rhui.md).
* Scopri di più sull' [offerta RHEL BYOS](./byos.md).
* Per ulteriori informazioni sui processi di aggiornamento sul posto di Red Hat, vedere l'articolo relativo all' [aggiornamento da RHEL 7 a RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) nella documentazione di Red Hat.
* Per ulteriori informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL, vedere [Red Hat Enterprise Linux ciclo di vita](https://access.redhat.com/support/policy/updates/errata) nella documentazione di Red Hat.
