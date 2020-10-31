---
title: Aggiungere un certificato della CA personalizzato - Gestione API di Azure | Microsoft Docs
description: Informazioni su come aggiungere un certificato della CA personalizzato in Gestione API di Azure. È anche possibile visualizzare le istruzioni per eliminare un certificato.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 124bc053aa2c6e59e205bb6f33a9a96190799499
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102038"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Come aggiungere un certificato della CA personalizzato in Gestione API di Azure

Gestione API di Azure consente di installare i certificati della CA nel computer all'interno di archivi di certificati intermedi e radice attendibile. Questa funzionalità deve essere usata se i servizi richiedono un certificato della CA personalizzato.

Questo articolo illustra come gestire i certificati della CA di un'istanza del servizio Gestione API di Azure nel portale di Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Caricare un certificato della CA

![Aggiungere certificati della CA](media/api-management-howto-ca-certificates/00.png)

Seguire questa procedura per caricare un nuovo certificato della CA. Se non è ancora stata creata un'istanza del servizio Gestione API, vedere l'esercitazione [Creare un'istanza di Gestione API](get-started-create-service-instance.md).

1. Passare all'istanza del servizio Gestione API di Azure nel portale di Azure.

2. Scegliere **Certificati CA** dal menu.

3. Fare clic sul pulsante **+ Aggiungi** .  

    ![Screenshot che mostra il pulsante + Aggiungi per l'aggiunta di un certificato della CA.](media/api-management-howto-ca-certificates/01.png)  

4. Cercare il certificato e scegliere l'archivio certificati. È necessario specificare solo la chiave pubblica e pertanto la password non è obbligatoria.

    ![Screenshot che illustra come cercare il certificato.](media/api-management-howto-ca-certificates/02.png)  

5. Fare clic su **Salva** . Questa operazione può richiedere qualche minuto.

    ![Screenshot che illustra come salvare il certificato.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> È possibile caricare un certificato della CA usando il comando `New-AzApiManagementSystemCertificate` di PowerShell.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Eliminare un certificato client

Per eliminare un certificato, fare clic sul menu di scelta rapida **...** e selezionare **Elimina** accanto al certificato.

![Eliminare certificati della CA](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
