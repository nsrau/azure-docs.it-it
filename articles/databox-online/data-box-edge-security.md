---
title: Sicurezza di Azure al bordo casella Data | Microsoft Docs
description: Vengono descritte le funzionalità di sicurezza e privacy che proteggono il bordo casella dei dati di Azure dispositivo e del servizio di dati in locale e nel cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 8823aebe17a5446b3c507878833c2525c338dde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756232"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data margine sicurezza e protezione dei dati

Sicurezza è delle principali preoccupazioni quando si sta adottando una nuova tecnologia, soprattutto se la tecnologia viene usata con dati riservati o proprietari. Azure consente di dati al bordo casella assicurare che autorizzati solo le entità possono visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza applicato al bordo casella dei dati che consentono di proteggere ognuno dei componenti della soluzione e i dati archiviati al loro interno.

Azure Edge di finestra di dati è costituito da quattro componenti principali che interagiscono tra loro:

- **Servizio bordo casella dei dati, ospitato in Azure**. La risorsa di gestione che consente la creazione dell'ordine di dispositivo, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Il dispositivo di bordo casella dati**. Il dispositivo di trasferimento viene fornita all'utente in modo che è possibile importare i dati in locale in Azure.
- **Client/host connessi al dispositivo**. I client nell'infrastruttura che si connettono al dispositivo Edge casella dei dati e contengano dati che devono essere protetti.
- **Archiviazione cloud**. Il percorso alla piattaforma cloud di Azure dove sono archiviati dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa bordo casella dei dati creati.

## <a name="data-box-edge-service-protection"></a>Protezione servizio bordo casella dei dati

Il servizio dati casella Edge è un servizio di gestione ospitato in Azure. Il servizio viene usato per configurare e gestire il dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Protezione dei dati per il dispositivo bordo casella

Il dispositivo perimetrale casella dei dati è un dispositivo locale che aiuta a trasformare i dati da elaborarlo in locale e quindi inviarli ad Azure. Il dispositivo:

- Richiede una chiave di attivazione per accedere al servizio dati al bordo casella.
- È protetta in tutte le volte in cui da una password del dispositivo.
- È un dispositivo bloccato. Il dispositivo BMC e BIOS è protetti da password. Il BIOS è protetta da limitato l'accesso degli utenti.
- Avvio protetto è abilitato.
- Viene eseguito Windows Defender Device Guard. Device Guard consente di eseguire solo applicazioni attendibili definite nei criteri di integrità del codice.

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite una chiave di attivazione

Solo un dispositivo Edge finestra dati autorizzato è consentito per l'iscrizione al servizio dati al bordo casella creati nella sottoscrizione di Azure. Per autorizzare un dispositivo, è necessario usare una chiave di attivazione per attivare il dispositivo con il servizio al bordo casella dei dati.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Per altre informazioni, vedere [ottenere una chiave di attivazione](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggere il dispositivo tramite password

Le password assicurarsi che solo gli utenti autorizzati possono accedere ai dati. I dati al bordo casella dispositivi avvio in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente del dispositivo tramite un browser Web locale e quindi specificare una password per accedere al dispositivo.
- Connettersi in remoto all'interfaccia di PowerShell del dispositivo tramite HTTP. Gestione remota è attivata per impostazione predefinita. È quindi possibile fornire la password del dispositivo per accedere al dispositivo. Per altre informazioni, vedere [Connect in modalità remota al dispositivo Edge finestra dati](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilizzo interfaccia utente al web locale [modificare la password](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se si modifica la password, assicurarsi di notificarlo a tutti gli utenti di accesso remoto in modo che non hanno problemi di accesso.

## <a name="protect-your-data"></a>Proteggi i tuoi dati

In questa sezione descrive le funzionalità di sicurezza applicato al bordo casella dei dati che proteggono i dati archiviati e in transito.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteggere i dati in movimento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Ruotare e quindi [sincronizzare le chiavi dell'account di archiviazione](data-box-edge-manage-shares.md#sync-storage-keys) regolarmente per proteggere l'account di archiviazione da utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestire le informazioni personali

Il servizio dati al bordo casella raccoglie informazioni personali negli scenari seguenti:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco di utenti che possono accedere o eliminare una condivisione, seguire i passaggi descritti in [gestire le condivisioni sul bordo di finestra dati](data-box-edge-manage-shares.md).

Per altre informazioni, esaminare l'informativa sulla privacy di Microsoft nella [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo periferico casella dei dati](data-box-edge-deploy-prep.md)
