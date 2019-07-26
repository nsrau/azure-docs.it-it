---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404021"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Per creare un criterio di accesso condiviso che conceda le autorizzazioni di connessione al **servizio** e **del registro di sistema** e ottenere una stringa di connessione per questo criterio, attenersi alla procedura seguente:

1. Aprire l'hub Internet delle cose nel [portale di Azure](https://portal.azure.com). Il modo più semplice per ottenere l'hub Internet è quello di selezionare i **gruppi di risorse**, selezionare il gruppo di risorse in cui si trova l'hub Internet e quindi selezionare l'hub delle cose dall'elenco di risorse.

2. Nel riquadro sinistro dell'hub Internet, selezionare **criteri di accesso condiviso**.

3. Dal menu superiore sopra l'elenco di criteri selezionare **Aggiungi**.

4. Nel riquadro **Aggiungi criteri di accesso condiviso** immettere un nome descrittivo per il criterio. ad esempio: *serviceAndRegistryRead*. In **autorizzazioni**selezionare **Registro di sistema lettura** e **servizio Connetti**e quindi selezionare **Crea**.

    ![Mostra come aggiungere nuovi criteri di accesso condiviso](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. Tornare al riquadro **criteri di accesso condivisi** e selezionare i nuovi criteri dall'elenco dei criteri.

6. In **chiavi di accesso condivise**selezionare l'icona di copia per la **stringa di connessione--chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub Internet, vedere [controllo di accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
