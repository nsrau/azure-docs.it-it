---
title: Usare il portale di Azure per risolvere i problemi relativi a Azure Stack Edge Pro | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi a Azure Stack Edge Pro. È possibile eseguire la diagnostica, raccogliere informazioni per il supporto e usare i log per la risoluzione dei problemi.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: alkohli
ms.openlocfilehash: f668136713024ba57d72bf5a457cd249742a3b11
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742027"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-issues"></a>Risolvere i problemi relativi a Azure Stack Edge Pro

Questo articolo descrive come risolvere i problemi di Azure Stack Edge Pro. 

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Eseguire la diagnostica
> * Ottenere il pacchetto per il supporto
> * Usare i log per risolvere i problemi

## <a name="run-diagnostics"></a>Eseguire la diagnostica

Per diagnosticare e risolvere gli errori di qualsiasi dispositivo, è possibile eseguire i test di diagnostica. Per eseguire i test di diagnostica, usare la procedura seguente nell'interfaccia utente Web locale.

1. Nell'interfaccia utente Web locale passare a **Risoluzione dei problemi > Test diagnostici**. Selezionare il test che si desidera eseguire e selezionare **Esegui test**. Vengono eseguiti i test per diagnosticare eventuali problemi con la rete, il dispositivo, il proxy Web, l'ora o le impostazioni cloud. Si riceve una notifica che indica che il dispositivo sta eseguendo i test.

    ![Selezionare i test](media/azure-stack-edge-troubleshoot/run-diag-1.png)

2. Al termine dei test, vengono visualizzati i risultati.

    ![Esaminare i risultati del test](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    Se un test non riesce, viene visualizzato un URL per l'azione consigliata. Selezionare l'URL per visualizzare l'azione consigliata.

    ![Esaminare gli avvisi per i test non superati](media/azure-stack-edge-troubleshoot/run-diag-3.png)

## <a name="collect-support-package"></a>Ottenere il pacchetto per il supporto

Un pacchetto di log è costituito da tutti i log rilevanti utili al supporto tecnico Microsoft nella risoluzione dei problemi del dispositivo. Un pacchetto di log può essere generato con l'interfaccia utente Web locale.

Per ottenere un pacchetto di supporto, usare la procedura seguente.

1. Nell'interfaccia utente Web locale passare a **Risoluzione dei problemi > Supporto**. Selezionare **Crea pacchetto**per il supporto. Il sistema inizia a ottenere il pacchetto per il supporto. Questa operazione potrebbe richiedere alcuni minuti.

    ![Fare clic su Aggiungi utente](media/azure-stack-edge-troubleshoot/collect-logs-1.png)

2. Dopo aver creato il pacchetto per il supporto, selezionare **Scarica pacchetto**per il supporto. Un pacchetto compresso viene scaricato nel percorso selezionato. È possibile decomprimere il pacchetto e visualizzare i file di log del registro di sistema.

    ![Fare clic su Aggiungi utente 2](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Usare i log per risolvere i problemi

Gli eventuali errori riscontrati durante i processi di caricamento e aggiornamento vengono inclusi nei file di log degli errori corrispondenti.

1. Per visualizzare i file degli errori, passare alla condivisione e selezionarla per visualizzare il contenuto. 

      ![Connettersi alla condivisione e visualizzarne il contenuto](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. Selezionare la _cartella Pro di Microsoft Azure stack Edge_. Questa cartella contiene due sottocartelle:

    * Una cartella di caricamento contenente i file di log per gli errori di caricamento
    * Una cartella di aggiornamento per gli errori durante l'aggiornamento

    Ecco come appare un file di log per l'aggiornamento:

    ```xml
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ```

3. Quando viene visualizzato un errore in questo file (evidenziato nell'esempio), annotare il codice di errore, in questo caso è 16001. Cercare la descrizione di questo codice di errore con il riferimento di errore seguente.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [problemi noti in questa versione](data-box-gateway-release-notes.md).
