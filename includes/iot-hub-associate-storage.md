---
title: File di inclusione
description: File di inclusione
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/20/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 8aa4695ea1175fe9d558e02bae661c9610123299
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43086388"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associare un account di archiviazione di Azure all'hub IoT

Poiché l'app per dispositivo simulato carica un file in un BLOB, è necessario avere un account di [Archiviazione di Azure](../articles/storage/common/storage-quickstart-create-account.md) associato all'hub IoT. Quando si associa un account di Archiviazione di Azure a un hub IoT, l'hub IoT genera un URI di firma di accesso condiviso. Un dispositivo può usare questo URI di firma di accesso condiviso per eseguire l'upload di un file in modo sicuro in un contenitore BLOB. Il servizio hub IoT e gli SDK di dispositivo coordinano il processo che genera l'URI di firma di accesso condiviso e lo rendono disponibile per un dispositivo che lo userà per caricare un file.

Seguire le istruzioni in [Configurare i caricamenti dei file con il portale di Azure](../articles/iot-hub/iot-hub-configure-file-upload.md) per associare un account di archiviazione di Azure all'hub IoT. Assicurarsi che un contenitore BLOB venga associato all'hub IoT e che siano abilitate le notifiche file.

![Abilitazione di notifiche file nel portale](./media/iot-hub-associate-storage/enable-file-notifications.png)