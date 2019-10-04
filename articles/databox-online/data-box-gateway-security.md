---
title: Sicurezza Azure Data Box Gateway | Microsoft Docs
description: Descrive le funzionalità di sicurezza e privacy che proteggono il dispositivo virtuale, il servizio e i dati Azure Data Box Gateway, in locale e nel cloud.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900613"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Sicurezza e protezione dei dati Azure Data Box Gateway

La sicurezza è un problema fondamentale quando si adotta una nuova tecnologia, soprattutto se la tecnologia viene usata con dati riservati o proprietari. Azure Data Box Gateway consente di verificare che solo le entità autorizzate possano visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza di Azure Data Box Gateway che consentono di proteggere i componenti della soluzione e i dati archiviati.

La soluzione Data Box Gateway è costituita da quattro componenti principali che interagiscono tra loro:

- **Servizio Data Box gateway, ospitato in Azure**. La risorsa di gestione usata per creare l'ordine dei dispositivi, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Data Box Gateway dispositivo**. Il dispositivo virtuale di cui si esegue il provisioning nell'hypervisor del sistema fornito. Questo dispositivo virtuale viene usato per importare i dati locali in Azure.
- **Client/host connessi al dispositivo**. I client nell'infrastruttura che si connettono al dispositivo Data Box Gateway e contengono dati che devono essere protetti.
- **Archiviazione cloud**. Posizione della piattaforma cloud di Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa Data Box Gateway creata.


## <a name="data-box-gateway-service-protection"></a>Protezione del servizio Data Box Gateway

Il servizio Data Box Gateway è un servizio di gestione ospitato in Azure. Il servizio viene usato per configurare e gestire il dispositivo.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Protezione del dispositivo Data Box Gateway

Il dispositivo Data Box Gateway è un dispositivo virtuale di cui è stato effettuato il provisioning nell'hypervisor di un sistema locale fornito dall'utente. Il dispositivo consente di inviare i dati ad Azure. Il dispositivo:

- Richiede una chiave di attivazione per accedere al servizio Data Box Edge/Data Box Gateway.
- È sempre protetto da una password del dispositivo.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite la chiave di attivazione

Solo un dispositivo Data Box Gateway autorizzato è autorizzato a partecipare al servizio Data Box Gateway creato nella sottoscrizione di Azure. Per autorizzare un dispositivo, è necessario usare una chiave di attivazione per attivare il dispositivo con il servizio Data Box Gateway.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Per altre informazioni, vedere [ottenere una chiave di attivazione](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggi il dispositivo tramite password

Le password assicurano che solo gli utenti autorizzati possano accedere ai dati. I dispositivi Data Box Gateway vengono avviati in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente Web locale del dispositivo tramite un browser e quindi specificare una password per accedere al dispositivo.
- Connettersi in remoto all'interfaccia di PowerShell del dispositivo tramite HTTP. La gestione remota è attivata per impostazione predefinita. È quindi possibile specificare la password del dispositivo per accedere al dispositivo. Per altre informazioni, vedere [connettersi in remoto al dispositivo data box gateway](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Usare l'interfaccia utente Web locale per [modificare la password](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se si modifica la password, assicurarsi di notificare a tutti gli utenti di accesso remoto in modo che non si verifichino problemi durante l'accesso.


## <a name="protect-your-data"></a>Proteggi i tuoi dati

Questa sezione descrive le funzionalità di sicurezza di Data Box Gateway che proteggono i dati archiviati e in transito.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteggere i dati in corso

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Ruotare e [sincronizzare regolarmente le chiavi dell'account di archiviazione](data-box-gateway-manage-shares.md#sync-storage-keys) per proteggere l'account di archiviazione da utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestisci informazioni personali

Il servizio Data Box Gateway raccoglie informazioni personali negli scenari seguenti:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco degli utenti che possono accedere o eliminare una condivisione, attenersi alla procedura descritta in [gestire le condivisioni nel data box gateway](data-box-gateway-manage-shares.md).

Per ulteriori informazioni, consultare l'informativa sulla privacy Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo Data Box Gateway](data-box-gateway-deploy-prep.md)
