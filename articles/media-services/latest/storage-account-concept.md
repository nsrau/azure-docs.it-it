---
title: Account di archiviazione in Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra la modalità in cui Servizi multimediali usa gli account di archiviazione.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 90e01f39fa6b31095d76d0dfae2f700b4fa2ca3f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182868"
---
# <a name="storage-accounts"></a>Account di archiviazione

Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. 

È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). 

>[!NOTE]
> Solo gli account di BLOB non sono consentiti come **primari**. 

È consigliabile usare account GPv2, per poter scegliere tra livelli di archiviazione ad accesso frequente e livelli di archiviazione ad accesso sporadico. Per altre informazioni sugli account di archiviazione, vedere la [panoramica degli account di Archiviazione di Azure](../../storage/common/storage-account-overview.md). 

L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi

## <a name="assets-in-a-storage-account"></a>Asset in un account di archiviazione

In Servizi multimediali v3, le API di archiviazione vengono usate per caricare i file. Per informazioni su come usare le API di archiviazione con Servizi multimediali per caricare i file di input, consultare [Create a job input from a local file](job-input-from-local-file-how-to.md) (Creare un input del processo da un file locale). 

> [!Note]
> È consigliabile non tentare di modificare il contenuto dei contenitori BLOB generati dall'SDK di Servizi multimediali senza usare le API di Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come collegare un account di archiviazione all'account di Servizi multimediali, vedere [Creare un account](create-account-cli-quickstart.md).
