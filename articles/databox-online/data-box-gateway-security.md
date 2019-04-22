---
title: Sicurezza di Azure Gateway dati casella | Microsoft Docs
description: Descrive le funzionalità di sicurezza e privacy che proteggono il dispositivo virtuale di Azure Data finestra Gateway e del servizio dati in locale e nel cloud.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: b27b712128ddfbb07a7a7f68f616c20ec3fb53d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685901"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Gateway dati casella sicurezza e protezione dei dati

Sicurezza è delle principali preoccupazioni ad adottare una nuova tecnologia, soprattutto se la tecnologia viene usata con dati riservati o proprietari. Soluzione Gateway finestra dati di Microsoft Azure consente di garantire che solo le entità autorizzate possono visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza Gateway casella dei dati di Azure che consentono di proteggere ognuno dei componenti della soluzione e i dati archiviati su di essi.

La soluzione del Gateway di dati finestra è costituito da quattro componenti principali che interagiscono tra loro:

- **Servizio casella Gateway dati ospitato in Azure** : risorsa di gestione che consente la creazione dell'ordine di dispositivo, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Dispositivo Gateway finestra dati** : il dispositivo virtuale che è stato effettuato il provisioning nell'hypervisor del sistema specificato. Questo dispositivo virtuale viene usato per importare i dati in locale in Azure.
- **Client/host connessi al dispositivo** – client nell'infrastruttura che si connettono al dispositivo Gateway casella dei dati e contiene dati che devono essere protetti.
- **Archiviazione cloud** – Posizione nel cloud di Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa Gateway di dati finestra creata.


## <a name="data-box-gateway-service-protection"></a>Protezione di servizio finestra Gateway dati

Il servizio Gateway di dati finestra è un servizio di gestione ospitato in Microsoft Azure. Il servizio viene usato per configurare e gestire il dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Protezione dei dati per il dispositivo Gateway finestra

Il dispositivo Gateway di dati finestra è un dispositivo virtuale con provisioning in hypervisor di un sistema locale specificata dall'utente. Il dispositivo consente di inviare dati ad Azure. Il dispositivo:

- Richiede una chiave di attivazione per accedere al servizio Gateway di dati finestra Edge/Data finestra.
- È protetta in tutte le volte in cui da una password del dispositivo.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite una chiave di attivazione

Solo un dispositivo Gateway di dati finestra autorizzato è consentito per l'iscrizione al servizio dati casella Gateway creato nella sottoscrizione di Azure. Per autorizzare un dispositivo, è necessario utilizzare una chiave di attivazione per attivare il dispositivo con il servizio Gateway di dati finestra.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Per altre informazioni, visitare [ottenere una chiave di attivazione](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggere il dispositivo tramite password

Le password assicurarsi che i dati siano accessibili solo agli utenti autorizzati. Data Gateway casella dispositivi avvio in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente del dispositivo tramite un browser Web locale e quindi specificare una password per accedere al dispositivo.
- Connettersi in remoto all'interfaccia di PowerShell del dispositivo tramite HTTP. Gestione remota è attivata per impostazione predefinita. È quindi possibile fornire la password del dispositivo per accedere al dispositivo. Per altre informazioni, visitare [Connect in modalità remota al dispositivo Gateway di dati finestra](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilizzo interfaccia utente al web locale [modificare la password](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se si modifica la password, assicurarsi di notificarlo a tutti gli utenti di accesso remoto in modo che non riscontrino un errore di accesso.


## <a name="protect-the-data"></a>La protezione dei dati

In questa sezione descrive le funzionalità di sicurezza di dati finestra Gateway che proteggono i dati in transito e i dati archiviati.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteggere i dati in movimento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Ruotare e quindi [sincronizzare le chiavi dell'account di archiviazione](data-box-gateway-manage-shares.md#sync-storage-keys) regolarmente per garantire che l'account di archiviazione non è accessibile a utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestire le informazioni personali

Il servizio Gateway di dati finestra raccoglie informazioni personali nelle istanze principali seguenti:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco di utenti che possono accedere o eliminare una condivisione, seguire i passaggi descritti in [gestire le condivisioni nel Gateway dati casella](data-box-gateway-manage-shares.md).

Per altre informazioni, consultare l'Informativa sulla privacy Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un dispositivo Gateway di dati finestra](data-box-gateway-deploy-prep.md).
