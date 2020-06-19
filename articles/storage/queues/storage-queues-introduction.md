---
title: Introduzione a Code di Azure - Archiviazione di Azure
description: Introduzione a Code di Azure
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: 678ab8a3f3dace17671c242939c20cb5499b2e39
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84805122"
---
# <a name="what-are-azure-queues"></a>Che cosa sono le code di Azure?

Archiviazione code di Azure è un servizio per l'archiviazione di un numero elevato di messaggi. È possibile accedere ai messaggi ovunque ci si trovi con chiamate autenticate tramite HTTP o HTTPS. Un messaggio in coda avere dimensioni fino a 64 KB. Una coda può contenere milioni di messaggi, fino al limite di capacità totale dell'account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono.

## <a name="queue-service-concepts"></a>Concetti del servizio di accodamento

Il servizio di accodamento contiene i componenti seguenti:

![Diagramma che mostra la relazione tra un account di archiviazione, le code e i messaggi](./media/storage-queues-introduction/queue1.png)

* **Formato URL:** è possibile fare riferimento alle code usando il formato URL seguente:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    L'URL seguente fa riferimento a una delle code nel diagramma:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità degli account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Coda:** una coda contiene un set di messaggi. Il nome della coda **deve** essere tutto in minuscolo. Per altre informazioni, vedere [Denominazione di code e metadati](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Messaggio:** un messaggio, in qualsiasi formato, con dimensioni massime di 64 kB. Prima della versione 2017-07-29, la durata (TTL) massima consentita è di sette giorni. Per la versione 2017-07-29 o successive, la durata massima consentita può essere un numero positivo qualsiasi o -1, a indicare che il messaggio non scade. Se questo parametro viene omesso, la durata predefinita è di sette giorni.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account di archiviazione](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Introduzione alle code con .NET](storage-dotnet-how-to-use-queues.md)
