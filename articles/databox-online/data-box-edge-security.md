---
title: Sicurezza Azure Data Box Edge | Microsoft Docs
description: Descrive le funzionalità di sicurezza e privacy che proteggono il dispositivo, il servizio e i dati Azure Data Box Edge in locale e nel cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970892"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Sicurezza e protezione dei dati Azure Data Box Edge

La sicurezza è un problema fondamentale quando si adotta una nuova tecnologia, soprattutto se la tecnologia viene usata con dati riservati o proprietari. Azure Data Box Edge consente di verificare che solo le entità autorizzate possano visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza di Data Box Edge che consentono di proteggere i componenti della soluzione e i dati archiviati.

Azure Data Box Edge è costituito da quattro componenti principali che interagiscono tra loro:

- **Servizio Data Box Edge, ospitato in Azure**. La risorsa di gestione usata per creare l'ordine dei dispositivi, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Data Box Edge dispositivo**. Il dispositivo di trasferimento fornito all'utente per poter importare i dati locali in Azure.
- **Client/host connessi al dispositivo**. I client nell'infrastruttura che si connettono al dispositivo Data Box Edge e contengono dati che devono essere protetti.
- **Archiviazione cloud**. Posizione della piattaforma cloud di Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa Data Box Edge creata.

## <a name="data-box-edge-service-protection"></a>Protezione del servizio Data Box Edge

Il servizio Data Box Edge è un servizio di gestione ospitato in Azure. Il servizio viene usato per configurare e gestire il dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Protezione del dispositivo Data Box Edge

Il dispositivo Data Box Edge è un dispositivo locale che consente di trasformare i dati mediante l'elaborazione locale e quindi inviarli ad Azure. Il dispositivo:

- Richiede una chiave di attivazione per accedere al servizio Data Box Edge.
- È sempre protetto da una password del dispositivo.
- È un dispositivo bloccato. Il BMC e il BIOS del dispositivo sono protetti da password. Il BIOS è protetto da accesso utente limitato.
- Con avvio protetto abilitato.
- Esegue Windows Defender Device Guard. Device Guard consente di eseguire solo applicazioni attendibili definite nei criteri di integrità del codice.

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite la chiave di attivazione

Solo un dispositivo Data Box Edge autorizzato è autorizzato a partecipare al servizio Data Box Edge creato nella sottoscrizione di Azure. Per autorizzare un dispositivo, è necessario usare una chiave di attivazione per attivare il dispositivo con il servizio Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Per altre informazioni, vedere [ottenere una chiave di attivazione](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggi il dispositivo tramite password

Le password assicurano che solo gli utenti autorizzati possano accedere ai dati. I dispositivi Data Box Edge vengono avviati in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente Web locale del dispositivo tramite un browser e quindi specificare una password per accedere al dispositivo.
- Connettersi in remoto all'interfaccia di PowerShell del dispositivo tramite HTTP. La gestione remota è attivata per impostazione predefinita. È quindi possibile specificare la password del dispositivo per accedere al dispositivo. Per altre informazioni, vedere [connettersi in remoto al dispositivo data box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Usare l'interfaccia utente Web locale per [modificare la password](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se si modifica la password, assicurarsi di notificare a tutti gli utenti di accesso remoto in modo che non si verifichino problemi durante l'accesso.

## <a name="protect-your-data"></a>Proteggi i tuoi dati

Questa sezione descrive le funzionalità di sicurezza di Data Box Edge che proteggono i dati archiviati e in transito.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256 bit Encryption viene usato per proteggere i dati locali.


### <a name="protect-data-in-flight"></a>Proteggere i dati in corso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Ruotare e [sincronizzare regolarmente le chiavi dell'account di archiviazione](data-box-edge-manage-shares.md#sync-storage-keys) per proteggere l'account di archiviazione da utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestisci informazioni personali

Il servizio Data Box Edge raccoglie informazioni personali negli scenari seguenti:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco degli utenti che possono accedere o eliminare una condivisione, attenersi alla procedura descritta in [gestire le condivisioni nel data box Edge](data-box-edge-manage-shares.md).

Per ulteriori informazioni, consultare l'informativa sulla privacy Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo Data Box Edge](data-box-edge-deploy-prep.md)
