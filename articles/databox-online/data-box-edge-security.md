---
title: Sicurezza di Azure al bordo casella Data | Microsoft Docs
description: Descrive le funzionalità di sicurezza e privacy che proteggono il dispositivo Edge casella dei dati di Azure, servizio e i dati in locale e nel cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682101"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data margine sicurezza e protezione dei dati

Sicurezza è delle principali preoccupazioni ad adottare una nuova tecnologia, soprattutto se la tecnologia viene usata con dati riservati o proprietari. Soluzione bordo casella dei dati di Microsoft Azure consente di garantire che solo le entità autorizzate possono visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza applicato al bordo casella dei dati che consentono di proteggere ognuno dei componenti della soluzione e i dati archiviati su di essi.

La soluzione Edge casella dei dati di Azure è costituita da quattro componenti principali che interagiscono tra loro:

- **Servizio casella Edge dati ospitato in Azure** : risorsa di gestione che consente la creazione dell'ordine di dispositivo, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Il dispositivo di bordo casella dati** : il dispositivo di trasferimento viene fornita all'utente per importare i dati in locale in Azure.
- **Client/host connessi al dispositivo** – client nell'infrastruttura che si connettono al dispositivo Edge casella dei dati e contiene dati che devono essere protetti.
- **Archiviazione cloud** – Posizione nel cloud di Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa bordo casella dei dati che è stato creato.

## <a name="data-box-edge-service-protection"></a>Protezione servizio bordo casella dei dati

Il servizio dati casella Edge è un servizio di gestione ospitato in Microsoft Azure. Il servizio viene usato per configurare e gestire il dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Protezione dei dati per il dispositivo bordo casella

Il dispositivo perimetrale casella dei dati è un dispositivo locale che aiuta a trasformare i dati da elaborarlo in locale e quindi inviarli ad Azure. Il dispositivo:

- Richiede una chiave di attivazione per accedere al servizio dati al bordo casella.
- È protetta in tutte le volte in cui da una password del dispositivo.
- È un dispositivo bloccato. Il dispositivo BMC e BIOS è protetti da password con accesso utente limitato per il BIOS.
- Avvio protetto è abilitato.
- Viene eseguito Windows Defender Device Guard. Device Guard consente di eseguire solo applicazioni attendibili definite nei criteri di integrità del codice.

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite una chiave di attivazione

Solo un dispositivo Edge finestra dati autorizzato è consentito per l'iscrizione al servizio dati casella Edge creato nella sottoscrizione di Azure. Per autorizzare un dispositivo, è necessario utilizzare una chiave di attivazione per attivare il dispositivo con il servizio al bordo casella dei dati.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Per altre informazioni, visitare [ottenere una chiave di attivazione](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggere il dispositivo tramite password

Le password assicurarsi che i dati siano accessibili solo agli utenti autorizzati. I dati al bordo casella dispositivi avvio in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente del dispositivo tramite un browser Web locale e quindi specificare una password per accedere al dispositivo.
- Connettersi in remoto all'interfaccia di PowerShell del dispositivo tramite HTTP. Gestione remota è attivata per impostazione predefinita. È quindi possibile fornire la password del dispositivo per accedere al dispositivo. Per altre informazioni, visitare [Connect in modalità remota al dispositivo Edge finestra dati](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilizzo interfaccia utente al web locale [modificare la password](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se si modifica la password, assicurarsi di notificarlo a tutti gli utenti di accesso remoto in modo che non riscontrino un errore di accesso.

## <a name="protect-the-data"></a>La protezione dei dati

In questa sezione descrive le funzionalità di sicurezza applicato al bordo casella dei dati che proteggono i dati in transito e i dati archiviati.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteggere i dati in movimento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Ruotare e quindi [sincronizzare le chiavi dell'account di archiviazione](data-box-edge-manage-shares.md#sync-storage-keys) regolarmente per garantire che l'account di archiviazione non è accessibile a utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestire le informazioni personali

Il servizio dati al bordo casella raccoglie informazioni personali nelle istanze principali seguenti:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco di utenti che possono accedere o eliminare una condivisione, seguire i passaggi descritti in [gestire le condivisioni sul bordo di finestra dati](data-box-edge-manage-shares.md).

Per altre informazioni, consultare l'Informativa sulla privacy Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo periferico finestra dati](data-box-edge-deploy-prep.md).
