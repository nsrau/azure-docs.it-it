---
title: Collega AWS CloudTrail ad Azure Sentinel Documenti Microsoft
description: Scopri come connettere i dati AWS CloudTrail ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588655"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Collega Azure Sentinel ad AWS CloudTrail

Utilizza il connettore AWS per trasmettere in streaming tutti gli eventi AWS CloudTrail in Azure Sentinel. Questo processo di connessione delega l'accesso per Azure Sentinel ai log delle risorse AWS, creando una relazione di trust tra AWS CloudTrail e Azure Sentinel. Questa operazione viene eseguita in AWS creando un ruolo che concede l'autorizzazione ad Azure Sentinel per accedere ai log AWS.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre dell'autorizzazione di scrittura nell'area di lavoro di Azure Sentinel.You must have write permission on the Azure Sentinel workspace.

> [!NOTE]
> Azure Sentinel raccoglie gli eventi CloudTrail da tutte le aree geografiche. Si consiglia di non trasmettere eventi da un'area a un'altra.

## <a name="connect-aws"></a>Connettere AWS 


1. In Azure Sentinel seleziona **Connettori dati** e quindi seleziona la riga **Amazon Web Services** nella tabella e nel riquadro AWS a destra fai clic su Apri pagina **connettore**.

1. Seguire le istruzioni in **Configurazione** attenendosi alla procedura seguente.
 
1.  Nella console di Amazon Web Services, in **Sicurezza, identità & conformità**, selezionare **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Scegliere **Ruoli** e selezionare **Crea ruolo**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Scegli **un altro account AWS.** Nel campo **ID account** immettere l'ID **account Microsoft** (**123412341234**) disponibile nella pagina del connettore AWS nel portale di Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Assicurarsi che **l'opzione Richiedi ID esterno** sia selezionata e quindi immettere l'ID esterno (ID area di lavoro) disponibile nella pagina del connettore AWS nel portale di Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  In **Allega criteri di autorizzazione** selezionare **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Immettere un Tag (Facoltativo).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Immettere quindi un **Nome ruolo** e selezionare il pulsante **Crea ruolo.**

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Nell'elenco Ruoli scegliere il ruolo creato.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Copiare **l'ARN del ruolo**. Nel portale di Azure Sentinel, nella schermata del connettore Amazon Web Services, incollarlo nel campo **Ruolo da aggiungere** e fare clic su **Aggiungi**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi AWS, cerca **AWSCloudTrail**.



## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato illustrato come connettere AWS CloudTrail ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

