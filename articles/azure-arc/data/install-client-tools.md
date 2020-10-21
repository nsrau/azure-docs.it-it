---
title: Installare gli strumenti client
description: Installare azdata, kubectl, l'interfaccia della riga di comando di Azure, PSQL, Azure Data Studio (Insider) e l'estensione ARC per Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ecf0f54913f980d879b562eb4aa8063acf6c4772
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320260"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Installare strumenti client per distribuire e gestire servizi dati abilitati per Azure Arc

> [!IMPORTANT]
> Se si esegue l'aggiornamento a una nuova versione mensile, assicurarsi di eseguire anche l'aggiornamento alla versione più recente di Azure Data Studio, lo [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] strumento e le [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] estensioni di Azure Arc per Azure Data Studio.

Questo documento illustra i passaggi per l'installazione di, Azure Data Studio, l'interfaccia della riga di comando di [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure (AZ) e lo strumento dell'interfaccia della riga di comando Kubernetes (kubectl) nel computer client.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Strumenti per la creazione e la gestione di servizi dati abilitati per Azure Arc 

La tabella seguente elenca gli strumenti comuni necessari per la creazione e la gestione dei servizi dati abilitati per Azure Arc e per installare questi strumenti:

| Strumento | Obbligatoria | Descrizione | Installazione |
|---|---|---|---|
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] | Sì | Strumento da riga di comando per l'installazione e la gestione di un cluster Big Data. [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] include anche un'utilità della riga di comando per connettersi ed eseguire query in Azure SQL e SQL Server le istanze e i server Postgres usando i comandi `azdata sql query` (eseguire una singola query dalla riga di comando), `azdata sql shell` (una shell interattiva) `azdata postgres query` e `azdata postgres shell` . | [Installazione](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Sì | Strumento di esperienza avanzata per la connessione e l'esecuzione di query su diversi database, tra cui SQL di Azure, SQL Server, PostrgreSQL e MySQL. Le estensioni per Azure Data Studio offrono un'esperienza di amministrazione per i servizi dati abilitati per Azure Arc. | [Installazione](/sql/azure-data-studio/download-azure-data-studio) |
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] estensione per Azure Data Studio | Sì | Estensione per Azure Data Studio che verrà installata [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] se non è già presente.| Installare dalla raccolta estensioni in Azure Data Studio.|
| Estensione di Azure Arc per Azure Data Studio | Sì | Estensione per Azure Data Studio che fornisce un'esperienza di gestione per i servizi dati abilitati per Azure Arc. Esiste una dipendenza dall' [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] estensione per Azure Data Studio. | Installare dalla raccolta estensioni in Azure Data Studio.|
| Estensione PostgreSQL in Azure Data Studio | No | Estensione PostgreSQL per Azure Data Studio che fornisce funzionalità di gestione per PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Installare dalla raccolta estensioni in Azure Data Studio.|
| INTERFACCIA della riga di comando di Azure (AZ)<sup>1</sup> | Sì | Interfaccia della riga di comando moderna per la gestione dei servizi di Azure. Usato con le distribuzioni AKS e per caricare i dati di inventario e fatturazione di Azure Arc abilitato in Azure. ([Altre informazioni](/cli/azure/?view=azure-cli-latest&preserve-view=true)). | [Installazione](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| INTERFACCIA della riga di comando Kubernetes (kubectl)<sup>2</sup> | Sì | Strumento da riga di comando per la gestione del cluster Kubernetes ([altre informazioni](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | Obbligatorio per alcuni script di esempio. | Strumento da riga di comando per il trasferimento di dati con URL. | [Windows](https://curl.haxx.se/windows/) \| Linux: installare il pacchetto curl |
| OC | Obbligatorio per le distribuzioni di Red Hat OpenShift e Azure RedHat OpenShift. |`oc` è l'interfaccia della riga di comando di Open Shift. | [Installazione dell'interfaccia della riga di comando](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> è necessario usare l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione, se necessario.

<sup>2</sup> è necessario usare la `kubectl` versione 1,13 o successiva. Il numero di versione di `kubectl` deve anche essere compreso tra il numero precedente e quello successivo della versione secondaria del cluster Kubernetes. Se si vuole installare una versione specifica nel client `kubectl`, vedere [Installare il file binario di `kubectl` tramite curl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl). In Windows 10 usare cmd.exe e non Windows PowerShell per eseguire curl.

<sup>3</sup> se si usa PowerShell, curl è un alias per il cmdlet Invoke-WebRequest.

## <a name="next-steps"></a>Passaggi successivi

[Creare il controller di dati di Azure Arc](create-data-controller.md)