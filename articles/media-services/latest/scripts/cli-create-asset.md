---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un asset di Servizi multimediali di Azure | Microsoft Docs
description: Lo script dell'interfaccia della riga di comando di Azure in questo argomento mostra come creare un asset di Servizi multimediali di Azure in cui caricare contenuto.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 76d2adf3c408e174081c3106e07a87f751b4a179
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612662"
---
# <a name="cli-example-create-an-asset"></a>Esempio dell'interfaccia della riga di comando: Creare un asset

Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come creare un asset di Servizi multimediali di Azure in cui caricare contenuto.

## <a name="prerequisites"></a>Prerequisiti 

- Installare e usare l'interfaccia della riga di comando in locale. Per questo articolo è necessaria l'interfaccia della riga di comando di Azure 2.0 o versione successiva. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

    Attualmente, non tutti i comandi delle [interfacce della riga di comando di Servizi multimediali v3](https://aka.ms/ams-v3-cli-ref) funzionano in Azure Cloud Shell. È consigliabile usare l'interfaccia della riga di comando solo in locale.

- [Creare un account di Servizi multimediali di Azure](../create-account-cli-how-to.md).

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere [Azure CLI samples](../cli-samples.md) (Esempi dell'interfaccia della riga di comando di Azure).
