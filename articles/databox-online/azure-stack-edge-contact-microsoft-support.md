---
title: Registrare il ticket di supporto per Azure Stack Edge Azure Data Box Gateway | Microsoft Docs
description: Informazioni su come registrare la richiesta di supporto per i problemi relativi al Azure Stack Edge o Data Box Gateway ordini.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 3839fb325b1ed0c052f7a4e8955e9a9fda51fc5f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569654"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-and-azure-data-box-gateway"></a>Aprire un ticket di supporto per Azure Stack Edge e Azure Data Box Gateway

Questo articolo si applica a Azure Stack Edge e Azure Data Box Gateway entrambi gestiti dal servizio Azure Stack Edge/Azure Data Box Gateway. Se si verificano problemi con il servizio, è possibile creare una richiesta di assistenza per il supporto tecnico. In questo articolo viene descritto:

* Come creare una richiesta di supporto
* Come gestire una richiesta di supporto relativa al ciclo di vita all'interno del portale.

## <a name="create-a-support-request"></a>Creare una richiesta di supporto

Per creare una richiesta di supporto, attenersi alla procedura seguente.

1. Passare a Azure Stack Edge o Data Box Gateway ordine. Passare alla sezione **supporto e risoluzione dei problemi** e quindi selezionare **nuova richiesta di supporto**.
   
2. In **nuova richiesta di supporto**, nella scheda **nozioni di base** , seguire questa procedura:
    
    1. Nell'elenco a discesa **Tipo di problema** selezionare **Tecnico**.
    2. Scegliere la **sottoscrizione**.
    3. In **Servizio** selezionare **Servizi personali**. Nell'elenco a discesa selezionare **Azure stack Edge e data box gateway**.
    4. Selezionare la **risorsa**. Corrisponde al nome dell'ordine.
    5. Fornire un breve **Riepilogo** del problema riscontrato. 
    6. Selezionare il **tipo di problema**.
    7. In base al tipo di problema selezionato, scegliere un **sottotipo di problema**corrispondente.
    8. Selezionare **Avanti: soluzioni >>**.

        ![Nozioni di base](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. Nella scheda **Dettagli** seguire questa procedura:
    
    1. Specificare la data e l'ora di inizio del problema.
    2. Specificare una **Descrizione** per il problema.
    3. In **caricamento file**selezionare l'icona della cartella per esplorare tutti gli altri file che si vuole caricare.
    4. Selezionare **Condividi informazioni diagnostica**.
    5. In base alla sottoscrizione, viene popolato automaticamente un **piano di supporto** .
    6. Nell'elenco a discesa selezionare il livello di **gravità**.
    7. Specificare un **metodo di contatto preferito**.
    8. Le **ore di risposta** vengono selezionate automaticamente in base al piano di sottoscrizione.
    9. Fornire la lingua preferita per il supporto.
    10. Nelle **informazioni di contatto**specificare nome, indirizzo di posta elettronica, telefono, contatto facoltativo, paese/area geografica. Il supporto tecnico Microsoft usa queste informazioni per contattare l'utente per altre informazioni, la diagnostica e la risoluzione. 
    11. Selezionare **Avanti: rivedere + crea >>**.

        ![Problema](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. Nella scheda **Verifica e crea** esaminare le informazioni relative al ticket di supporto. Selezionare **Crea**. 

    ![Problema](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    Dopo aver creato il ticket di supporto, un tecnico del supporto contatterà il prima possibile per procedere con la richiesta.

## <a name="get-hardware-support"></a>Ottenere supporto hardware

Queste informazioni si applicano solo a Azure Stack dispositivo. Il processo per segnalare problemi hardware è il seguente:

1. Aprire un ticket di supporto dal portale di Azure per un problema hardware. In **tipo di problema**selezionare **Azure stack hardware**. Scegliere il **sottotipo di problema** come **errore hardware**. 

    ![Problema hardware](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Dopo aver creato il ticket di supporto, un tecnico del supporto contatterà il prima possibile per procedere con la richiesta. 

2. Se supporto tecnico Microsoft determina che si tratta di un problema hardware, viene eseguita una delle azioni seguenti: 

    - Viene inviata un'unità di sostituzione dei campi (FRU) per la parte hardware non riuscita. Attualmente, l'unità di alimentazione è l'unica FRU supportata. 
    - Per qualsiasi altro errore della parte, Microsoft esegue una sostituzione completa del sistema (FSR) o uno scambio di dispositivi.

3. Se viene generato un ticket di supporto prima dell'ora locale 4:30 PM (dal lunedì al venerdì), un tecnico in sede invia il giorno lavorativo successivo alla propria sede per eseguire una FRU o una sostituzione completa del dispositivo.

## <a name="manage-a-support-request"></a>Gestire una richiesta di supporto

Dopo la creazione del ticket di supporto, sarà possibile gestire il ciclo di vita del ticket dal portale.

#### <a name="to-manage-your-support-requests"></a>Per gestire le richieste di supporto

1. Per visualizzare la pagina Guida e supporto, passare a **Sfoglia -> Guida e supporto**.

    ![Gestire le richieste di supporto](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)   

2. Verrà visualizzato l'elenco tabulare **Richieste di supporto recenti** in **Guida e supporto**.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Selezionare e fare clic su una richiesta di supporto. È possibile visualizzare lo stato e i dettagli della richiesta. Fare clic su **+ Nuovo messaggio** se si vuole seguire la richiesta.

   
## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [risolvere i problemi relativi a Azure stack Edge](azure-stack-edge-troubleshoot.md).
Informazioni su come [risolvere i problemi relativi a data box gateway](data-box-gateway-troubleshoot.md).
