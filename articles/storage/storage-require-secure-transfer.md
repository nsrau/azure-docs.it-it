---
title: Richiedere il trasferimento sicuro in Archiviazione di Azure | Microsoft Docs
description: "Informazioni sulla funzionalità \"Trasferimento sicuro obbligatorio\" per Archiviazione di Azure e su come abilitarla."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 516618653064fd4e334197bba767a013a805260a
ms.contentlocale: it-it
ms.lasthandoff: 06/21/2017

---
# <a name="require-secure-transfer"></a>Richiedere il trasferimento sicuro

L'opzione Trasferimento sicuro obbligatorio aumenta la sicurezza dell'account di archiviazione perché consente le richieste all'account di archiviazione solo tramite connessioni sicure il protocollo HTTPS. Ad esempio, quando si chiamano API REST per accedere all'account di archiviazione, è necessario connettersi usando HTTPS. Quando questa impostazione è abilitata, le eventuali richieste tramite HTTP vengono rifiutate.

Quando si usa il servizio File di Azure, se è abilitata l'opzione "Trasferimento sicuro obbligatorio" le connessioni senza crittografia hanno esito negativo. Questo include scenari in cui si usano SMB 2.1, SMB 3.0 senza crittografia e alcuni tipi del client SMB Linux. 

Per impostazione predefinita, l'opzione "Trasferimento sicuro obbligatorio" è disattivata.

> [!NOTE]
> Poiché Archiviazione di Azure non supporta HTTPS per i nomi di dominio personalizzati, l'opzione non è applicabile quando si usa un nome di dominio personalizzato.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Abilitare "Trasferimento sicuro obbligatorio" nel portale di Azure

È possibile abilitare l'impostazione "Trasferimento sicuro obbligatorio" sia quando si crea un account di archiviazione nel [portale di Azure](https://portal.azure.com), sia per gli account di archiviazione esistenti.

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>Richiedere il trasferimento sicuro quando si crea un account di archiviazione

1. Aprire il pannello **Crea account di archiviazione** nel portale di Azure.
1. In **Trasferimento sicuro obbligatorio** selezionare **Abilitato**.

  ![screenshot](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Richiedere il trasferimento sicuro per un account di archiviazione esistente

1. Selezionare un account di archiviazione esistente nel portale di Azure.
1. Selezionare **Configurazione** in **IMPOSTAZIONI** nel pannello del menu dell'account di archiviazione.
1. In **Trasferimento sicuro obbligatorio** selezionare **Abilitato**.

  ![screenshot](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="next-steps"></a>Passaggi successivi
Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. Per altre informazioni, vedere la [Guida alla sicurezza delle risorse di archiviazione](storage-security-guide.md).

