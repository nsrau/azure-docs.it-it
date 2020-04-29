---
title: includere il file
description: File di inclusione
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76020900"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associare un account di archiviazione di Azure all'hub IoT

Poiché l'app per dispositivo simulato carica un file in un BLOB, è necessario avere un account di [Archiviazione di Azure](../articles/storage/common/storage-account-create.md) associato al proprio hub IoT. Quando si associa un account di Archiviazione di Azure a un hub IoT, l'hub IoT genera un URI di firma di accesso condiviso. Un dispositivo può usare questo URI di firma di accesso condiviso per eseguire l'upload di un file in modo sicuro in un contenitore BLOB. Il servizio hub IoT e gli SDK di dispositivo coordinano il processo che genera l'URI di firma di accesso condiviso e lo rendono disponibile per un dispositivo che lo userà per caricare un file.

Seguire le istruzioni in [Configure file uploads using the Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md) (Configurare i caricamenti dei file tramite il portale di Azure). Assicurarsi che un contenitore BLOB venga associato all'hub IoT e che siano abilitate le notifiche file.

![Abilitazione di notifiche file nel portale](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
