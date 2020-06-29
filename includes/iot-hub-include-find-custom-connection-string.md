---
title: includere file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70050461"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Per creare criteri di accesso condiviso che concedono le autorizzazioni di **connessione al servizio** e **lettura del registro di sistema** e ottenere una stringa di connessione per questo criterio, attenersi alla seguente procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Gruppi di risorse**. Selezionare il gruppo di risorse in cui si trova l'hub e quindi selezionare l'hub dall'elenco di risorse.

1. Nel riquadro sinistro dell'hub selezionare **Criteri di accesso condiviso**.

1. Dal menu superiore sopra l'elenco di criteri selezionare **Aggiungi**.

1. In **Aggiungi un criterio di accesso condiviso** immettere un nome descrittivo per il criterio, ad esempio *serviceAndRegistryRead*. In **Autorizzazioni** selezionare **Lettura del registro di sistema** e **Connessione al servizio**, quindi selezionare **crea**.

    ![Mostrare come aggiungere nuovi criteri di accesso condiviso](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selezionare il nuovo criterio dall'elenco dei criteri.

1. In **Chiavi di accesso condivise** selezionare l'icona di copia per **Stringa di connessione -- Chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub IoT, vedere [Controllo dell'accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
