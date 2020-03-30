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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050461"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Per creare un criterio di accesso condiviso che concede al **servizio connettersi** e le autorizzazioni di **lettura del Registro di sistema** e ottenere una stringa di connessione per questo criterio, attenersi alla seguente procedura:

1. Nel [portale](https://portal.azure.com)di Azure selezionare **Gruppi di risorse**. Selezionare il gruppo di risorse in cui si trova l'hub e quindi selezionare l'hub dall'elenco delle risorse.

1. Nel riquadro sinistro dell'hub selezionare **Criteri di accesso condiviso.**

1. Dal menu in alto sopra l'elenco dei criteri, selezionare **Aggiungi**.

1. In **Aggiungi criteri di accesso condiviso**immettere un nome descrittivo per il criterio, ad esempio *serviceAndRegistryRead*. In **Autorizzazioni**selezionare **Lettura del Registro** di sistema e **Connessione servizio**, quindi selezionare **Crea**.

    ![Mostra come aggiungere un nuovo criterio di accesso condiviso](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selezionare il nuovo criterio dall'elenco dei criteri.

1. In Chiavi di **accesso condivise**selezionare l'icona di copia per la stringa di connessione, ovvero la chiave **primaria,** quindi salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Per ulteriori informazioni sulle autorizzazioni e sui criteri di accesso condiviso dell'hub IoT, vedere [Controllo di accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
