---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e3110e4018e214e7e7aa591b811246369c029ecd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34667291"
---
Per creare un hub IoT usando il portale di Azure:

1. Accedere al [portale di Azure](http://portal.azure.com).

1. Selezionare **Crea una risorsa** > **Internet delle cose** > **Hub IoT**.

    ![Selezionare l'installazione dell'hub IoT](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Per creare l'hub IoT di livello gratuito, usare i valori nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Sottoscrizione | Selezionare la sottoscrizione di Azure nell'elenco a discesa. |
    | Gruppo di risorse | Creare un nuovo gruppo di risorse Questa esercitazione usa il nome **tutorials-iot-hub-rg**. |
    | Region | Questa esercitazione usa **Stati Uniti occidentali**. È possibile scegliere l'area più vicina. |
    | NOME | Nello screenshot seguente viene usato il nome **tutorials-iot-hub**. È necessario scegliere un nome univoco quando si crea l'hub. |

    ![Impostazioni dell'hub 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Impostazione | Valore |
    | ------- | ----- |
    | Piano tariffario e livello di scalabilità | F1 Gratuito. È consentito un solo hub di livello gratuito in una sottoscrizione. |
    | Unità di hub IoT | 1 |

    ![Impostazioni dell'hub 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Fare clic su **Crea**. La creazione dell'hub può richiedere vari minuti.

    ![Impostazioni dell'hub 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Prendere nota del nome dell'hub IoT scelto. Questo valore verrà usato più avanti nell'esercitazione.