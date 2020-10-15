---
title: I prerequisiti includono file condivisi da due schede nello stesso file | Microsoft Docs
description: Prerequisiti dell'ordine per la distribuzione di Azure Data Box
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/15/2020
ms.author: v-grpr
ms.openlocfilehash: 6aaf57d9bcdfb1f350e1d54937e9c705dd32116e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85392480"
---
### <a name="for-service"></a>Per il servizio

[!INCLUDE [Data Box service prerequisites](data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Per il dispositivo

Prima di iniziare, verificare che:

* Sia disponibile un computer host connesso alla rete del data center. Il servizio Data Box copierà i dati da questo computer. Il computer host deve eseguire un sistema operativo supportato come descritto nei [requisiti di sistema per Azure Data Box](../articles/databox/data-box-system-requirements.md).
* Il data center disponga di una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE, ma la velocità dell'operazione di copia ne risentirà.
