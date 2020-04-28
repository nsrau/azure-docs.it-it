---
title: Consigli sull'archiviazione dei dati &-Centro sicurezza di Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552866"
---
# <a name="protect-azure-data-and-storage-services"></a>Proteggere i servizi di archiviazione e dati di Azure
Quando il Centro sicurezza di Azure identifica le potenziali vulnerabilità di sicurezza, viene creato un Consiglio che guida l'utente nel processo di configurazione dei controlli necessari per rafforzare e proteggere le risorse.

Questo articolo illustra la **pagina sicurezza dati** della sezione relativa alla sicurezza delle risorse del Centro sicurezza.

Per un elenco completo delle raccomandazioni che è possibile visualizzare in questa pagina, vedere [indicazioni sui dati e sull'archiviazione](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Visualizzare le informazioni sulla sicurezza dei dati

1. Nella sezione **igiene della sicurezza delle risorse** fare clic su **risorse di archiviazione e dati**.

    ![Risorse di archiviazione & dati](./media/security-center-monitoring/click-data.png)

    Verrà visualizzata la pagina **sicurezza dati** con le raccomandazioni per le risorse di dati.

    [![Risorse dati](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    In questa pagina è possibile effettuare le operazioni seguenti:

    * Fare clic sulla scheda **Panoramica** per elencare tutte le raccomandazioni sulle risorse dati da correggere. 
    * Fare clic su ogni scheda e visualizzare le raccomandazioni per tipo di risorsa.

    > [!NOTE]
    > Per altre informazioni sulla crittografia di archiviazione, vedere [crittografia di archiviazione di Azure per dati](../storage/common/storage-service-encryption.md)inattivi.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Correggere una raccomandazione su una risorsa di dati

1. In una delle schede delle risorse fare clic su una risorsa. Verrà visualizzata la pagina informazioni che elenca le raccomandazioni da correggere.

    ![Informazioni sulle risorse](./media/security-center-monitoring/sql-recommendations.png)

2. Fare clic su una raccomandazione. Verrà visualizzata la pagina raccomandazione con i **passaggi correttivi** per implementare la raccomandazione.

   ![Procedura di correzione](./media/security-center-monitoring/remediate1.png)

3. Fare clic su **intraprendere un'azione**. Verrà visualizzata la pagina Impostazioni risorsa.

    ![Abilita raccomandazione](./media/security-center-monitoring/remediate2.png)

4. Attenersi alla **procedura di correzione** e fare clic su **Salva**.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Elenco di riferimento completo delle raccomandazioni sulla sicurezza del Centro sicurezza di Azure](recommendations-reference.md)
* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)