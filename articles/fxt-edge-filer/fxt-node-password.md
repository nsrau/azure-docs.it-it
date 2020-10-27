---
title: "Esercitazione: Inizializzare l'hardware - Azure FXT Edge Filer"
description: Informazioni su come connettersi al nodo hardware e impostare una password iniziale nei nodi di Azure FXT Edge Filer.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 79d908077d3bd66a3ed86759e37574383358881b
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218851"
---
# <a name="tutorial-set-hardware-passwords"></a>Esercitazione: Impostare password per l'hardware

La prima volta che si accende un nodo Azure FXT Edge Filer, è necessario impostare una password radice. I nodi hardware non vengono spediti con una password predefinita.

Le porte di rete sono disabilitate fino a quando non viene impostata una password e l'utente ROOT non accede.

Eseguire questo passaggio dopo aver installato e cablato il nodo, ma prima di tentare di creare il cluster.

Questa esercitazione descrive come connettersi al nodo hardware e impostare la password. Spiega inoltre come aggiungere una password di configurazione del BIOS per proteggere il nodo.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Collegare una tastiera e un monitor al nodo e accendere il nodo
> * Impostare una password di configurazione del BIOS
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

## <a name="create-a-bios-setup-password"></a>Creare una password di configurazione del BIOS

Una password di configurazione del BIOS protegge le impostazioni del BIOS del nodo da modifiche accidentali o non autorizzate. Questa password non è necessaria per creare un cluster, ma è fortemente consigliata come parte della strategia di sicurezza del cluster.

Per creare una password di configurazione del BIOS:

1. Attivare o riavviare il nodo e quindi premere immediatamente F2 per aprire l'utilità di configurazione di sistema.

1. Nella schermata **System Setup Main Menu** (Menu principale configurazione sistema) scegliere **System BIOS** > **System Security** (BIOS sistema > Sicurezza sistema).

1. Verificare che l'impostazione **Password Status** (Stato password) sia **Unlocked** (Sbloccata).

1. Usare il campo **Setup Password** (Imposta password) per impostare la password. In questa schermata si può anche impostare una password del BIOS di sistema, se si vuole usarne una.

1. Premere ESC per tornare alla schermata **System BIOS** (BIOS sistema), quindi premere nuovamente ESC. Verrà visualizzato un messaggio in cui viene chiesto di salvare le modifiche. Se il sistema non viene riavviato automaticamente, riavviarlo per visualizzare la schermata di avvio normale.<!-- how to exit this mode/do you need to reboot to get to the initial setup screen? -->

## <a name="set-initial-passwords"></a>Impostare le password iniziali

Il nodo Azure FXT Edge Filer visualizzerà diversi messaggi sul monitor durante l'avvio. Dopo alcuni istanti, appare schermata di configurazione iniziale simile a questa:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set the iDRAC and temporary root password.
Minimum password length is 8.

NOTE: This does not set a BIOS setup password. For security,
Microsoft recommends using a BIOS setup password, restricting
physical access to the node, and other measures. Learn more at
https://aka.ms/fxt-security.

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
