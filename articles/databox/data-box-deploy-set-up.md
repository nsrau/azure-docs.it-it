---
title: Configurare Azure Data Box| Microsoft Docs
description: Informazioni su come cablare e connettersi ad Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2018
ms.author: alkohli
ms.openlocfilehash: f9671e3acdb71affe2187258582254561785780e
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785637"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Esercitazione: Cablare e connettersi ad Azure Data Box

Questa esercitazione descrive come cablare, collegare e accendere il dispositivo Azure Data Box.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Cablare il Data Box
> * Connettersi al Data Box

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Sia stata completata l'[Esercitazione: Ordinare Azure Data Box](data-box-deploy-ordered.md).
2. Sia stato ricevuto il Data Box e lo stato dell'ordine nel portale sia **Recapitato**. L'involucro trasparente fissato al dispositivo sotto l'attuale etichetta contenga un'etichetta di spedizione. Tenere al sicuro quest'etichetta perché servirà per la spedizione di restituzione.
3. Siano state esaminate le [linee guida per la sicurezza del Data Box](data-box-safety.md).
4. Si sia ricevuto un cavo di alimentazione a terra da usare con il dispositivo di archiviazione da 100 TB.
5. Sia disponibile un computer host con i dati da copiare nel Data Box. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
    - Essere connesso a una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE, ma la velocità dell'operazione di copia ne risentirà. 
6. Si abbia accesso a una superficie piana su cui posizionare il Data Box. Per posizionare il dispositivo su uno scaffale standard, è necessario uno slot 7U nel rack del data center. È possibile posizionare il dispositivo in piano o in verticale nel rack.
7. Si disponga dei cavi seguenti per collegare il Data Box al computer host.
    - Due cavi in rame SFP+ Twinax a 10 GbE (da usare con le interfacce di rete DATA 1, DATA 2)
    - Un cavo di rete RJ-45 CAT 6 (da usare con l'interfaccia di rete MGMT)
    - Un cavo di rete RJ-45 CAT 6A oppure RJ-45 CAT 6 (da usare con l'interfaccia di rete DATA 3 configurata rispettivamente come 10 Gbps o 1 Gbps)

## <a name="cable-your-device"></a>Cablare il dispositivo

Per cablare il dispositivo, eseguire la procedura seguente.

1. Ispezionare il dispositivo per accertare eventuali prove di manomissione o qualsiasi altro danno evidente. Se il dispositivo risulta manomesso o seriamente danneggiato, non procedere oltre. Contattare immediatamente il supporto tecnico Microsoft per valutare se il dispositivo funziona correttamente o è necessaria una spedizione sostitutiva.
2. Trasportare il dispositivo nella posizione in cui si vuole accenderlo. Posizionare il dispositivo su una superficie piana. Il dispositivo può essere posizionato anche su uno scaffale standard.
3. Collegare i cavi di alimentazione e di rete. Di seguito è illustrato il backplane di un dispositivo connesso per una configurazione comune. 
    
    ![Backplane cablato di un dispositivo Data Box](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Collegare il cavo di alimentazione alla posizione con l'etichetta di potenza in ingresso. L'altra estremità del cavo di alimentazione deve essere collegata a un'unità di distribuzione dell'alimentazione.
    2. Usare il cavo RJ-45 CAT 6 per collegare la porta MGMT a un'estremità e un portatile all'altra estremità.            
    3. Collegare il cavo RJ-45 CAT 6A alla porta DATA 3 a un'estremità. La porta DATA 3 è configurata a 10 GbE se si usa un cavo RJ-45 CAT 6A e a 1 GbE se si usa un cavo RJ-45 CAT 6.
    4. Usare i cavi in rame SFP+ Twinax a 10 GbE per collegare le porte DATA 1 e DATA 2 rispettivamente. 
    5. Le altre estremità dei cavi dalle porte dati vengono collegate al computer con un commutatore a 10 GbE.

4. Individuare il pulsante di accensione sul pannello operativo anteriore del dispositivo. Accendere il dispositivo.

    ![Pulsante di accensione del Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>Connettersi al dispositivo

Eseguire la procedura seguente per configurare il dispositivo usando l'interfaccia utente Web locale e l'interfaccia utente del portale.

1. Configurare la scheda Ethernet nel portatile usato per connettersi al dispositivo impostando l'indirizzo IP statico 192.168.100.5 e la subnet 255.255.255.0. 
2. Connettersi alla porta MGMT del dispositivo e accedere alla relativa interfaccia utente Web locale all'indirizzo https://192.168.100.10. Questa operazione potrebbe richiedere fino a 5 minuti dopo l'accensione del dispositivo.
3. Fare clic su **Dettagli** e quindi su **Continua per la pagina Web**.

   ![Connettersi all'interfaccia utente Web locale](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. Verrà visualizzata una **pagina di accesso** per l'interfaccia utente Web locale. Verificare che il numero di serie del dispositivo corrisponda nell'interfaccia utente del portale e nell'interfaccia utente Web locale. A questo punto il dispositivo è bloccato.
5. Accedere al [portale di Azure](https://portal.azure.com).
6. Scaricare le credenziali del dispositivo dal portale. Passare a **Generale > Dettagli dispositivo**. Copiare la password indicata in **Password per il dispositivo**. La password per il dispositivo è associata a un ordine specifico nel portale. 

    ![Ottenere le credenziali del dispositivo](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Specificare la password del dispositivo recuperata dal portale di Azure nel passaggio precedente per accedere all'interfaccia utente Web locale del dispositivo. Fare clic su **Accedi**.
8. Nel **Dashboard** verificare che le interfacce di rete siano configurate. 
    - Se nell'ambiente è abilitato il DHCP, le interfacce di rete vengono configurate automaticamente. 
    - Se il DHCP non è abilitato, passare a **Imposta interfacce di rete** e assegnare IP statici se necessario.

    ![Pagina dashboard](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Una volta completata la configurazione del dispositivo, è possibile connettersi alle condivisioni del dispositivo e copiare i dati dal computer al dispositivo. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Cablare il Data Box
> * Connettersi al Data Box

Passare all'esercitazione successiva per informazioni su come copiare i dati sul Data Box.

> [!div class="nextstepaction"]
> [Copiare i dati in Azure Data Box](./data-box-deploy-copy-data.md)

