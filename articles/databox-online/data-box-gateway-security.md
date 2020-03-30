---
title: Sicurezza di Azure Data Box Gateway - Documenti Microsoft
description: Descrive le funzionalità di sicurezza e privacy che proteggono il dispositivo virtuale, il servizio e i dati di Azure Data Box Gateway in locale e nel cloud.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900613"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Sicurezza e protezione dei dati di Azure Data Box GatewayAzure Data Box Gateway security and data protection

La sicurezza è una delle principali preoccupazioni quando si adotta una nuova tecnologia, soprattutto se la tecnologia viene utilizzata con dati riservati o proprietari. Azure Data Box Gateway consente di verificare che solo le entità autorizzate possano visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza di Azure Data Box Gateway che consentono di proteggere ogni componente della soluzione e i dati archiviati in essi.

La soluzione Gateway data box è costituita da quattro componenti principali che interagiscono tra loro:

- **Servizio Gateway casella dati, ospitato in Azure.** Risorsa di gestione utilizzata per creare l'ordine dei dispositivi, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Dispositivo Gateway casella dati**. Il dispositivo virtuale di cui si esegue il provisioning nell'hypervisor del sistema fornito. Questo dispositivo virtuale viene usato per importare i dati locali in Azure.This virtual device is used to import your on-premises data into Azure.
- **Client/host connessi al dispositivo**. I client dell'infrastruttura che si connettono al dispositivo Gateway di dati e contengono dati che devono essere protetti.
- **Archiviazione cloud**. Posizione nella piattaforma cloud di Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa Gateway casella dati creata.


## <a name="data-box-gateway-service-protection"></a>Protezione del servizio Gateway di dati

Il servizio Gateway di Data Box è un servizio di gestione ospitato in Azure.The Data Box Gateway service is a management service that's hosted in Azure. Il servizio viene utilizzato per configurare e gestire il dispositivo.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Protezione del dispositivo Data Box Gateway

Il dispositivo Gateway data è un dispositivo virtuale di cui viene eseguito il provisioning nell'hypervisor di un sistema locale fornito. Il dispositivo consente di inviare dati ad Azure.The device helps send data to Azure. Il tuo dispositivo:

- È necessaria una chiave di attivazione per accedere al servizio Data Box Edge/Data Box Gateway.
- È protetto in ogni momento da una password del dispositivo.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite chiave di attivazione

Solo un dispositivo Gateway casella dati autorizzato può partecipare al servizio Gateway casella dati creato nella sottoscrizione di Azure.Only an authorized Data Box Gateway device is allowed join the Data Box Gateway service that you create in your Azure subscription. Per autorizzare un dispositivo, è necessario usare un codice di attivazione per attivare il dispositivo con il servizio Gateway di data Box.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Per ulteriori informazioni, vedere Ottenere una chiave di [attivazione](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggere il dispositivo tramite password

Le password garantiscono che solo gli utenti autorizzati possano accedere ai dati. I dispositivi Gateway dati si avviano in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente Web locale del dispositivo tramite un browser e quindi fornire una password per accedere al dispositivo.
- Connessione remota all'interfaccia PowerShell del dispositivo tramite HTTP. La gestione remota è attivata per impostazione predefinita. È quindi possibile fornire la password del dispositivo per accedere al dispositivo. Per ulteriori informazioni, vedere [Connettersi in remoto al dispositivo Gateway data](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)box .

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilizzare l'interfaccia utente Web locale per [modificare la password.](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access) Se si modifica la password, assicurarsi di inviare una notifica a tutti gli utenti di accesso remoto in modo che non abbiano problemi di accesso.


## <a name="protect-your-data"></a>Proteggere i dati

In questa sezione vengono descritte le funzionalità di sicurezza di Data Box Gateway che proteggono i dati in transito e archiviati.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteggi i dati in volo

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Ruotare e quindi sincronizzare regolarmente [le chiavi dell'account di archiviazione](data-box-gateway-manage-shares.md#sync-storage-keys) per proteggere l'account di archiviazione da utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestire le informazioni personali

Il servizio Gateway casella dati raccoglie informazioni personali negli scenari seguenti:The Data Box Gateway service collects personal information in the following scenarios:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco degli utenti che possono accedere o eliminare una condivisione, seguire la procedura descritta in [Gestire le condivisioni nel Gateway casella dati](data-box-gateway-manage-shares.md).

Per ulteriori informazioni, vedere l'informativa sulla privacy di Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo Gateway di Data Box](data-box-gateway-deploy-prep.md)
