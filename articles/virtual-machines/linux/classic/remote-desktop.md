---
title: Desktop remoto per una macchina virtuale Linux | Microsoft Docs
description: Informazioni sull'installazione e la configurazione di Desktop remoto per la connessione a una macchina virtuale Linux di Microsoft Azure per il modello di distribuzione classica
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: mingzhan
ms.openlocfilehash: 68031d548bdbeda9a83d1bceaaea7c5bbcab3188
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Uso di Desktop remoto per connettersi a una macchina virtuale Linux di Microsoft Azure.
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per la versione aggiornata di questo articolo relativa a Resource Manager, vedere [qui](../use-remote-desktop.md).

## <a name="overview"></a>Panoramica
Il protocollo RDP (Remote Desktop Protocol) è un protocollo proprietario utilizzato per Windows. Come si può utilizzare RDP per connettersi a una VM (macchina virtuale) Linux in modalità remota?

L'articolo seguente risponderà a questa domanda. Illustrerà come installare e configurare xrdp su una macchina virtuale Linux di Microsoft Azure in modo da connettersi a essa con Desktop remoto da un computer Windows. Utilizzeremo una macchina virtuale Linux che esegue Ubuntu o OpenSUSE come nell'esempio riportato in questa guida.

Lo strumento xrdp è un server RDP open source che consente di connettere il server Linux con Desktop remoto da un computer Windows. RDP offre prestazioni migliori di VNC (Virtual Network Computing). VNC esegue il rendering usando una grafica di qualità JPEG e può risultare lento, mentre RDP è veloce e nitido.

> [!NOTE]
> È necessario disporre di una macchina virtuale di Microsoft Azure che esegue Linux. Vedere l'[esercitazione relativa alle macchine virtuali Linux di Azure](createportal.md)per creare e impostare una macchina virtuale Linux.
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Creare un endpoint per Desktop remoto
In questo documento verrà usato l'endpoint predefinito 3389 per Desktop remoto. Configurare l'endpoint 3389 come `Remote Desktop` per la macchina virtuale Linux nel modo seguente:

![immagine](./media/remote-desktop/endpoint-for-linux-server.png)

Per informazioni su come configurare un endpoint per la macchina virtuale, vedere [queste indicazioni](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Installare Gnome Desktop
Connettersi alla macchina virtuale Linux tramite `putty` e installare `Gnome Desktop`.

Per Ubuntu, utilizzare:

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


Per OpenSUSE, usare:

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>Installare xrdp
Per Ubuntu, utilizzare:

    #sudo apt-get install xrdp

Per OpenSUSE, usare:

> [!NOTE]
> Aggiornare la versione OpenSUSE con la versione in uso nel comando seguente. Di seguito è riportato un comando di esempio per `OpenSUSE 13.2`.
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Avviare xrdp e impostare il servizio xdrp all'avvio
Per OpenSUSE, usare:

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

Per Ubuntu, xrdp verrà avviato e abilitato automaticamente al momento dell'avvio dopo l'installazione.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Uso di xfce se si usa una versione di Ubuntu successiva a Ubuntu 12.04LTS
Poiché attualmente xrdp non supporta Gnome Desktop per le versioni di Ubuntu successive a Ubuntu 12.04LTS, verrà usato Desktop `xfce`.

Per installare `xfce`, usare questo comando:

    #sudo apt-get install xubuntu-desktop

Quindi abilitare `xfce` usando questo comando:

    #echo xfce4-session >~/.xsession

Modificare il file di configurazione `/etc/xrdp/startwm.sh`:

    #sudo vi /etc/xrdp/startwm.sh   

Aggiungere la riga `xfce4-session` prima della riga `/etc/X11/Xsession`.

Per riavviare il servizio xrdp, usare:

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Connettersi alla macchina virtuale Linux da un computer Windows
In un computer Windows avviare il client Desktop remoto e immettere il nome DNS della macchina virtuale Linux oppure passare al dashboard della macchina virtuale nel portale di Azure e fare clic su `Connect` per connettere la macchina virtuale Linux. In questo caso verrà visualizzata la finestra di accesso:

![immagine](./media/remote-desktop/no2.png)

Accedere con il nome utente e la password della macchina virtuale Linux.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso di xrdp, vedere [http://www.xrdp.org/](http://www.xrdp.org/).
