---
title: Esercitazione per configurare Azure Data Box Heavy | Microsoft Docs
description: Informazioni su come cablare e connettere Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: f5473ae7bd9ac1615d59d18c996e01902e1adbd6
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241420"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Esercitazione: Cablare e connettersi ad Azure Data Box Heavy

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Introduzione ad Azure Data Box Disk Heavy

::: zone-end

::: zone target = "docs"

Questa esercitazione descrive come cablare, connettere e accendere il dispositivo Azure Data Box Heavy.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Cablare Data Box Heavy
> * Connettersi a Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Questa guida fornisce istruzioni su come verificare i prerequisiti, cablare e connettere il dispositivo, copiare i dati, caricarli in Azure e quindi verificare i dati caricati.

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Aver completato l'esercitazione descritta in [Esercitazione: Ordinare Azure Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Aver ricevuto Data Box Heavy e che lo stato dell'ordine nel portale sia **Recapitati**.
3. Aver consultato le [linee guida per la sicurezza di Data Box Heavy](data-box-safety.md).
4. Avere accesso a una superficie piana nel data center, vicina a una connessione di rete disponibile, in grado di alloggiare un dispositivo con questo ingombro. Questo dispositivo non può essere montato su rack.
5. Aver ricevuto quattro cavi di alimentazione con messa a terra da usare con il dispositivo di archiviazione.
6. Sia disponibile un computer host connesso alla rete del data center. Il dispositivo Data Box Heavy copierà i dati da questo computer. Il computer host deve eseguire un [sistema operativo supportato](data-box-heavy-system-requirements.md).
7. Il data center disponga di una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. 
8. Avere un computer portatile con un cavo RJ-45 per connettersi all'interfaccia utente locale e configurare il dispositivo. Usare il portatile per configurare una sola volta ogni nodo del dispositivo.
9. È necessario un cavo da 40 Gbps o da 10 Gbps per ogni nodo del dispositivo.
    - Scegliere cavi compatibili con l'interfaccia di rete [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html).
    - Per il cavo da 40 Gbps, l'estremità da collegare al dispositivo deve essere di tipo QSFP+.
    - Il cavo da 10 Gbps deve avere un connettore SFP+ sull'estremità da collegare a uno switch da 10 Gbps e un adattatore da QSFP+ a SFP+ (o l'adattatore QSA) sull'estremità da collegare al dispositivo.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Cablare il dispositivo per l'alimentazione

Per cablare il dispositivo, eseguire la procedura seguente.

