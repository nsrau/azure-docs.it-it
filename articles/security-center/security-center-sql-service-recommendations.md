---
title: Consigli sull'archiviazione & dati - Centro sicurezza di AzureData & storage recommendations - Azure Security Center
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione dei dati e del servizio SQL di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552866"
---
# <a name="protect-azure-data-and-storage-services"></a>Proteggere i servizi di archiviazione e dati di AzureProtect Azure data and storage services
Quando il Centro sicurezza di Azure identifica potenziali vulnerabilità della sicurezza, crea suggerimenti che guidano l'utente attraverso il processo di configurazione dei controlli necessari per la protezione e la protezione delle risorse.

In questo articolo viene illustrata la **pagina Sicurezza dei dati** della sezione relativa alla sicurezza delle risorse del Centro sicurezza.

Per un elenco completo dei consigli che potrebbero essere visualizzati in questa pagina, vedere Suggerimenti per [l'archiviazione](recommendations-reference.md#recs-datastorage)e dati .


## <a name="view-your-data-security-information"></a>Visualizzare le informazioni di sicurezza dei dati

1. Nella sezione **Igiene** sicurezza risorse fare clic su **Risorse dati e archiviazione**.

    ![Risorse di archiviazione & datiData & storage resources](./media/security-center-monitoring/click-data.png)

    Verrà visualizzata la pagina **Sicurezza dei dati** con suggerimenti per le risorse dati.

    [![Risorse dati](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    In questa pagina è possibile effettuare le operazioni seguenti:

    * Fare clic sulla scheda **Panoramica** per visualizzare tutti i suggerimenti per le risorse dati da correggere. 
    * Fare clic su ogni scheda e visualizzare i suggerimenti in base al tipo di risorsa.

    > [!NOTE]
    > Per altre informazioni sulla crittografia di archiviazione, vedere Crittografia di Archiviazione di Azure per i [dati inattivi.](../storage/common/storage-service-encryption.md)


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Correggere una raccomandazione su una risorsa di datiRemediate a recommendation on a data resource

1. In una delle schede delle risorse fare clic su una risorsa. Viene visualizzata la pagina delle informazioni in cui sono elencati i consigli da correggere.

    ![Informazioni sulle risorse](./media/security-center-monitoring/sql-recommendations.png)

2. Fare clic su un suggerimento. Verrà visualizzata la pagina Raccomandazione con i passaggi di **correzione** per implementare la raccomandazione.

   ![Passaggi di correzioneRemediation steps](./media/security-center-monitoring/remediate1.png)

3. Fare clic su **Azione.** Viene visualizzata la pagina delle impostazioni delle risorse.

    ![Abilita raccomandazione](./media/security-center-monitoring/remediate2.png)

4. Seguire i passaggi di **correzione** e fare clic su **Salva**.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle raccomandazioni che si applicano ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:To learn more about recommendations that apply to other Azure resource types, see the following topics:

* [Elenco di riferimento completo dei consigli per la sicurezza del Centro sicurezza di Azure](recommendations-reference.md)
* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)