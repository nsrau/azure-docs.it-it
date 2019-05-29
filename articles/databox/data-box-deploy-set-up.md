---
title: Esercitazione per configurare Azure Data Box | Microsoft Docs
description: Informazioni su come cablare e connettersi ad Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: alkohli
ms.openlocfilehash: 6a725784c419b67f7738b70ad867d2d6ef8b0785
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795967"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Esercitazione: Cablare e connettersi ad Azure Data Box

Questa esercitazione descrive come cablare, collegare e accendere il dispositivo Azure Data Box.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Cablare il Data Box
> * Connettersi al Data Box

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Aver completato l'esercitazione descritta in [Esercitazione: Ordinare Azure Data Box](data-box-deploy-ordered.md).
2. Sia stato ricevuto il Data Box e lo stato dell'ordine nel portale sia **Recapitato**. 
    - L'involucro trasparente fissato al dispositivo sotto l'attuale etichetta contenga un'etichetta di spedizione. Tenere al sicuro quest'etichetta perché servirà per la spedizione di restituzione.
    - In alcune aree in Europa, il dispositivo potrebbe arrivare confezionato in una scatola. Assicurarsi di disimballare il dispositivo e di conservare la scatola per la spedizione di ritorno.
3. Siano state esaminate le [linee guida per la sicurezza del Data Box](data-box-safety.md).
4. Si sia ricevuto un cavo di alimentazione a terra da usare con il dispositivo di archiviazione da 100 TB.
5. Sia disponibile un computer host con i dati da copiare nel Data Box. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
    - Essere connesso a una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE, ma la velocità dell'operazione di copia ne risentirà. 
6. Si abbia accesso a una superficie piana su cui posizionare il Data Box. Per posizionare il dispositivo su uno scaffale standard, è necessario uno slot 7U nel rack del data center. È possibile posizionare il dispositivo in piano o in verticale nel rack.
7. Si disponga dei cavi seguenti per collegare il Data Box al computer host.
    - Uno o più cavi in rame SFP+ Twinax o in fibra ottica SFP+ a 10 GbE (da usare con interfacce di rete DATA 1 e DATA 2). Data Box include schede Mellanox ConnectX®-3 Pro EN 10GBASE-T a due porte con interfaccia di rete PCI Express 3.0, quindi i cavi compatibili con questa interfaccia dovrebbero funzionare. Per i test interni, ad esempio, è stato usato un cavo CISCO SFP-H10GB-CU3M 10GBASE-CU TWINMAX SFP +3M. Per altre informazioni, vedere l'[elenco dei cavi e dei commutatori supportati di Mellanox](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - Un cavo di rete RJ-45 CAT 6 (da usare con l'interfaccia di rete MGMT)
    - Un cavo di rete RJ-45 CAT 6A oppure RJ-45 CAT 6 (da usare con l'interfaccia di rete DATA 3 configurata rispettivamente come 10 Gbps o 1 Gbps)

## <a name="cable-your-device"></a>Cablare il dispositivo

Per cablare il dispositivo, eseguire la procedura seguente.

1. Ispezionare il dispositivo per accertare eventuali prove di manomissione o qualsiasi altro danno evidente. Se il dispositivo risulta manomesso o seriamente danneggiato, non procedere oltre. Contattare immediatamente il supporto tecnico Microsoft per valutare se il dispositivo funziona correttamente o è necessaria una spedizione sostitutiva.
2. Trasportare il dispositivo nella posizione in cui si vuole accenderlo. Posizionare il dispositivo su una superficie piana. Il dispositivo può essere posizionato anche su uno scaffale standard.
3. Collegare i cavi di alimentazione e di rete. Di seguito è illustrato il backplane di un dispositivo connesso per una configurazione comune. A seconda dell'ambiente corrente, è possibile scegliere tra altre [opzioni di cablaggio](data-box-cable-options.md).
    
    ![Backplane cablato di un dispositivo Data Box](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Collegare il cavo di alimentazione alla posizione con l'etichetta di potenza in ingresso. L'altra estremità del cavo di alimentazione deve essere collegata a un'unità di distribuzione dell'alimentazione.
    2. Usare il cavo RJ-45 CAT 6 per collegare la porta MGMT a un'estremità e un portatile all'altra estremità.            
    3. Collegare il cavo RJ-45 CAT 6A alla porta DATA 3 a un'estremità. La porta DATA 3 è configurata a 10 GbE se si usa un cavo RJ-45 CAT 6A e a 1 GbE se si usa un cavo RJ-45 CAT 6.
    4. A seconda delle interfacce di rete da connettere per il trasferimento dei dati, usare fino a due cavi in rame SFP+ Twinax o in fibra ottica SFP+ a 10 GbE per connettere rispettivamente le porte DATA 1 e DATA 2. 
    5. Le altre estremità dei cavi dalle porte dati vengono collegate al computer con un commutatore a 10 GbE.

4. Individuare il pulsante di accensione sul pannello operativo anteriore del dispositivo. Accendere il dispositivo.

    ![Pulsante di accensione del Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>Connettersi al dispositivo

Eseguire la procedura seguente per configurare il dispositivo usando l'interfaccia utente Web locale e l'interfaccia utente del portale.

1. Configurare la scheda Ethernet nel portatile usato per connettersi al dispositivo impostando l'indirizzo IP statico 192.168.100.5 e la subnet 255.255.255.0. 
2. Connettersi alla porta MGMT del dispositivo e accedere all'interfaccia utente Web locale del dispositivo all'indirizzo https\://192.168.100.10. Questa operazione potrebbe richiedere fino a 5 minuti dopo l'accensione del dispositivo.
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

Dopo la configurazione delle interfacce di rete per i dati, è anche possibile usare l'indirizzo IP di una qualsiasi delle interfacce, da DATA 1 a DATA 3, per accedere all'interfaccia utente Web locale all'indirizzo `https://<IP address of a data network interface>`. 

Dopo il completamento della configurazione del dispositivo, è possibile connettersi alle condivisioni del dispositivo stesso e copiarvi i dati dal computer. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Cablare il Data Box
> * Connettersi al Data Box

Passare all'esercitazione successiva per informazioni su come copiare i dati sul Data Box.

> [!div class="nextstepaction"]
> [Copiare i dati in Azure Data Box](./data-box-deploy-copy-data.md)

