---
title: Accedere al pannello di controllo di Avere vFXT - Azure
description: Come connettersi al cluster vFXT e al pannello di controllo basato su browser di Avere per configurare Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: c48f0d8f7ad34db585f4deae566641b6453357e8
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670058"
---
# <a name="access-the-vfxt-cluster"></a>Accedere al cluster vFXT

Per modificare le impostazioni e monitorare il cluster Avere vFXT, usare il pannello di controllo di Avere. Il pannello di controllo di Avere è un'interfaccia grafica basata su browser per il cluster.

Poiché il cluster vFXT si trova all'interno di una rete virtuale privata, è necessario creare un tunnel SSH oppure usare un altro metodo per raggiungere l'indirizzo IP di gestione del cluster. I passaggi di base sono due: 

1. Creare una connessione tra la workstation e la rete virtuale privata 
1. Usare l'indirizzo IP di gestione del cluster per caricare il pannello di controllo in un Web browser 

> [!NOTE] 
> In questo articolo si presuppone di aver già impostato un indirizzo IP pubblico nel controller del cluster o in un'altra macchina virtuale all'interno della rete virtuale del cluster. Se per l'accesso alla rete virtuale si usa una VPN o ExpressRoute, passare a [Connettersi al pannello di controllo di Avere](#connect-to-the-avere-control-panel-in-a-browser).

Prima della connessione, assicurarsi che la coppia di chiavi pubblica/privata SSH usata durante la creazione del controller del cluster sia installata nel computer locale. Per istruzioni, vedere la documentazione sulle chiavi SSH per [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) o per [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).  

## <a name="access-with-a-linux-host"></a>Accedere con un host Linux

con questo formato: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*@*controller_public_IP* 

Questo comando esegue la connessione all'indirizzo IP di gestione del cluster tramite l'indirizzo IP del controller del cluster.

Esempio:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

L'autenticazione avviene automaticamente se si è usata la chiave SSH pubblica per creare il cluster e la chiave corrispondente è installata nel sistema client.


## <a name="access-with-a-windows-host"></a>Accedere con un host Windows

Se si usa PuTTY, immettere nel campo **hostname** il nome utente del controller del cluster e il relativo indirizzo IP: *your_username*@*controller_public_IP*.

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

L'autenticazione avviene automaticamente se si è usata la chiave SSH pubblica per creare il cluster e la chiave corrispondente è installata nel sistema client.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Connettersi al pannello di controllo di Avere in un browser

Questo passaggio usa un Web browser per connettersi all'utilità di configurazione in esecuzione nel cluster vFXT.

Aprire un Web browser e passare a https://127.0.0.1:8443. 

A seconda del browser, potrebbe essere necessario fare clic su **Avanzate** e verificare che sia sicuro procedere alla pagina.

Immettere il nome utente `admin` e la password specificata in fase di creazione del cluster.

![Screenshot della finestra di accesso di Avere popolata con il nome utente "admin" e una password](media/avere-vfxt-gui-login.png)

Fare clic su **Login** (Accedi) o premere INVIO sulla tastiera.

## <a name="next-steps"></a>Passaggi successivi

Ora che è possibile accedere al cluster, abilitare il [supporto](avere-vfxt-enable-support.md).
