---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Distribuire un'applicazione gestita | Microsoft Docs
description: Questo articolo include uno script di esempio dell'interfaccia della riga di comando di Azure che distribuisce una definizione di applicazione gestita di Azure alla sottoscrizione.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 18dbd46df3e5e1f7dc0368b2c007e0c15d76decc
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330165"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Distribuire un'applicazione gestita per il catalogo di servizi con l'interfaccia della riga di comando di Azure

Questo script distribuisce una definizione di applicazione gestita dal catalogo di servizi. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per distribuire l'applicazione gestita. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | Crea un'applicazione gestita. Fornire l'ID della definizione e i parametri per il modello. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).
