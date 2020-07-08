---
title: includere il file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80756985"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Per creare un criterio di accesso condiviso che conceda le autorizzazioni di connessione al **servizio** e di **scrittura del registro di sistema** e per ottenere una stringa di connessione per questo criterio, attenersi alla procedura seguente:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Gruppi di risorse**. Selezionare il gruppo di risorse in cui si trova l'hub e quindi selezionare l'hub dall'elenco di risorse.

1. Nel riquadro sinistro dell'hub selezionare **Criteri di accesso condiviso**.

1. Dal menu superiore sopra l'elenco di criteri selezionare **Aggiungi**.

1. In **Aggiungi un criterio di accesso condiviso**immettere un nome descrittivo per il criterio, ad esempio *serviceAndRegistryReadWrite*. In **autorizzazioni**selezionare **Registro di sistema scrittura** e **servizio connessione**, quindi selezionare **Crea**. L'autorizzazione di **lettura del registro di sistema** viene inclusa automaticamente quando si seleziona scrittura nel registro di **sistema**.

    ![Mostrare come aggiungere nuovi criteri di accesso condiviso](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Selezionare il nuovo criterio dall'elenco dei criteri.

1. In **Chiavi di accesso condivise** selezionare l'icona di copia per **Stringa di connessione -- Chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub IoT, vedere [Controllo dell'accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
