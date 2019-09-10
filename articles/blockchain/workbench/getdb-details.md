---
title: Ottenere i dettagli del database di anteprima di Azure blockchain Workbench
description: Informazioni su come ottenere informazioni su database e server di database di Azure blockchain Workbench Preview.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f4a4eaab9a03aeed27e29eb645b6e22a028b243b
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845069"
---
# <a name="get-information-about-your-azure-blockchain-workbench-preview-database"></a>Ottenere informazioni sul database di anteprima di Azure blockchain Workbench

Questo articolo illustra come ottenere informazioni dettagliate sul database di anteprima di Azure blockchain Workbench.

## <a name="overview"></a>Panoramica

Vengono date informazioni sulle applicazioni, i flussi di lavoro e l'esecuzione di contratti smart usando le viste del database nel database SQL di Blockchain Workbench. Gli sviluppatori possono usare queste informazioni quando usano strumenti quali Microsoft Excel, Power BI, Visual Studio e SQL Server Management Studio.

Prima che uno sviluppatore possa connettersi al database è necessario avere:

* Un accesso client esterno consentito nel firewall del database. Questo articolo sulla configurazione del firewall di un database illustra come consentire l'accesso.
* Il nome del server del database e il nome del database.

## <a name="connect-to-the-blockchain-workbench-database"></a>Connettersi al database di Blockchain Workbench

Per connettersi al database:

1. Accedere al portale di Azure con un account che abbia autorizzazioni di **Proprietario** per le risorse di Azure Blockchain Workbench.
2. Nel riquadro di spostamento a sinistra scegliere **Gruppi di risorse**.
3. Scegliere il nome del gruppo di risorse per la distribuzione di Blockchain Workbench.
4. Selezionare **Tipo** per ordinare l'elenco di risorse e quindi scegliere **SQL server**. L'elenco ordinato dello screenshot successivo mostra due database SQL, "master" e uno che usa "lhgn" come **prefisso della risorsa**.

   ![Elenco ordinato delle risorse di Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Per visualizzare informazioni dettagliate relative al database di Blockchain Workbench, selezionare il collegamento per il database con il **prefisso della risorsa** indicato nella distribuzione di Blockchain Workbench.

   ![Dettagli del database](./media/getdb-details/workbench-db-details.png)

Il nome del server del database e il nome del database consentono di connettersi al database di Blockchain Workbench usando lo strumento di sviluppo o quello di creazione di report.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Viste di database in Azure Blockchain Workbench)