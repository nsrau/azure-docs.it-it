---
title: Gestire protocolli e modalità di crittografia in Gestione API di Azure | Microsoft Docs
description: Informazioni su come gestire i protocolli (TLS) e le crittografie (DES) in gestione API di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 7f87389016286c9f1b91abc77155be9e94005371
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335880"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gestire i protocolli e la crittografia in Gestione API di Azure

Gestione API di Azure supporta più versioni del protocollo TLS per i lati client e back-end, nonché la crittografia 3DES.

Questa guida illustra come gestire la configurazione dei protocolli e della crittografia per un'istanza di Gestione API di Azure.

![Gestire i protocolli e la crittografia in APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi in questo articolo è necessario avere quanto segue:

* Un'istanza di Gestione API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Modalità di gestione dei protocolli TLS e della crittografia 3DES

1. Accedere all'**istanza di Gestione API** nel portale di Azure.
2. Selezionare **Impostazioni protocollo** dal menu.  
3. Abilitare o disabilitare i protocolli o le crittografie desiderate.
4. Fare clic su **Save**. Le modifiche verranno applicate entro un'ora.  

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.