---
title: Panoramica delle versioni del runtime per Funzioni di Azure
description: La soluzione Funzioni di Azure supporta più versioni del runtime. Informazioni sulle differenze tra le versioni e su come scegliere quella più adatta alle proprie esigenze.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/29/2018
ms.author: glenga
ms.openlocfilehash: 716f2b537a47c6e721c7393cba340a583c7ed064
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092139"
---
# <a name="azure-functions-runtime-versions-overview"></a>Panoramica delle versioni del runtime per Funzioni di Azure

 Esistono due versioni principali del runtime di Funzioni di Azure: 1.x e 2.x. Solo 1. x viene approvata per la produzione. Questo articolo illustra quali sono le novità della versione 2.x, disponibile in anteprima.

| Runtime | Status |
|---------|---------|
|1.x|Disponibile a livello generale|
|2.x|Anteprima<sup>*</sup>|

<sup>*</sup>Per ricevere aggiornamenti importanti sulla versione 2.x, tra cui gli annunci relativi a modifiche di rilievo, guardare il repository degli [annunci del Servizio app di Azure](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE] 
> Questo articolo si riferisce al servizio cloud di Funzioni di Azure. Per informazioni sul prodotto che consente di eseguire Funzioni di Azure in locale, vedere [Panoramica sul runtime di Funzioni di Azure](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Sviluppo multipiattaforma

Il runtime 1.x supporta lo sviluppo e l'hosting solo nel portale o in Windows. Il runtime 2.x viene eseguito in .NET Core e quindi può essere eseguito in tutte le piattaforme supportate da .NET Core, tra cui macOS e Linux. Questo consente scenari di sviluppo e hosting multipiattaforma che non sono possibili con la versione 1.x.

## <a name="languages"></a>Lingue

Il runtime 2.x usa un nuovo modello di estendibilità del linguaggio. Per il momento sono JavaScript e Java a sfruttare questo nuovo modello. I linguaggi sperimentali di Funzioni di Azure 1.x non sono stati aggiornati per usare il nuovo modello, quindi non sono supportati nella versione 2.x. Nella tabella seguente sono indicati i linguaggi di programmazione supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Per altre informazioni, vedere [Linguaggi supportati](supported-languages.md).

## <a name="bindings"></a>Associazioni 

Il runtime 2.x usa un nuovo [modello di estendibilità di associazione](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) che offre questi vantaggi:

* Supporto per le estensioni di associazione di terze parti.
* Separazione di runtime e associazioni. Questo consente di applicare il controllo delle versioni alle estensioni di associazione e di rilasciarle in modo indipendente. È ad esempio possibile scegliere di eseguire l'aggiornamento a una versione di un'estensione basata su una nuova versione di un SDK sottostante.
* Ambiente di esecuzione più leggero, in cui solo le associazioni in uso sono note e vengono caricate dal runtime.

Tutte le associazioni di Funzioni di Azure predefinite hanno adottato questo modello e non sono più incluse per impostazione predefinita, tranne il trigger Timer e il trigger HTTP. Tali estensioni vengono installate automaticamente quando si creano funzioni con strumenti come Visual Studio o tramite il portale.

Nella tabella seguente sono indicate le associazioni supportate in ogni versione del runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Problemi noti nella versione 2.x

Per altre informazioni sul supporto delle associazioni e su altre discrepanze a livello funzionale nella versione 2.x, vedere i [problemi noti della versione 2.0 del runtime](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Scrivere codici per Funzioni di Azure e testarle in locale](functions-run-local.md)
* [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md)
* [Note sulla versione](https://github.com/Azure/azure-functions-host/releases)
