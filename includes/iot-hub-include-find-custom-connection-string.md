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
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050461"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Per creare un criterio di accesso condiviso che conceda le autorizzazioni di connessione al **servizio** e **del registro di sistema** e ottenere una stringa di connessione per questo criterio, attenersi alla procedura seguente:

1. Nella [portale di Azure](https://portal.azure.com)selezionare gruppi di **risorse**. Selezionare il gruppo di risorse in cui si trova l'hub e quindi selezionare l'hub dall'elenco di risorse.

1. Nel riquadro a sinistra dell'hub selezionare **criteri di accesso condiviso**.

1. Dal menu superiore sopra l'elenco di criteri selezionare **Aggiungi**.

1. In **Aggiungi un criterio di accesso condiviso**immettere un nome descrittivo per il criterio, ad esempio *serviceAndRegistryRead*. In **autorizzazioni**selezionare **Registro di sistema lettura** e **servizio Connetti**e quindi selezionare **Crea**.

    ![Mostra come aggiungere nuovi criteri di accesso condiviso](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selezionare il nuovo criterio dall'elenco dei criteri.

1. In **chiavi di accesso condivise**selezionare l'icona di copia per la **stringa di connessione--chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub Internet, vedere [controllo di accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
