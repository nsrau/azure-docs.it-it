---
title: Accedere al pannello di controllo di Avere vFXT - Azure
description: Come connettersi al cluster vFXT e al pannello di controllo basato su browser di Avere per configurare Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 79e7c5db2a2c445ae740a21744a0bdfe0736c01a
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342434"
---
# <a name="access-the-vfxt-cluster"></a>Accedere al cluster vFXT

Per modificare le impostazioni del cluster e monitorare il cluster, usare il pannello di controllo. Il pannello di controllo di Avere è un'interfaccia grafica basata su browser per il cluster.

Poiché il cluster vFXT si trova all'interno di una rete virtuale privata, è necessario creare un tunnel SSH o usare un altro metodo per raggiungere l'indirizzo IP di gestione del cluster.

I passaggi di base sono due:

1. Creare una connessione tra la workstation e la rete virtuale privata
1. Caricare il pannello di controllo del cluster in un Web browser

> [!NOTE]
> In questo articolo si presuppone di aver già impostato un indirizzo IP pubblico nel controller del cluster o in un'altra macchina virtuale all'interno della rete virtuale del cluster. Questo articolo descrive come usare tale macchina virtuale come host per accedere al cluster. Se si usa una VPN o ExpressRoute per l'accesso alla rete virtuale, passare a [Connetti al pannello di controllo](#connect-to-the-avere-control-panel-in-a-browser).

Prima della connessione, assicurarsi che la coppia di chiavi pubblica/privata SSH usata durante la creazione del controller del cluster sia installata nel computer locale. Per istruzioni, vedere la documentazione sulle chiavi SSH per [Windows](../virtual-machines/linux/ssh-from-windows.md) o per [Linux](../virtual-machines/linux/mac-create-ssh-keys.md). Se è stata usata una password anziché una chiave pubblica, verrà richiesto di immetterla quando ci si connette.

## <a name="create-an-ssh-tunnel"></a>Creazione di un tunnel SSH

È possibile creare un tunnel SSH dalla riga di comando di un sistema client basato su Linux o Windows 10.

Usare un comando di tunneling SSH con il formato seguente:

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Questo comando esegue la connessione all'indirizzo IP di gestione del cluster tramite l'indirizzo IP del controller del cluster.

Esempio:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

L'autenticazione avviene automaticamente se si è usata la chiave SSH pubblica per creare il cluster e la chiave corrispondente è installata nel sistema client. Se si è usata una password, il sistema chiederà di specificarla.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Connettersi al pannello di controllo di Avere in un browser

Questo passaggio usa un Web browser per connettersi all'utilità di configurazione nel cluster vFXT.

* Per una connessione tunnel SSH, aprire il Web browser e passare a `https://127.0.0.1:8443`.

  Ci si è connessi all'indirizzo IP del cluster al momento della creazione del tunnel, pertanto è sufficiente usare l'indirizzo IP localhost nel browser. Se è stata usata una porta locale diversa dalla 8443, usare invece il numero di porta.

* Se si usa una VPN o ExpressRoute per raggiungere il cluster, passare all'indirizzo IP di gestione del cluster nel browser. Esempio: ``https://203.0.113.51``

A seconda del browser, potrebbe essere necessario fare clic su **Avanzate** e verificare che sia sicuro procedere alla pagina.

Immettere il nome utente `admin` e la password amministrativa specificata in fase di creazione del cluster.

![Screenshot della finestra di accesso di Avere popolata con il nome utente "admin" e una password](media/avere-vfxt-gui-login.png)

Fare clic su **Login** (Accedi) o premere INVIO sulla tastiera.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver effettuato l'accesso al pannello di controllo del cluster, abilitare il [supporto](avere-vfxt-enable-support.md).