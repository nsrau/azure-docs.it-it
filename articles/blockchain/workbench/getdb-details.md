---
title: Ottenere i dettagli del database di Azure Blockchain Workbench
description: Informazioni su come ottenere informazioni su database e server di database di Azure blockchain Workbench Preview.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 2b3190a9d042be8ead1ff3d5ef48d4a2a19e8963
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324700"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Ottenere informazioni sul database di Azure Blockchain Workbench

Questo articolo illustra come ottenere informazioni dettagliate sul database di anteprima di Azure blockchain Workbench.

## <a name="overview"></a>Overview

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