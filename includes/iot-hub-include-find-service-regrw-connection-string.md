---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756985"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Per creare un criterio di accesso condiviso che concede al **servizio di connessione** e autorizzazioni di scrittura del **Registro** di sistema e per ottenere una stringa di connessione per questo criterio, attenersi alla seguente procedura:

1. Nel [portale](https://portal.azure.com)di Azure selezionare **Gruppi di risorse**. Selezionare il gruppo di risorse in cui si trova l'hub e quindi selezionare l'hub dall'elenco delle risorse.

1. Nel riquadro sinistro dell'hub selezionare **Criteri di accesso condiviso.**

1. Dal menu in alto sopra l'elenco dei criteri, selezionare **Aggiungi**.

1. In **Aggiungi criteri di accesso condiviso**immettere un nome descrittivo per il criterio, ad esempio *serviceAndRegistryReadWrite*. In **Autorizzazioni**selezionare **Scrittura del Registro di sistema** e Connessione **servizio**, quindi selezionare **Crea**. (L'autorizzazione di **lettura del Registro** di sistema viene inclusa automaticamente quando si seleziona **scrittura del Registro di sistema**.)

    ![Mostra come aggiungere un nuovo criterio di accesso condiviso](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Selezionare il nuovo criterio dall'elenco dei criteri.

1. In Chiavi di **accesso condivise**selezionare l'icona di copia per la stringa di connessione, ovvero la chiave **primaria,** quindi salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Per ulteriori informazioni sulle autorizzazioni e sui criteri di accesso condiviso dell'hub IoT, vedere [Controllo di accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
