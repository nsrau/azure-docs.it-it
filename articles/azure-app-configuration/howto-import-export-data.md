---
title: Importare o esportare dati con la configurazione di app Azure
description: Informazioni su come importare o esportare i dati di configurazione in o da app Azure configurazione. Scambiare dati tra l'archivio di configurazione dell'app e il progetto di codice.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 21eba653bcd853db9550d0d3781aacd281884605
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588076"
---
# <a name="import-or-export-configuration-data"></a>Importare o esportare dati di configurazione

App Azure configurazione supporta le operazioni di importazione ed esportazione dei dati. Usare queste operazioni per lavorare con i dati di configurazione in blocco e scambiare dati tra l'archivio di configurazione dell'app e il progetto di codice. Ad esempio, è possibile configurare un archivio di configurazione delle app per il test e un altro per la produzione. È possibile copiare le impostazioni dell'applicazione in modo da non dover immettere due volte i dati.

Questo articolo fornisce una guida per l'importazione e l'esportazione dei dati con la configurazione dell'app. Se si vuole configurare una sincronizzazione continuativa con il repository GitHub, vedere l' [azione GitHub](https://aka.ms/azconfig-gha1).

## <a name="import-data"></a>Importa dati

L'importazione porta i dati di configurazione in un archivio di configurazione dell'app da un'origine esistente. Usare la funzione Import per eseguire la migrazione dei dati in un archivio di configurazione dell'app o aggregare dati da più origini. La configurazione dell'app supporta l'importazione da un file JSON, YAML o Properties.

Importare i dati usando l'interfaccia della riga di comando di [portale di Azure](https://portal.azure.com) o [Azure](./scripts/cli-import.md). Seguire questa procedura nel portale di Azure:

1. Passare all'archivio di configurazione dell'app e selezionare **Importa/Esporta** dal menu **operazioni** .

1. Nella scheda **Importa** selezionare file di configurazione del **servizio di origine**  >  **Configuration File**.

1. Selezionare **per lingua** e selezionare il tipo di input desiderato.

1. Selezionare l'icona della **cartella** e selezionare il file da importare.

    ![File da importare](./media/import-file.png)

1. Selezionare un **separatore**ed eventualmente immettere un **prefisso** da usare per i nomi delle chiavi importate.

1. Facoltativamente, selezionare un' **etichetta**.

1. Selezionare **applica** per completare l'importazione.

    ![Importazione file completata](./media/import-file-complete.png)

## <a name="export-data"></a>Esportare i dati

Esporta scrive i dati di configurazione archiviati nella configurazione dell'app in un'altra destinazione. Usare la funzione Export, ad esempio, per salvare i dati in un archivio di configurazione dell'app in un file incorporato con il codice dell'applicazione durante la distribuzione.

Esportare i dati usando il [portale di Azure](https://portal.azure.com) o l'interfaccia della riga di comando di [Azure](./scripts/cli-export.md). Seguire questa procedura nel portale di Azure:

1. Individuare l'archivio di configurazione dell'app e selezionare **Importa/Esporta**.

1. Nella scheda **Esporta** selezionare il file di configurazione del **servizio di destinazione**  >  **Configuration File**.

1. Immettere facoltativamente un **prefisso** e selezionare un' **etichetta** e un punto nel tempo per le chiavi da esportare.

1. Selezionare un **File type**  >  **separatore**di tipo di file.

1. Selezionare **applica** per completare l'esportazione.

    ![Il file di esportazione è terminato](./media/export-file-complete.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
