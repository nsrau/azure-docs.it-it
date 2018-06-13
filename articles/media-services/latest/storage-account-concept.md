---
title: Account di archiviazione in Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra la modalità in cui Servizi multimediali usa gli account di archiviazione.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6d4c21867b0b46508f348300ae2b9553a75d23b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782570"
---
# <a name="storage-accounts"></a>Account di archiviazione

Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. 

È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). 

>[!NOTE]
> Solo gli account di BLOB non sono consentiti come **primari**. 

È consigliabile usare account GPv2, per poter scegliere tra livelli di archiviazione ad accesso frequente e livelli di archiviazione ad accesso sporadico. Per altre informazioni sugli account di archiviazione, vedere [Opzioni di account di archiviazione di Azure](../../storage/common/storage-account-options.md). 

## <a name="assets-in-a-storage-account"></a>Asset in un account di archiviazione

In Servizi multimediali v3, le API di archiviazione vengono usate per caricare i file. Per informazioni su come usare le API di archiviazione con Servizi multimediali per caricare i file di input, consultare [Create a job input from a local file](job-input-from-local-file-how-to.md) (Creare un input del processo da un file locale). 

> [!Note]
> È consigliabile non tentare di modificare il contenuto dei contenitori BLOB generati dall'SDK di Servizi multimediali senza usare le API di Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come collegare un account di archiviazione all'account di Servizi multimediali, vedere [Creare un account](create-account-cli-quickstart.md).
