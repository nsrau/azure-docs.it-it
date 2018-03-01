---
title: "Creare la capacità di Power BI Embedded nel portale di Azure | Microsoft Docs"
description: "Questo articolo illustra come creare una capacità di Power BI Embedded in Microsoft Azure."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: bf7eb967760338626cd7e0465b16dc570e5f582a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Creare la capacità di Power BI Embedded nel portale di Azure

Questo articolo illustra come creare una capacità di Power BI Embedded in Microsoft Azure. Power BI Embedded semplifica le capacità di Power BI consentendo di aggiungere rapidamente oggetti visivi, report e dashboard straordinari nelle app.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>Prima di iniziare

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

* **Sottoscrizione di Azure**: visitare la pagina [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/) per creare un account.
* **Azure Active Directory**: la sottoscrizione deve essere associata a un tenant Azure Active Directory (AAD). È anche ***necessario effettuare l'accesso ad Azure con un account di tale tenant***. Gli account Microsoft non sono supportati. Per altre informazioni, vedere Autenticazione e autorizzazioni utente.
* **Tenant Power BI:** almeno un account nel tenant AAD deve aver effettuato l'iscrizione a Power BI.
* **Gruppo di risorse**: usare un gruppo di risorse già esistente o [crearne uno nuovo](../azure-resource-manager/resource-group-overview.md).

## <a name="create-a-capacity"></a>Creare una capacità

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Crea una risorsa** > **Dati e analisi**.

3. Nella casella di ricerca, cercare *Power BI Embedded*.

4. In Power BI Embedded, selezionare **Crea**.

5. Inserire le informazioni richieste e quindi selezionare **Crea**.

    ![Campi da compilare per creare la nuova capacità](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |Impostazione |DESCRIZIONE |
    |---------|---------|
    |**Nome risorsa**|Nome che identifica la capacità. Il nome risorsa viene visualizzato nel portale di amministrazione di Power BI oltre che nel portale di Azure.|
    |**Sottoscrizione**|Sottoscrizione per la quale si vuole creare la capacità.|
    |**Gruppo di risorse**|Gruppo di risorse che contiene questa nuova capacità. Scegliere un gruppo di risorse esistente oppure crearne uno nuovo. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).|
    |**Amministratore di capacità di Power BI**|Gli amministratori di capacità di Power BI possono visualizzare la capacità nel portale di amministrazione di Power BI e concedere le autorizzazioni di assegnazione ad altri utenti. Per impostazione predefinita, l'amministratore di capacità è l'account dell'utente. L'amministratore di capacità deve trovarsi all'interno del tenant Power BI.|
    |**Posizione**|Posizione in cui è ospitato Power BI per il tenant. Questa impostazione viene risolta automaticamente e non è possibile selezionare una posizione diversa.|
    |**Piano tariffario**|Selezionare lo SKU (numero vCore e dimensioni memoria) che soddisfa le proprie esigenze.  Per i dettagli, vedere [Prezzi di Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded/).|

6. Selezionare **Create**.

La creazione richiede in genere meno di un minuto e spesso solo alcuni secondi. Se è stata selezionata l'opzione **Aggiungi al dashboard**, passare al dashboard per visualizzare la nuova capacità. In alternativa, passare a **Tutti i servizi** > **Power BI Embedded** per vedere se la capacità è pronta.

![Dashboard del portale di Azure con la capacità di Power BI Embedded](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

Per usare la nuova capacità di Power BI Embedded, passare al portale di amministrazione di Power BI per assegnare le aree di lavoro. Per altre informazioni, vedere [Gestire capacità all'interno di Power BI Premium e Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

Se non è necessario usare questa capacità, sospenderla per interromperne l'addebito. Per altre informazioni, vedere [Sospendere e avviare la capacità di Power BI Embedded nel portale di Azure](pause-start.md).

Per iniziare a incorporare il contenuto di Power BI nell'applicazione, vedere [Incorporare i dashboard, i report e i riquadri di Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)