---
title: Usare l'interfaccia della riga di comando di Databricks da Azure Cloud Shell | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando di Databricks da Azure Cloud Shell.
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 8e4213813ff23586ac683556d4a3c0c587edea58
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Usare l'interfaccia della riga di comando di Databricks da Azure Cloud Shell

Informazioni su come usare l'interfaccia della riga di comando di Databricks da Azure Cloud Shell per eseguire operazioni in Databricks.

## <a name="prerequisites"></a>prerequisiti

* Un'area di lavoro e un cluster Azure Databricks. Per istruzioni, vedere [Introduzione ad Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Configurare un token di accesso personale in Databricks. Per istruzioni, vedere [Token management](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management) (Gestione di token).

## <a name="use-the-azure-cloud-shell"></a>Usare Azure Cloud Shell

1. Accedere al [portale di Azure](https://portal.azure.com).
 
2. Nell'angolo superiore destro fare clic sull'icona di **Cloud Shell**.

   ![Avviare Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Avviare ODBC da Excel")

3. Assicurarsi di selezionare **Bash** per l'ambiente Cloud Shell. È possibile effettuare la selezione dell'opzione nel menu a discesa, come illustrato nello screenshot seguente.

   ![Avviare Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Avviare ODBC da Excel") 

4. Creare un ambiente virtuale in cui è possibile installare l'interfaccia della riga di comando di Databricks. Nel frammento di codice seguente si crea un ambiente virtuale denominato `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Passare all'ambiente virtuale creato.

       source databrickscli/bin/activate

6. Installare l'interfaccia della riga di comando di Databricks.

       pip install databricks-cli

7. Configurare l'autenticazione con Databricks usando il token di accesso creato, elencato come uno dei prerequisiti. Usare il comando seguente:

       databricks configure --token

    Si ricevono le richieste seguenti:

    * Viene chiesto di eseguire l'accesso all'host Databricks. Immettere il valore nel formato `https://eastus2.azuredatabricks.net`. In questo caso, **Stati Uniti orientali 2** è l'area di Azure in cui è stata creata l'area di lavoro di Azure Databricks.

    * Viene chiesto di immettere un nome utente. Immettere **token**.

    * Infine, viene chiesto di immettere la password. Immettere il token creato in precedenza.

Una volta completati questi passaggi, è possibile iniziare a usare l'interfaccia della riga di comando di Databricks da Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Usare l'interfaccia della riga di comando di Databricks

È ora possibile iniziare a usare l'interfaccia della riga di comando di Databricks. Eseguire, ad esempio, il comando illustrato di seguito per elencare tutti i cluster Databricks presenti nell'area di lavoro.

    databricks clusters list

È anche possibile usare il comando seguente per accedere al file system di Databricks.

    databricks fs ls


Per informazioni di riferimento complete sui comandi, vedere [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (Interfaccia della riga di comando di Databricks).


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere [Panoramica dell'interfaccia della riga di comando di Azure](../cloud-shell/overview.md)
* Per un elenco di comandi per l'interfaccia della riga di comando di Azure, vedere [CLI reference](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) (Informazioni di riferimento sull'interfaccia della riga di comando)
* Per un elenco di comandi per l'interfaccia della riga di comando di Databricks, vedere [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (Interfaccia della riga di comando di Databricks)


