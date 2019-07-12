---
title: Connetti i dati di Symantec AWS a Sentinel anteprima di Azure | Microsoft Docs
description: Informazioni su come connettere i dati di Symantec AWS a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673973"
---
# <a name="connect-azure-sentinel-to-aws"></a>Connettere Azure Sentinel per AWS

Usare il connettore AWS per trasmettere tutti i tuoi eventi CloudTrail AWS in Azure Sentinel. Questo processo di connessione delega l'accesso per Azure Sentinel ai log delle risorse di AWS, creare una relazione di trust tra CloudTrail AWS e Azure Sentinel. Questa operazione viene eseguita in AWS mediante la creazione di un ruolo che conceda l'autorizzazione a Azure Sentinel per accedere ai log AWS.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre dell'autorizzazione di scrittura nell'area di lavoro Azure Sentinel.

## <a name="connect-aws"></a>Connettere AWS 
 
1.  In Amazon Web Services console, sotto **Security, Identity & Compliance**, fare clic su **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

2.  Scegli **ruoli** e fare clic su **Crea ruolo**.

    ![AWS2](./media/connect-aws/aws-2.png)

3.  Scegliere **account AWS un'altra.** Nel **ID dell'Account** immettere il **ID Account Microsoft** (**123412341234**) che sono reperibili nella pagina di connettore di AWS nel portale di Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

4.  Assicurarsi che **Richiedi ID esterno** sia selezionata e quindi immettere l'ID esterno (ID area di lavoro) che possono essere trovati nella pagina di connettore di AWS nel portale di Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

5.  Sotto **collegare il criterio le autorizzazioni** selezionate **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

6.  Immettere un Tag (facoltativo).

    ![AWS6](./media/connect-aws/aws-6.png)

7.  Quindi, immettere un **nome del ruolo** e fare clic sui **Crea ruolo** pulsante.

    ![AWS7](./media/connect-aws/aws-7.png)

8.  Nell'elenco dei ruoli, scegliere il ruolo creato.

    ![AWS8](./media/connect-aws/aws-8.png)

9.  Copia il **ARN del ruolo** e incollarlo nella **ruolo a cui aggiungere** campo nel portale di Azure Sentinel.

    ![AWS9](./media/connect-aws/aws-9.png)

10. Per usare lo schema appropriato nel Log Analitica per gli eventi AWS, cercare **AWSCloudTrail**.



## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettersi CloudTrail AWS a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

