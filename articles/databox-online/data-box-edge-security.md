---
title: Sicurezza di Azure Data Box Edge - Documenti Microsoft
description: Descrive le funzionalità di sicurezza e privacy che proteggono il dispositivo, il servizio e i dati di Azure Data Box Edge in locale e nel cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970892"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Sicurezza e protezione dei dati per Azure Data Box Edge

La sicurezza è una delle principali preoccupazioni quando si adotta una nuova tecnologia, soprattutto se la tecnologia viene utilizzata con dati riservati o proprietari. Azure Data Box Edge consente di garantire che solo le entità autorizzate possano visualizzare, modificare o eliminare i dati.

In questo articolo vengono descritte le funzionalità di sicurezza di Data Box Edge che consentono di proteggere ogni componente della soluzione e i dati in essi archiviati.

Azure Data Box Edge è costituito da quattro componenti principali che interagiscono tra loro:Azure Data Box Edge consists of four main components that interact with each other:

- **Servizio Data Box Edge, ospitato in Azure.** Risorsa di gestione utilizzata per creare l'ordine dei dispositivi, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Dispositivo Data Box Edge**. Dispositivo di trasferimento fornito all'utente in modo da poter importare i dati locali in Azure.The transfer device that's shipped to you so you can import your on-premises data into Azure.
- **Client/host connessi al dispositivo**. I client dell'infrastruttura che si connettono al dispositivo Data Box Edge e contengono dati che devono essere protetti.
- **Archiviazione cloud**. Posizione nella piattaforma cloud di Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa Data Box Edge creata.

## <a name="data-box-edge-service-protection"></a>Protezione del servizio Data Box Edge

Il servizio Data Box Edge è un servizio di gestione ospitato in Azure.The Data Box Edge service is a management service that's hosted in Azure. Il servizio viene utilizzato per configurare e gestire il dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Protezione del dispositivo Data Box Edge

Il dispositivo Data Box Edge è un dispositivo locale che consente di trasformare i dati elaborandoli in locale e quindi inviandoli ad Azure.The Data Box Edge device is an on-premises device that helps transform your data by processing it locally and then sending to Azure. Il tuo dispositivo:

- È necessaria una chiave di attivazione per accedere al servizio Data Box Edge.
- È protetto in ogni momento da una password del dispositivo.
- È un dispositivo bloccato. Il dispositivo BMC e il BIOS sono protetti da password. Il BIOS è protetto da un accesso limitato da parte degli utenti.
- L'avvio sicuro è abilitato.
- Esegue Windows Defender Device Guard. Device Guard consente di eseguire solo applicazioni attendibili definite nei criteri di integrità del codice.

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite chiave di attivazione

Solo un dispositivo Data Box Edge autorizzato può partecipare al servizio Data Box Edge creato nella sottoscrizione di Azure.Only an authorized Data Box Edge device is allowed join the Data Box Edge service that you create in your Azure subscription. Per autorizzare un dispositivo, è necessario utilizzare un codice di attivazione per attivare il dispositivo con il servizio Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Per ulteriori informazioni, vedere Ottenere una chiave di [attivazione](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggere il dispositivo tramite password

Le password garantiscono che solo gli utenti autorizzati possano accedere ai dati. I dispositivi Data Box Edge si avviano in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente Web locale del dispositivo tramite un browser e quindi fornire una password per accedere al dispositivo.
- Connessione remota all'interfaccia PowerShell del dispositivo tramite HTTP. La gestione remota è attivata per impostazione predefinita. È quindi possibile fornire la password del dispositivo per accedere al dispositivo. Per ulteriori informazioni, consultate [Connettersi in remoto al dispositivo Data Box Edge.](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilizzare l'interfaccia utente Web locale per [modificare la password.](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access) Se si modifica la password, assicurarsi di inviare una notifica a tutti gli utenti di accesso remoto in modo che non abbiano problemi di accesso.

## <a name="protect-your-data"></a>Proteggere i dati

In questa sezione vengono descritte le funzionalità di sicurezza di Data Box Edge che proteggono i dati archiviati e in transito.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- La crittografia a 256 bit XTS-AES di BitLocker viene utilizzata per proteggere i dati locali.


### <a name="protect-data-in-flight"></a>Proteggi i dati in volo

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Ruotare e quindi sincronizzare regolarmente [le chiavi dell'account di archiviazione](data-box-edge-manage-shares.md#sync-storage-keys) per proteggere l'account di archiviazione da utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestire le informazioni personali

Il servizio Data Box Edge raccoglie informazioni personali negli scenari seguenti:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco degli utenti che possono accedere o eliminare una condivisione, seguire la procedura descritta in [Gestire le condivisioni in Data Box Edge](data-box-edge-manage-shares.md).

Per ulteriori informazioni, vedere l'informativa sulla privacy di Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo Data Box Edge](data-box-edge-deploy-prep.md)
