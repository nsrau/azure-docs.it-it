---
title: Importare o esportare dati con configurazione delle App di Azure | Microsoft Docs
description: Informazioni su come importare o esportare dati da o verso una configurazione di App di Azure
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
ms.openlocfilehash: 851a8705b3cfa9c88e369af7b961a653dee8fd7a
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959851"
---
# <a name="import-or-export-configuration-data"></a>Importare o esportare dati di configurazione

Configurazione delle App sono supportati dati Azure importare ed esportare le operazioni. Questi consentono di lavorare con i dati di configurazione in blocco come anche come scambiare i dati tra la configurazione dell'app archivio e codice di progetto. Ad esempio, è possibile configurare archivio di configurazione di un'app per i test e un altro per la produzione e Copia impostazioni dell'applicazione tra loro tramite un file in modo che non è necessario immettere due volte i dati.

Questo articolo fornisce una Guida per l'importazione ed esportazione dei dati con configurazione delle App.

## <a name="import-data"></a>Importa dati

Importazione porta configurazione archivio dati in configurazione dell'App da un'origine esistente, invece di immetterle manualmente. È possibile usare la funzione di importazione per eseguire la migrazione dei dati in un archivio di configurazione app o i dati aggregati provenienti da più origini. Configurazione delle App supporta l'importazione da un file JSON, YAML o proprietà.

È possibile importare dati usando il [portale di Azure](https://aka.ms/azconfig/portal) oppure [Azure CLI](./scripts/cli-import.md). Dal portale di Azure, seguire questa procedura:

1. Passare all'archivio di configurazione di app e fare clic su **importazione/esportazione**.

2. Nel **importazione** scheda, scegliere **servizio di origine** e **File di configurazione**.

3. Scegli **per il linguaggio** e **tipo di File**.

4. Fare clic sui **cartella** icona e selezionare il file da importare.

    ![File di importazione](./media/import-file.png)

5. Scegliere una **separatore** e immettere facoltativamente una **prefisso** da utilizzare per i nomi delle chiavi importati.

6. È possibile scegliere una **etichetta**.

7. Fare clic su **applica** per completare l'importazione.

    ![File di importazione completata](./media/import-file-complete.png)

## <a name="export-data"></a>Esportazione dei dati

Esportazione scrive i dati di configurazione archiviati in configurazione dell'App in un'altra destinazione. È possibile usare la funzione di esportazione, ad esempio, per salvare i dati in un archivio di configurazione di app in un file che verrà incorporato con il codice dell'applicazione durante la distribuzione.

È possibile esportare i dati usando il [portale di Azure](https://aka.ms/azconfig/portal) oppure [CLI di Azure](./scripts/cli-export.md). Dal portale di Azure, seguire questa procedura:

1. Passare all'archivio di configurazione di app e fare clic su **importazione/esportazione**.

2. Nel **esportare** scheda, scegliere **servizio di destinazione** e **File di configurazione**.

3. Immettere facoltativamente una **Prefix** e scegliere una **etichetta** e un punto nel tempo per l'esportazione delle chiavi.

4. Scegliere una **tipo di File** e **separatore**.

5. Fare clic su **applica** per completare l'esportazione.

    ![File di esportazione completata](./media/export-file-complete.png)

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Creare un'app Web ASP.NET](./quickstart-aspnet-core-app.md)  
