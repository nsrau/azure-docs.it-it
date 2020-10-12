---
title: Aprire e salvare file con pacchetti SSIS distribuiti in Azure
description: Informazioni su come aprire e salvare file in locale e in Azure quando si esegue la migrazione in modalità lift-and-shift di pacchetti SSIS che usano file system locali in SSIS in Azure
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 36660854b9a7ae13431545392ef551694b48e97c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82628913"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Aprire e salvare file in locale e in Azure con pacchetti SSIS distribuiti in Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive come aprire e salvare file in locale e in Azure quando si esegue la migrazione in modalità lift-and-shift di pacchetti SSIS che usano file system locali in SSIS in Azure.

## <a name="save-temporary-files"></a>Salvare i file temporanei

Se è necessario archiviare ed elaborare i file temporanei durante l'esecuzione di un singolo pacchetto, i pacchetti possono usare la directory di lavoro corrente (`.`) o la cartella temporanea (`%TEMP%`) dei nodi del runtime di integrazione SSIS di Azure.

## <a name="use-on-premises-file-shares"></a>Usare condivisioni di file in locale

Per continuare a usare **condivisioni file locali** quando si spostano pacchetti che usano i file system locali in SSIS in Azure, effettuare le operazioni seguenti:

1. Trasferire i file dai file system locali alle condivisioni file locali.

2. Aggiungere le condivisioni file locali a una rete virtuale di Azure.

3. Aggiungere il runtime di integrazione Azure-SSIS alla stessa rete virtuale. Per altre informazioni, vedere [Aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Connettere il runtime di integrazione Azure-SSIS alle condivisioni file locali all'interno della stessa rete virtuale impostando le credenziali di accesso che usano l'autenticazione di Windows. Per altre informazioni, vedere [Connettersi a dati e condivisioni file con autenticazione di Windows](ssis-azure-connect-with-windows-auth.md).

5. Aggiornare i percorsi di file locali nei pacchetti in base ai percorsi UNC che puntano alle condivisioni file locali. Ad esempio, aggiornare `C:\abc.txt` a `\\<on-prem-server-name>\<share-name>\abc.txt`.

## <a name="use-azure-file-shares"></a>Usare le condivisione file di Azure

Per usare **File di Azure** quando si spostano pacchetti che usano i file system locali in SSIS in Azure, effettuare le operazioni seguenti:

1. Trasferire i file dai file system locali a File di Azure. Per altre informazioni, vedere [File di Azure](https://azure.microsoft.com/services/storage/files/).

2. Connettere il runtime di integrazione SSIS di Azure a File di Azure impostando le credenziali di accesso che usano l'autenticazione di Windows. Per altre informazioni, vedere [Connettersi a dati e condivisioni file con autenticazione di Windows](ssis-azure-connect-with-windows-auth.md).

3. Aggiornare i percorsi di file locali nei pacchetti in base ai percorsi UNC che puntano a File di Azure. Ad esempio, aggiornare `C:\abc.txt` a `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`.

## <a name="next-steps"></a>Passaggi successivi

- Distribuire i pacchetti. Per altre informazioni, vedere [Distribuire un progetto SSIS con SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Eseguire i pacchetti. Per altre informazioni, vedere [Eseguire pacchetti SSIS in Azure con SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Pianificare i pacchetti. Per altre informazioni, vedere [pianificare pacchetti SSIS in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).
