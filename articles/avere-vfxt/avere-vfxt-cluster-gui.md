---
title: Accedere al pannello di controllo di Avere vFXT - Azure
description: Come connettersi al cluster vFXT e al pannello di controllo basato su browser di Avere per configurare Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f989f4d103efecf2b6e206287dd8b7b300a1796d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794270"
---
# <a name="access-the-vfxt-cluster"></a>Accedere al cluster vFXT

Per modificare le impostazioni e monitorare il cluster Avere vFXT, usare il pannello di controllo di Avere. Il pannello di controllo di Avere è un'interfaccia grafica basata su browser per il cluster.

Poiché il cluster vFXT si trova all'interno di una rete virtuale privata, è necessario creare un tunnel SSH oppure usare un altro metodo per raggiungere l'indirizzo IP di gestione del cluster. I passaggi di base sono due: 

1. Creare una connessione tra la workstation e la rete virtuale privata 
1. Caricare il pannello di controllo del cluster in un Web browser 

> [!NOTE] 
> In questo articolo si presuppone di aver già impostato un indirizzo IP pubblico nel controller del cluster o in un'altra macchina virtuale all'interno della rete virtuale del cluster. Questo articolo descrive come usare tale macchina virtuale come host per accedere al cluster. Se per l'accesso alla rete virtuale si usa una VPN o ExpressRoute, passare a [Connettersi al pannello di controllo di Avere](#connect-to-the-avere-control-panel-in-a-browser).

Prima della connessione, assicurarsi che la coppia di chiavi pubblica/privata SSH usata durante la creazione del controller del cluster sia installata nel computer locale. Per istruzioni, vedere la documentazione sulle chiavi SSH per [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) o per [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys). Se si è usata una password anziché una chiave pubblica, verrà chiesto di immetterla al momento della connessione. 

## <a name="ssh-tunnel-with-a-linux-host"></a>Tunnel SSH con un host Linux

Se si usa un client basato su Linux, usare un comando di tunneling SSH in questo formato: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Questo comando esegue la connessione all'indirizzo IP di gestione del cluster tramite l'indirizzo IP del controller del cluster.

Esempio:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

L'autenticazione avviene automaticamente se si è usata la chiave SSH pubblica per creare il cluster e la chiave corrispondente è installata nel sistema client. Se si è usata una password, il sistema chiederà di specificarla.

## <a name="ssh-tunnel-with-a-windows-host"></a>Tunnel SSH con un host Windows

Questo esempio usa l'utilità terminale basata su Windows comune PuTTY.

Immettere nel campo **hostname** il nome utente del controller del cluster e il relativo indirizzo IP: *nome_utente*\@*IP_pubblico_controller*.

Esempio: ``azureuser@203.0.113.51``

Nel pannello **Configuration** (Configurazione):

1. Espandere **Connection (Connessione)** > **SSH** a sinistra. 
1. Fare clic su **Tunnels** (Tunnel). 
1. Immettere una porta di origine, ad esempio 8443. 
1. Per la destinazione, immettere l'indirizzo IP di gestione del cluster vFXT e la porta 443. 
   Esempio: ``203.0.113.51:443``
1. Fare clic su **Aggiungi**.
1. Fare clic su **Apri**.

![Screenshot dell'applicazione Putty che mostra dove fare clic per aggiungere un tunnel](media/avere-vfxt-ptty-numbered.png)

L'autenticazione avviene automaticamente se si è usata la chiave SSH pubblica per creare il cluster e la chiave corrispondente è installata nel sistema client. Se si è usata una password, il sistema chiederà di specificarla.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Connettersi al pannello di controllo di Avere in un browser

Questo passaggio usa un Web browser per connettersi all'utilità di configurazione in esecuzione nel cluster vFXT.

* Per una connessione tunnel SSH, aprire il Web browser e passare a `https://127.0.0.1:8443`. 

  Ci si è connessi all'indirizzo IP del cluster al momento della creazione del tunnel, pertanto è sufficiente usare l'indirizzo IP localhost nel browser. Se è stata usata una porta locale diversa dalla 8443, usare invece il numero di porta.

* Se si usa una VPN o ExpressRoute per raggiungere il cluster, passare all'indirizzo IP di gestione del cluster nel browser. Esempio: ``https://203.0.113.51``

A seconda del browser, potrebbe essere necessario fare clic su **Avanzate** e verificare che sia sicuro procedere alla pagina.

Immettere il nome utente `admin` e la password amministrativa specificata in fase di creazione del cluster.

![Screenshot della finestra di accesso di Avere popolata con il nome utente "admin" e una password](media/avere-vfxt-gui-login.png)

Fare clic su **Login** (Accedi) o premere INVIO sulla tastiera.

## <a name="next-steps"></a>Passaggi successivi

Ora che è possibile accedere al cluster, abilitare il [supporto](avere-vfxt-enable-support.md).
