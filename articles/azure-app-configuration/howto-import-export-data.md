---
title: Importare o esportare dati con la configurazione di app Azure | Microsoft Docs
description: Informazioni su come importare o esportare dati da o verso la configurazione di app Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9780dc34aa6b146fe62b11586cbab46825e60535
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185163"
---
# <a name="import-or-export-configuration-data"></a>Importare o esportare dati di configurazione

App Azure configurazione supporta le operazioni di importazione ed esportazione dei dati. Usare queste operazioni per lavorare con i dati di configurazione in blocco e scambiare dati tra l'archivio di configurazione dell'app e il progetto di codice. Ad esempio, è possibile configurare un archivio di configurazione delle app per il test e un altro per la produzione. È quindi possibile copiare le impostazioni dell'applicazione tra di essi tramite un file in modo da non dover immettere due volte i dati.

Questo articolo fornisce una guida per l'importazione e l'esportazione dei dati con la configurazione dell'app.

## <a name="import-data"></a>Importa dati

L'importazione porta i dati di configurazione in un archivio di configurazione dell'app da un'origine esistente, invece di immetterli manualmente. Usare la funzione Import per eseguire la migrazione dei dati in un archivio di configurazione dell'app o aggregare dati da più origini. La configurazione dell'app supporta l'importazione da un file JSON, YAML o Properties.

Importare i dati usando l'interfaccia della riga di comando di [portale di Azure](https://portal.azure.com) o [Azure](./scripts/cli-import.md). Seguire questa procedura nel portale di Azure:

1. Individuare l'archivio di configurazione dell'app e selezionare **Importa/Esporta**.

2. Nella scheda **Importa** selezionare servizio di **origine** > **file di configurazione**.

3. Selezionare **per lingua** > **tipo di file**.

4. Selezionare l'icona della **cartella** e selezionare il file da importare.

    ![Importa file](./media/import-file.png)

5. Selezionare un **separatore**ed eventualmente immettere un **prefisso** da usare per i nomi delle chiavi importate.

6. Facoltativamente, selezionare un' **etichetta**.

7. Selezionare **applica** per completare l'importazione.

    ![Importazione file completata](./media/import-file-complete.png)

## <a name="export-data"></a>Esportazione dei dati

Esporta scrive i dati di configurazione archiviati nella configurazione dell'app in un'altra destinazione. Usare la funzione Export, ad esempio, per salvare i dati in un archivio di configurazione dell'app in un file incorporato con il codice dell'applicazione durante la distribuzione.

Esportare i dati usando il [portale di Azure](https://portal.azure.com) o l'interfaccia della riga di comando di [Azure](./scripts/cli-export.md). Seguire questa procedura nel portale di Azure:

1. Individuare l'archivio di configurazione dell'app e selezionare **Importa/Esporta**.

2. Nella scheda **Esporta** selezionare servizio di **destinazione** > **file di configurazione**.

3. Immettere facoltativamente un **prefisso** e selezionare un' **etichetta** e un punto nel tempo per le chiavi da esportare.

4. Selezionare un **tipo di File** > **separatore**.

5. Selezionare **applica** per completare l'esportazione.

    ![Il file di esportazione è terminato](./media/export-file-complete.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