1. Ispezionare il dispositivo per accertare eventuali prove di manomissione o qualsiasi altro danno evidente. Se il dispositivo risulta manomesso o seriamente danneggiato, non procedere oltre. [Contattare immediatamente il supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md) per valutare se il dispositivo funziona correttamente o è necessaria una spedizione sostitutiva.
2. Portare il dispositivo sul sito di installazione.

    ![Sito di installazione del dispositivo Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Bloccare le rotelle del dispositivo come illustrato di seguito.

    ![Rotelle del dispositivo Data Box Heavy bloccate](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Individuare le manopole che sbloccano le porte anteriore e posteriore del dispositivo. Sbloccare e spostare la porta anteriore accostandola al lato del dispositivo. Ripetere questa operazione anche per la porta posteriore.
    Entrambe le porte devono rimanere aperte durante il funzionamento del dispositivo per consentire un flusso d'aria ottimale dalla parte anteriore a quella posteriore.

    ![Porte di Data Box Heavy aperte](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Il vano sul retro del dispositivo dovrebbe contenere quattro cavi di alimentazione. Rimuovere i cavi dal vano e metterli da parte.

    ![Cavi di alimentazione di Data Box Heavy nel vano](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Il passaggio successivo consiste nell'identificare le varie porte sul retro del dispositivo. Sono presenti due nodi del dispositivo, **NODE1** e **NODE2**. Ogni nodo include quattro interfacce di rete, **MGMT**, **DATA1**, **DATA2**, **DATA3**. La porta **MGMT** si usa per la gestione durante la configurazione iniziale del dispositivo. **DATA1**-**DATA3** sono porte di dati. Le porte **MGMT** e **DATA3** sono da 1 Gbps, mentre **DATA1** e **DATA2** possono funzionare come porte da 40 Gbps o da 10 Gbps. Sotto i due nodi del dispositivo si trovano quattro alimentatori condivisi tra i nodi. Guardando il dispositivo, da sinistra a destra gli **alimentatori** sono denominati **PSU1**, **PSU2**, **PSU3** e **PSU4**.

    ![Porte di Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Connettere tutti e quattro i cavi di alimentazione agli alimentatori del dispositivo. I LED verdi si accendono e lampeggiano.
8. Usare i pulsanti di alimentazione nel pannello anteriore per attivare i nodi del dispositivo. Tenere premuto il pulsante di alimentazione per qualche secondo finché non si accendono i LED blu. A questo punto tutti i LED verdi degli alimentatori sul retro dovrebbero restare accesi fissi. Il pannello operativo anteriore del dispositivo contiene anche LED di errore. Quando uno di questi LED è acceso, significa che si è verificato un problema con un alimentatore, una ventola o le unità disco.  

    ![Pannello operativo anteriore di Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Cablare il primo nodo per la rete

In uno dei nodi del dispositivo eseguire la procedura seguente per configurare il cablaggio di rete.

1. Usare un cavo di rete RJ-45 CAT 6 (cavo blu nella figura) per connettere il computer host alla porta di gestione da 1 Gbps.
2. Usare un cavo QSFP+ (fibra o rame) per connettere almeno una interfaccia di rete a 40 Gbps (preferita rispetto a quella a 1 Gbps) per i dati. Se si usa uno switch da 10 Gbps, usare un cavo SFP+ con un adattatore da QSFP+ a SFP+ (adattatore QSA) per connettere l'interfaccia di rete da 40 Gbps per i dati.

    ![Porte di Data Box Heavy cablate](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > Le porte DATA 1 e DATA2 sono commutate e non corrispondono a quanto visualizzato nell'interfaccia utente Web locale.
    > L'adattatore del cavo da 40 Gbps si connette se inserito nel modo illustrato di seguito.

    ![Adattatore del cavo da 40 Gbps di Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Configurare il primo nodo

Eseguire la procedura seguente per configurare il dispositivo usando la configurazione locale e il portale di Azure.

1. Scaricare le credenziali del dispositivo dal portale. Passare a **Generale > Dettagli dispositivo**. Copiare la password indicata in **Password per il dispositivo**. Queste password sono associate a un ordine specifico nel portale. Vengono visualizzati i due numeri di serie del dispositivo che corrispondono ai due nodi di Data Box Heavy. La password dell'amministratore del dispositivo è la stessa per entrambi i nodi.

    ![Credenziali del dispositivo Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Connettere la workstation client al dispositivo tramite un cavo di rete RJ-45 CAT6.
3. Configurare la scheda Ethernet nel computer usato per connettersi al dispositivo impostando l'indirizzo IP statico `192.168.100.5` e la subnet `255.255.255.0`.

    ![Data Box Heavy si connette all'interfaccia utente Web locale](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Connettersi all'interfaccia utente Web locale del dispositivo all'URL seguente: `http://192.168.100.10`. Fare clic su **Avanzate** e quindi su **Proceed to 192.168.100.10 (unsafe)** (Passare a 192.168.100.10 (non sicuro)).
5. Verrà visualizzata una **pagina di accesso** per l'interfaccia utente Web locale.
    
    - Uno dei numeri di serie dei nodi visualizzati in questa pagina corrisponde sia nell'interfaccia utente del portale che in quella Web locale. Prendere nota del mapping tra numero di nodo e numero di serie. Nel portale sono visualizzati due nodi e due numeri di serie del dispositivo. Il mapping consente di capire quale nodo corrisponde a quale numero di serie.
    - A questo punto il dispositivo è bloccato.
    - Specificare la password di amministratore del dispositivo ottenuta nel passaggio precedente per accedere al dispositivo. Fare clic su **Accedi**.

    ![Accedere all'interfaccia utente Web locale di Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Nel dashboard verificare che le interfacce di rete siano configurate. Sono presenti quattro interfacce di rete nel nodo del dispositivo, due da 1 Gbps e due da 40 Gbps. Una delle interfacce da 1 Gbps è un'interfaccia di gestione, per cui non è configurabile dall'utente. Le altre tre interfacce di rete sono dedicate ai dati e possono essere configurate dall'utente.

- Se nell'ambiente è abilitato il DHCP, le interfacce di rete vengono configurate automaticamente.
- Se il DHCP non è abilitato, passare a Imposta interfacce di rete e assegnare IP statici se necessario.

    ![Nodo 1 nel dashboard di Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Configurare il secondo nodo

Seguire la procedura illustrata nella sezione [Configurare il primo nodo](#configure-first-node) per configurare il secondo nodo del dispositivo.

![Nodo 2 nel dashboard di Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Dopo il completamento della configurazione del dispositivo, è possibile connettersi alle condivisioni del dispositivo stesso e copiarvi i dati dal computer.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box Heavy, ad esempio:

> [!div class="checklist"]
> * Cablare Data Box Heavy
> * Connettersi a Data Box Heavy

Passare all'esercitazione successiva per informazioni su come copiare dati in Data Box Heavy.

> [!div class="nextstepaction"]
> [Copiare i dati in Azure Data Box](./data-box-heavy-deploy-copy-data.md)

::: zone-end
