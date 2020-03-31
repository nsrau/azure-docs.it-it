---
title: Importare o esportare dati con La configurazione delle app di AzureImport or export data with Azure App Configuration
description: Informazioni su come importare o esportare dati in o da Configurazione app di Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056874"
---
# <a name="import-or-export-configuration-data"></a>Importare o esportare dati di configurazione

Configurazione app di Azure supporta le operazioni di importazione ed esportazione dei dati. Usare queste operazioni per usare i dati di configurazione in blocco e scambiare dati tra l'archivio di configurazione app e il progetto di codice. Ad esempio, è possibile impostare un archivio di configurazione app per il test e un altro per la produzione. È possibile copiare le impostazioni dell'applicazione tra di essi in modo da non dover immettere i dati due volte.

Questo articolo fornisce una guida per l'importazione e l'esportazione di dati con Configurazione app. Se vuoi configurare una sincronizzazione in corso con il tuo repository GitHub, dai un'occhiata alla nostra [azione GitHub](https://aka.ms/azconfig-gha1).

## <a name="import-data"></a>Importa dati

L'importazione porta i dati di configurazione in un archivio di configurazione app da un'origine esistente. Usare la funzione di importazione per eseguire la migrazione dei dati in un archivio di configurazione app o aggregare dati da più origini. Configurazione app supporta l'importazione da un file JSON, YAML o delle proprietà.

Importare dati tramite il [portale di Azure](https://portal.azure.com) o l'interfaccia della riga di comando di [Azure.](./scripts/cli-import.md) Seguire questa procedura nel portale di Azure:

1. Passare all'archivio di configurazione app e selezionare **Importa/Esporta** dal menu **Operazioni.**

1. Nella scheda **Importa** selezionare File di**configurazione**del **servizio** > di origine .

1. Selezionare **Per lingua** e selezionare il tipo di input desiderato.

1. Selezionare l'icona **Cartella** e individuare il file da importare.

    ![File da importare](./media/import-file.png)

1. Selezionare un **separatore**e, facoltativamente, immettere un **Prefisso** da utilizzare per i nomi di chiave importati.

1. Facoltativamente, selezionare **un'etichetta**.

1. Selezionare **Applica** per completare l'importazione.

    ![File di importazione completato](./media/import-file-complete.png)

## <a name="export-data"></a>Esportazione dei dati

L'esportazione scrive i dati di configurazione archiviati in Configurazione app in un'altra destinazione. Usare la funzione di esportazione, ad esempio, per salvare i dati in un archivio di configurazione app in un file incorporato con il codice dell'applicazione durante la distribuzione.

Esportare i dati usando il [portale di Azure](https://portal.azure.com) o l'interfaccia della riga di comando di [Azure.](./scripts/cli-export.md) Seguire questa procedura nel portale di Azure:

1. Passare all'archivio di configurazione app e selezionare **Importa/Esporta**.

1. Nella scheda **Esporta** selezionare File di**configurazione**del **servizio** > di destinazione .

1. Se lo si desidera, immettere un **Prefisso** e selezionare **un'etichetta** e un punto nel tempo per le chiavi da esportare.

1. Selezionare un**separatore** **del tipo di** > file .

1. Selezionare **Applica** per completare l'esportazione.

    ![File di esportazione completato](./media/export-file-complete.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
