---
title: Rispedire Microsoft Azure Data Box | Microsoft Docs
description: Informazioni su come spedire Azure Data Box a Microsoft
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
ms.openlocfilehash: 05bfba9fffa7db75ef4e1a1167b3170a775e1d34
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785461"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Esercitazione: Restituire Azure Data Box e verificare il caricamento dei dati in Azure

Questa esercitazione illustra come restituire Azure Data Box e verificare che i dati siano stati caricati in Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Spedire il Data Box a Microsoft
> * Verificare il caricamento dei dati in Azure
> * Cancellare i dati dal Data Box

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di aver completato l'[Esercitazione: Copiare i dati in Azure Data Box ed eseguire la verifica](data-box-deploy-copy-data.md).

## <a name="ship-data-box-back"></a>Rispedire il Data Box

1. Verificare che il dispositivo sia spento e i cavi scollegati. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
2. Se il dispositivo viene spedito negli Stati Uniti, assicurarsi che l'etichetta indirizzo sia visibile sul display E-ink e prenotare un corriere per il ritiro. Se l'etichetta è danneggiata, è stata smarrita o non è visibile sul display E-ink, scaricarne una nuova dal portale di Azure e applicarla sul dispositivo. Passare a **Panoramica > Scarica etichetta di spedizione**. 

    Se il dispositivo viene spedito in Europa, l'etichetta di spedizione non viene visualizzata sullo schermo E-ink. L'etichetta indirizzo per la spedizione di ritorno è invece inserita all'interno dell'involucro trasparente sotto l'etichetta indirizzo di inoltro. Rimuovere l'etichetta indirizzo precedente e verificare che l'etichetta di spedizione sia chiaramente visibile.
    
3. Pianificare un ritiro con UPS per la restituzione negli Stati Uniti. Se i dischi vengono rispediti in Europa con DHL, richiedere un ritiro a DHL, visitando il sito Web e specificando il numero di lettera di vettura. Passare al sito Web di DHL Express del paese e scegliere **Prenota il ritiro di un reso > Richiedere ritiro**. 

    Specificare il numero di lettera di vettura e fare clic su **Schedule Pickup** (Pianifica ritiro) per organizzare il ritiro.

4. Dopo che il Data Box è stato ritirato ed esaminato dal corriere, lo stato dell'ordine nel portale diventa **Ritirato**. Viene visualizzato anche un ID di traccia.

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

Quando Microsoft riceve e analizza il dispositivo, lo stato dell'ordine diventa **Ricevuto**. Il dispositivo viene quindi sottoposto a una verifica fisica per rilevare eventuali danni o segni di manomissione. 

Al termine della verifica, il Data Box viene connesso alla rete nel data center di Azure. La copia dei dati viene avviata automaticamente. A seconda delle dimensioni dei dati, l'operazione di copia potrebbe richiedere da alcune ore a giorni per il completamento. È possibile monitorare lo stato di avanzamento del processo di copia nel portale.

Al termine della copia, lo stato dell'ordine diventa **Completato**.

Verificare la presenza dei dati negli account di archiviazione prima di eliminarli dall'origine. 

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box
 
 Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Spedire il Data Box a Microsoft
> * Verificare il caricamento dei dati in Azure
> * Cancellare i dati dal Data Box

Passare all'articolo successivo per informazioni su come gestire il Data Box tramite l'interfaccia utente Web locale.

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare Azure Data Box](./data-box-local-web-ui-admin.md)


