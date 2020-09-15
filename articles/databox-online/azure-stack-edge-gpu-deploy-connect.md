---
title: Esercitazione su come connettersi a un dispositivo Azure Stack Edge con GPU, configurarlo e attivarlo nel portale di Azure | Microsoft Docs
description: Esercitazione su come distribuire Azure Stack Edge con GPU, connettersi al dispositivo fisico, configurarlo e attivarlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3b500a7d93d19bcc0cd9c23d64ad079f0abfd353
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421357"
---
# <a name="tutorial-connect-to-azure-stack-edge-with-gpu"></a>Esercitazione: Connettersi ad Azure Stack Edge con GPU

Questa esercitazione descrive come è possibile connettersi a un dispositivo Azure Stack Edge con una GPU su scheda usando l'interfaccia utente Web locale.

Per il completamento del processo di connessione sono necessari circa 5 minuti.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Connettersi a un dispositivo fisico


## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge con GPU, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Connettersi all'installazione dell'interfaccia utente Web locale

1. Configurare la scheda Ethernet nel computer per connettersi al dispositivo Azure Stack Edge con l'indirizzo IP statico 192.168.100.5 e la subnet 255.255.255.0.

2. Collegare il computer alla porta 1 sul dispositivo. Se si connette direttamente il computer al dispositivo (senza switch), usare un cavo crossover o una scheda Ethernet USB. Usare la figura seguente per identificare PORT 1 sul dispositivo.

    ![Backplane del dispositivo cablato](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    Il backplane del dispositivo può avere un aspetto leggermente diverso a seconda del modello esatto ricevuto. Per altre informazioni, vedere [Cablare il dispositivo](azure-stack-edge-gpu-deploy-install.md#cable-the-device).


3. Aprire una finestra del browser e accedere all'interfaccia utente Web locale del dispositivo all'indirizzo `https://192.168.100.10`.  
    È possibile che questa operazione richieda alcuni minuti dopo averla abilitata nel dispositivo.

    Viene visualizzato un messaggio di errore o di avviso in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web. 
   
    ![Messaggio di errore del certificato di sicurezza del sito Web](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Selezionare **Continua in questa pagina Web**.  
    Questi passaggi possono variare in base al browser in uso.

5. Accedere all'interfaccia utente Web del dispositivo. La password predefinita è *Password1*. 
   
    ![Pagina di accesso al dispositivo Azure Stack Edge](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. Quando richiesto, modificare la password di amministratore del dispositivo.  
    La nuova password deve contenere da 8 a 16 caratteri e deve contenere tre dei seguenti tipi di carattere: maiuscolo, minuscolo, numerico e speciale.

A questo punto si è nella pagina **Panoramica** del dispositivo. Il passaggio successivo consiste nel configurare le impostazioni di rete per il dispositivo.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * Prerequisiti
> * Connettersi a un dispositivo fisico


Per informazioni su come configurare le impostazioni di rete nel dispositivo Azure Stack Edge, vedere:

> [!div class="nextstepaction"]
> [Configurare la rete](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
