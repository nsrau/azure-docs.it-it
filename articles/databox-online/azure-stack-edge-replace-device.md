---
title: Sostituire il dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Viene descritto come ottenere una sostituzione Azure Stack dispositivo Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: ec16a2b42b818e96399b8fdbad4a0951f84ef825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893902"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Sostituire il dispositivo Azure Stack Edge Pro

Questo articolo descrive come ottenere una sostituzione Azure Stack dispositivo Edge Pro. Un dispositivo sostitutivo è necessario quando il dispositivo esistente presenta un errore hardware o necessita di un aggiornamento. 


In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Aprire un ticket di supporto per problemi hardware
> * Crea una nuova risorsa per il dispositivo sostitutivo in portale di Azure
> * Installare, attivare il dispositivo sostitutivo
> * Restituisce il dispositivo originale

## <a name="open-a-support-ticket"></a>Aprire un ticket di supporto

Se si verifica un errore hardware nel dispositivo esistente, aprire un ticket di supporto. Il supporto tecnico Microsoft determinerà che un'unità sostituibile sul campo non è disponibile per questa istanza o che l'hardware del dispositivo deve essere aggiornato. In entrambi i casi, il supporto tecnico ordinerà un dispositivo sostitutivo.

1. Aprire un ticket di supporto con il supporto tecnico Microsoft indicando che si vuole restituire il dispositivo. Selezionare il tipo di problema come **hardware di Azure stack Edge Pro**.

    ![Aprire il ticket di supporto](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Si verrà contattati da un tecnico del supporto tecnico Microsoft. Specificare i dettagli di spedizione.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Creare una risorsa per il dispositivo sostitutivo

Per creare una risorsa, seguire questa procedura.

1. Eseguire la procedura descritta in [creare una nuova risorsa](azure-stack-edge-deploy-prep.md#create-a-new-resource) per creare una risorsa per il dispositivo sostitutivo. 

2. Assicurarsi di selezionare la casella di controllo **se si dispone di un dispositivo Azure stack Edge Pro**. 

    ![Risorsa per il dispositivo sostitutivo](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>Installare e attivare il dispositivo sostitutivo

Attenersi alla seguente procedura per installare e attivare il dispositivo sostitutivo:

1. [Installare il dispositivo](azure-stack-edge-deploy-install.md).

2. [Attivare il dispositivo](azure-stack-edge-deploy-connect-setup-activate.md) con la nuova risorsa creata in precedenza.

## <a name="return-your-existing-device"></a>Restituisce il dispositivo esistente

Seguire tutti i passaggi per restituire il dispositivo originale:

1. [Cancellare i dati contenuti nel dispositivo](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Avviare il ritorno del dispositivo](azure-stack-edge-return-device.md#initiate-device-return) per il dispositivo originale.
3. [Pianificare un ritiro](azure-stack-edge-return-device.md#schedule-a-pickup).
4. [Eliminare la risorsa](azure-stack-edge-return-device.md#delete-the-resource) associata al dispositivo restituito.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [restituire un dispositivo Azure stack Edge Pro](azure-stack-edge-return-device.md).
