---
title: Caricamento e archiviazione nel cloud con Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra i concetti relativi al caricamento e all'archiviazione nel cloud.
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
ms.openlocfilehash: d49d056ab84b60389df8bcaf1c75d6224633863d
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337859"
---
# <a name="cloud-upload-and-storage"></a>Caricamento e archiviazione nel cloud

Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. 

L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi

È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). 

>[!NOTE]
> Solo gli account di BLOB non sono consentiti come **primari**. 

È consigliabile usare account GPv2, per poter scegliere tra livelli di archiviazione ad accesso frequente e livelli di archiviazione ad accesso sporadico. Per altre informazioni sugli account di archiviazione, vedere la [panoramica degli account di Archiviazione di Azure](../../storage/common/storage-account-overview.md). 

## <a name="assets-in-a-storage-account"></a>Asset in un account di archiviazione

In Servizi multimediali v3, le API di archiviazione vengono usate per caricare i file.

> [!Note]
> È consigliabile non tentare di modificare il contenuto dei contenitori BLOB generati dall'SDK di Servizi multimediali senza usare le API di Servizi multimediali.

Per informazioni su come usare le API di archiviazione con Servizi multimediali per caricare i file di input, consultare [Create a job input from a local file](job-input-from-local-file-how-to.md) (Creare un input del processo da un file locale). 
 
## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come collegare un account di archiviazione all'account di Servizi multimediali, vedere [Creare un account](create-account-cli-quickstart.md).
