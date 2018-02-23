---
title: "Scalabilità dei ruoli di lavoro in servizi di App - Stack di Azure | Documenti Microsoft"
description: "Linee guida dettagliate per la scalabilità in servizi di App di Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: a9be9011062f07d59842d417bf6761ec81c39275
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Servizio App nello Stack di Azure: aggiungere più ruoli di infrastruttura o di lavoro
*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*  

Questo documento vengono fornite istruzioni su come ridimensionare il servizio App nei ruoli di lavoro e infrastruttura di Azure Stack. Contiene i passaggi per la creazione di ruoli di lavoro aggiuntivi per supportare le applicazioni di qualsiasi dimensione.

> [!NOTE]
> Se l'ambiente dello Stack di Azure non ha più di 96 GB di RAM è possibile aggiunta di ulteriore capacità difficoltà.

Servizio App nello Stack di Azure, per impostazione predefinita, supporta i piani di lavoro gratuito e condiviso. Per aggiungere altri piani di lavoro, è necessario aggiungere più ruoli di lavoro.

Se non si conoscono ciò che è stato distribuito con il valore predefinito di servizio App in Installazione dello Stack di Azure, è possibile esaminare informazioni aggiuntive nel [servizio App nella panoramica di Azure Stack](azure-stack-app-service-overview.md).

Servizio App di Azure nello Stack di Azure distribuisce tutti i ruoli utilizzando il set di scalabilità di macchine virtuali e di conseguenza sfrutta le funzionalità di scalabilità di questo carico di lavoro. Pertanto, tutti scalabilità dei piani di lavoro viene eseguita tramite l'amministratore di servizio App.

Aggiungere ulteriori processi di lavoro direttamente all'interno di amministratore. Provider di risorse del servizio App

1. Accedere al portale di amministrazione di Stack di Azure come amministratore del servizio.

2. Passare a **servizi App**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Fare clic su **Ruoli**. Qui è visualizzare la suddivisione di tutti i ruoli del servizio App distribuita.

4. Fare clic con il pulsante destro sulla riga del tipo di cui si desidera applicare la scalabilità e quindi fare clic su **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Fare clic su **Scaling**, selezionare il numero di istanze che si desidera scalare di e quindi fare clic su **salvare**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Servizio app di Azure stack verrà ora aggiungere le altre macchine virtuali, configurarli, installare tutti i software necessari e contrassegnarli come pronto quando questo processo è stato completato. Questo processo può richiedere circa 80 minuti.

7. È possibile monitorare lo stato di conformità dei nuovi ruoli visualizzando i lavoratori il **ruoli** blade.

Dopo che sono completamente distribuito e pronto, i processi di lavoro diventi disponibile per gli utenti di distribuire il carico di lavoro su di essi. Di seguito viene riportato un esempio di più livelli di prezzo disponibili per impostazione predefinita. Se sono non presenti processi di lavoro disponibili per un livello di lavoro specifico, non è disponibile l'opzione per scegliere il piano tariffario corrispondente.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Per ampliare gestione, i ruoli Front-End o Publisher aggiungere che è necessario scalare orizzontalmente il set di scalabilità della macchina virtuale corrispondente. Si aggiungerà la possibilità di scalare in orizzontale questi ruoli tramite la gestione dei servizi di App in una versione futura.

Per ampliare gestione, Front-End o ruoli del server di pubblicazione, la stessa procedura selezionando il tipo di ruolo appropriate. Controller non vengono distribuiti come set di scalabilità e pertanto due devono essere distribuiti in fase di installazione per tutte le distribuzioni di produzione.

### <a name="next-steps"></a>Passaggi successivi

[Configurare le origini di distribuzione](azure-stack-app-service-configure-deployment-sources.md)
