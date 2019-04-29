---
title: Sicurezza di Azure Gateway dati casella | Microsoft Docs
description: Vengono descritte le funzionalità di sicurezza e privacy che proteggono il dispositivo virtuale Gateway casella dei dati di Azure, servizio e i dati, in locale e nel cloud.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 230d1a28ba15a8736e46c02cb08217a28fc18599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754313"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Gateway dati casella sicurezza e protezione dei dati

Sicurezza è delle principali preoccupazioni quando si sta adottando una nuova tecnologia, soprattutto se la tecnologia viene usata con dati riservati o proprietari. Azure consente di Gateway di dati finestra assicurare che solo autorizzati le entità possono visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza Gateway casella dei dati di Azure che consentono di proteggere ognuno dei componenti della soluzione e i dati archiviati al loro interno.

La soluzione del Gateway di dati finestra è costituito da quattro componenti principali che interagiscono tra loro:

- **Servizio casella Gateway di dati, ospitato in Azure**. La risorsa di gestione che consente la creazione dell'ordine di dispositivo, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Dispositivo Gateway finestra dati**. Il dispositivo virtuale che si esegue il provisioning nell'hypervisor del sistema specificata dall'utente. Questo dispositivo virtuale viene usato per importare i dati in locale in Azure.
- **Client/host connessi al dispositivo**. I client nell'infrastruttura che si connettono al dispositivo Gateway casella dei dati e contengano dati che devono essere protetti.
- **Archiviazione cloud**. Il percorso alla piattaforma cloud di Azure dove sono archiviati dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa Gateway di dati finestra creati.


## <a name="data-box-gateway-service-protection"></a>Protezione di servizio finestra Gateway dati

Il servizio Gateway di dati finestra è un servizio di gestione ospitato in Azure. Il servizio viene usato per configurare e gestire il dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Protezione dei dati per il dispositivo Gateway finestra

Il dispositivo Gateway di dati finestra è un dispositivo virtuale che viene eseguito il provisioning nell'hypervisor di un sistema locale specificata dall'utente. Il dispositivo consente di inviare dati ad Azure. Il dispositivo:

- Richiede una chiave di attivazione per accedere al servizio Gateway di dati finestra Edge/Data finestra.
- È protetta in tutte le volte in cui da una password del dispositivo.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite una chiave di attivazione

Solo un dispositivo Gateway di dati finestra autorizzato è consentito aggiungere il servizio Gateway di dati finestra creati nella sottoscrizione di Azure. Per autorizzare un dispositivo, è necessario usare una chiave di attivazione per attivare il dispositivo con il servizio Gateway di dati finestra.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Per altre informazioni, vedere [ottenere una chiave di attivazione](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggere il dispositivo tramite password

Le password assicurarsi che solo gli utenti autorizzati possono accedere ai dati. Data Gateway casella dispositivi avvio in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente del dispositivo tramite un browser Web locale e quindi specificare una password per accedere al dispositivo.
- Connettersi in remoto all'interfaccia PowerShell del dispositivo tramite HTTP. Gestione remota è attivata per impostazione predefinita. È quindi possibile fornire la password del dispositivo per accedere al dispositivo. Per altre informazioni, vedere [Connect in modalità remota al dispositivo Gateway di dati finestra](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilizzo interfaccia utente al web locale [modificare la password](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se si modifica la password, assicurarsi di notificarlo a tutti gli utenti di accesso remoto in modo che non hanno problemi di accesso.


## <a name="protect-your-data"></a>Proteggi i tuoi dati

In questa sezione vengono descritte le funzionalità di sicurezza di Gateway finestra dati che proteggono i dati archiviati e in transito.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteggere i dati in movimento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Ruotare e quindi [sincronizzare le chiavi dell'account di archiviazione](data-box-gateway-manage-shares.md#sync-storage-keys) regolarmente per proteggere l'account di archiviazione da utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestire le informazioni personali

Il servizio Gateway di dati finestra raccoglie informazioni personali negli scenari seguenti:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco di utenti che possono accedere o eliminare una condivisione, seguire i passaggi descritti in [gestire le condivisioni nel Gateway dati casella](data-box-gateway-manage-shares.md).

Per altre informazioni, esaminare l'informativa sulla privacy di Microsoft nella [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un dispositivo Gateway di dati finestra](data-box-gateway-deploy-prep.md)
