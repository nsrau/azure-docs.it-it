---
title: Inizializzare l'hardware - Microsoft Azure FXT Edge Filer
description: Come impostare una password iniziale nei nodi Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 080aa05af77b996bc0eb71287a3dfef25c24629a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256009"
---
# <a name="tutorial-set-hardware-passwords"></a>Esercitazione: Impostare password per l'hardware

La prima volta che si accende un nodo Azure FXT Edge Filer, è necessario impostare una password radice. I nodi hardware non vengono spediti con una password predefinita. 

Le porte di rete sono disabilitate fino a quando non viene impostata una password e l'utente ROOT non accede.

Eseguire questo passaggio dopo aver installato e cablato il nodo, ma prima di tentare di creare il cluster. 

Questa esercitazione descrive come connettersi al nodo hardware e impostare la password. 

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Collegare una tastiera e un monitor al nodo e accendere il nodo
> * Impostare le password per la porta iDRAC e l'utente ROOT nel nodo
> * Accedere come utente ROOT 

Ripetere questi passaggi per ogni nodo che verrà usato nel cluster. 

Il completamento di questa esercitazione richiede circa 15 minuti. 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, completare i passaggi seguenti: 

* [Installare](fxt-install.md) ogni nodo Azure FXT Edge Filer in un'apparecchiatura in rack e collegare i cavi di alimentazione e l'accesso alla rete, come descritto nell'[esercitazione precedente](fxt-network-power.md). 
* Procurarsi una tastiera con cavo USB e un monitor con porta VGA da collegare ai nodi hardware. La porta seriale del nodo è inattiva prima di impostare la password.

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Collegare una tastiera e un monitor al nodo

Collegare fisicamente un monitor e una tastiera al nodo Azure FXT Edge Filer. 

* Collegare il monitor tramite la porta VGA.
* Collegare la tastiera tramite una delle porte USB. 

Usare il diagramma di riferimento per individuare le porte sul retro dello chassis. 

> [!NOTE]
> La porta seriale è inattiva finché non viene impostata la password. 

![Diagramma della parte posteriore di Azure FXT Edge Filer con indicazione delle porte seriale, VGA e USB](media/fxt-back-serial-vga-usb.png)

È possibile usare un commutatore KVM se si vuole collegare più di un nodo alle stesse periferiche. 

Accendere il nodo premendo il pulsante di accensione sulla parte anteriore. 

![Diagramma della parte anteriore di Azure FXT Edge Filer, con il pulsante di alimentazione rotondo indicato in alto a destra](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Impostare le password iniziali 

Il nodo Azure FXT Edge Filer visualizzerà diversi messaggi sul monitor durante l'avvio. Dopo alcuni istanti, appare schermata di configurazione iniziale simile a questa:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

La password immessa viene usata in due modi: 

* Come password radice temporanea per questo nodo Azure FXT Edge Filer. 

  Questa password verrà modificata quando si crea un cluster usando questo nodo o quando si aggiunge il nodo al cluster. La password di gestione del cluster (associata all'utente ``admin``) è anche la password radice per tutti i nodi in un cluster.

* Come password a lungo termine per la porta di gestione dell'hardware iDRAC/IPMI.

  Assicurarsi di ricordare la password qualora sia necessaria per accedere con IPMI in seguito per la risoluzione di un problema hardware.

Immettere e confermare la password: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Dopo aver immesso la password, il sistema prosegue nell'avvio. Al termine, viene visualizzato un prompt ``login:``. 

## <a name="sign-in-as-root"></a>Accedere come utente ROOT

Accedere come utente ``root`` con la password appena impostata. 

```
login: root
Password:**********
```

Dopo aver effettuato l'accesso come utente ROOT, le porte di rete sono attive e contattano il server DHCP per rilevare gli indirizzi IP. 

## <a name="next-steps"></a>Passaggi successivi

Il nodo è pronto per fare parte di un cluster. È possibile usarlo per creare il cluster Azure FXT Edge Filer oppure è possibile [aggiungerlo a un cluster esistente](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Creare un cluster](fxt-cluster-create.md)
