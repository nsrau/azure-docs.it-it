---
title: Aggiornamento sul posto di immagini Red Hat Enterprise Linux in Azure
description: Procedura per eseguire l'aggiornamento sul posto da immagini Red Hat Enterprise 7. x alla versione 8. x più recente
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: beede74134affeb3ee0d4bdd20d5da3b4c5e6eda
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566623"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux gli aggiornamenti sul posto

Questo articolo fornisce istruzioni dettagliate su come eseguire un aggiornamento sul posto da Red Hat Enterprise Linux 7 a Red Hat Enterprise Linux 8 usando l'utilità "Leapp" in Azure. Durante l'aggiornamento sul posto, il sistema operativo RHEL 7 esistente viene sostituito dalla versione RHEL 8.

>[!Note] 
> SQL Server sulle offerte di Red Hat Enterprise Linux non supportano l'aggiornamento sul posto in Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Cosa aspettarsi durante l'aggiornamento
Il sistema verrà riavviato alcune volte durante l'aggiornamento ed è normale. L'ultimo riavvio aggiornerà la macchina virtuale in RHEL 8 versione secondaria più recente. 

Il processo di aggiornamento potrebbe richiedere da 20 minuti a un paio di ore, dipende da diversi fattori, ad esempio le dimensioni della macchina virtuale e il numero di pacchetti installati nel sistema.

## <a name="preparations-for-the-upgrade"></a>Preparazioni per l'aggiornamento
L'aggiornamento sul posto è la modalità ufficiale consigliata da Red Hat e Azure per consentire ai clienti di aggiornare il sistema alla versione principale successiva. Prima di eseguire l'aggiornamento, è necessario tenere in considerazione i concetti seguenti. 

>[!Important] 
> Prima di eseguire l'aggiornamento, eseguire uno snapshot dell'immagine.

>[!NOTE]
> I comandi in questo articolo devono essere eseguiti con l'account radice

* Assicurarsi di usare la versione più recente di RHEL 7, che attualmente è RHEL 7,9. Se si usa una versione bloccata e non è possibile eseguire l'aggiornamento a RHEL 7,9, è possibile usare i [passaggi qui per passare a un repository non EUS](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Eseguire il comando seguente per verificare il modo in cui l'aggiornamento è in corso e se verrà completato. Il comando deve generare un file in '/var/log/Leapp/leapp-report.txt' che spieghi il processo e ciò che viene eseguito e se l'aggiornamento è possibile o meno
    ```bash
    leapp preupgrade --no-rhsm
    ```
* Verificare che la console seriale sia funzionante perché consente il monitoraggio durante il processo di aggiornamento.

* Abilitare l'accesso alla radice SSH in `/etc/ssh/sshd_config`
    1. Aprire il file `/etc/ssh/sshd_config`.
    1. Cerca ' #PermitRootLogin sì'
    1. Rimuovi ' #' per rimuovere il commento

## <a name="steps-for-performing-the-upgrade"></a>Passaggi per l'esecuzione dell'aggiornamento

Eseguire con attenzione questi passaggi. È consigliabile provare l'aggiornamento in un computer di test prima delle istanze di produzione.

1. Eseguire un aggiornamento yum per recuperare i pacchetti client più recenti.
    ```bash
    yum update -y
    ```

1. Installare Leapp-client-package.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Usare il file Leapp-data. tar. gz con repomap.csv e pes-events.js, presente nel [portale RedHat](https://access.redhat.com/articles/3664871)ed estrarli. 
    1. Scaricare il file.
    1. Estrarre il contenuto e rimuovere il file usando il comando seguente:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Aggiungere il file ' Answers ' per ' Leapp '.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Eseguire l'aggiornamento di ' Leapp '.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Al termine del `leapp upgrade` comando, riavviare manualmente il sistema per completare il processo. Il sistema verrà riavviato un paio di volte in cui non sarà disponibile. Monitorare il processo usando la console seriale.

1.  Verificare che l'aggiornamento sia stato completato correttamente.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Rimuovere l'accesso SSH radice dopo il completamento dell'aggiornamento.
    1. Aprire il file `/etc/ssh/sshd_config`.
    1. Cerca ' #PermitRootLogin sì'
    1. Aggiungi il ' #' al commento

1. Riavviare il servizio sshd per rendere effettive le modifiche
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>Problemi comuni
Di seguito sono riportate alcune delle istanze comuni che il `leapp preupgrade` `leapp upgrade` processo o potrebbe avere esito negativo.

**Errore: non sono state trovate corrispondenze per i modelli di plug-in disabilitati seguenti**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**Soluzione**\
Disabilitare il plug-in Subscription-Manager modificando il file `/etc/yum/pluginconf.d/subscription-manager.conf` e modificando abilitato in `enabled=0` .

Questo problema è dovuto al fatto che il plug-in yum di gestione delle sottoscrizioni è abilitato, che non viene usato per le macchine virtuali PAYG.

**Errore: possibili problemi con l'accesso remoto tramite la radice** `leapp preupgrade` Potrebbe non riuscire con l'errore seguente:
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
**Soluzione**\
Abilitare l'accesso alla radice in `/etc/sshd_conf` .
Ciò è dovuto al fatto che non è possibile abilitare l'accesso SSH radice in in base `/etc/sshd_conf` alla sezione "[preparazione per l'aggiornamento](#preparations-for-the-upgrade)". 

## <a name="next-steps"></a>Passaggi successivi
* Scopri di più sulle [Immagini Red Hat in Azure](./redhat-images.md).
* Altre informazioni su [Red Hat Update Infrastructure](./redhat-rhui.md).
* Scopri di più sull' [offerta RHEL BYOS](./byos.md).
* Informazioni sui processi di aggiornamento sul posto di Red Hat sono reperibili nella documentazione di Red Hat, [aggiornamento da RHEL 7 a RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).