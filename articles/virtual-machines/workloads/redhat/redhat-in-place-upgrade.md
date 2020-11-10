---
title: Aggiornamento sul posto di immagini Red Hat Enterprise Linux in Azure
description: Procedura per eseguire l'aggiornamento sul posto da immagini Red Hat Enterprise 7. x alla versione 8. x più recente
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447728"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux gli aggiornamenti sul posto

Questo articolo fornisce istruzioni dettagliate su come eseguire un aggiornamento sul posto da Red Hat Enterprise Linux 7 a Red Hat Enterprise Linux 8 usando l'utilità "Leapp" in Azure. Durante l'aggiornamento sul posto, il sistema operativo RHEL 7 esistente viene sostituito dalla versione RHEL 8.

>[!Note] 
> SQL Server sulle offerte di Red Hat Enterprise Linux non supportano l'aggiornamento sul posto in Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Cosa aspettarsi durante l'aggiornamento
Il sistema verrà riavviato alcune volte durante l'aggiornamento ed è normale. L'ultimo riavvio aggiornerà la macchina virtuale in RHEL 8 versione secondaria più recente.

## <a name="preparations-for-the-upgrade"></a>Preparazioni per l'aggiornamento
Gli aggiornamenti sul posto rappresentano la modalità ufficiale consigliata da Red Hat e Azure per consentire ai clienti di eseguire l'aggiornamento del sistema alla versione principale successiva. Prima di eseguire l'aggiornamento, è necessario tenere in considerazione i concetti seguenti. 

>[!Important] 
> Prima di eseguire l'aggiornamento, eseguire uno snapshot dell'immagine.

>[!NOTE]
> I comandi in questo articolo devono essere eseguiti con l'account radice

* Assicurarsi di usare la versione più recente di RHEL 7, che attualmente è RHEL 7,9. Se si usa una versione bloccata e non è possibile eseguire l'aggiornamento a RHEL 7,9, è possibile usare i [passaggi qui per passare a un repository non EUS](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Eseguire il comando seguente per verificare il modo in cui l'aggiornamento è in corso e se verrà completato. Il comando deve generare un file in '/var/log/Leapp/leapp-report.txt' che spieghi il processo e ciò che viene eseguito e se l'aggiornamento è possibile o meno
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>Passaggi per l'esecuzione dell'aggiornamento

Eseguire con attenzione questi passaggi. È consigliabile provare l'aggiornamento in un computer di test prima delle istanze di produzione.

1. Eseguire un aggiornamento yum per recuperare i pacchetti client più recenti.
    ```bash
    yum update
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
    
1. Abilitare PermitRootLogin in nella/etc/ssh/sshd_config
    1. Aprire il file nella/etc/ssh/sshd_config
    1. Cerca ' #PermitRootLogin sì'
    1. Rimuovi ' #' per rimuovere il commento



1. Eseguire l'aggiornamento di ' Leapp '.
    ```bash
    leapp upgrade --no-rhsm
    ```
1. Riavviare il servizio sshd per rendere effettive le modifiche
    ```bash
    systemctl restart sshd
    ```
1. Impostare come commento PermitRootLogin in nella/etc/ssh/sshd_config di nuovo
    1. Aprire il file nella/etc/ssh/sshd_config
    1. Cerca ' #PermitRootLogin sì'
    1. Aggiungi il ' #' al commento

## <a name="next-steps"></a>Passaggi successivi
* Scopri di più sulle [Immagini Red Hat in Azure](./redhat-images.md).
* Altre informazioni su [Red Hat Update Infrastructure](./redhat-rhui.md).
* Scopri di più sull' [offerta RHEL BYOS](./byos.md).
* Informazioni sui processi di aggiornamento sul posto di Red Hat sono reperibili nella documentazione di Red Hat, [aggiornamento da RHEL 7 a RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).