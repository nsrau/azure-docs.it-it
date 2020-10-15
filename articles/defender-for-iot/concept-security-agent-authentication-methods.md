---
title: Metodi di autenticazione dell'agente di sicurezza
description: Informazioni sui diversi metodi di autenticazione disponibili quando si usa il servizio Defender for Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 896baa70fb6e18165a025459a063e33d705261ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939485"
---
# <a name="security-agent-authentication-methods"></a>Metodi di autenticazione dell'agente di sicurezza

Questo articolo illustra i diversi metodi di autenticazione che è possibile usare con l'agente AzureIoTSecurity per l'autenticazione con l'hub Internet delle cose.

Per ogni dispositivo caricato in Defender per le cose nell'hub Internet, è necessario un modulo di sicurezza. Per autenticare il dispositivo, Defender for Internet è in grado di usare uno dei due metodi seguenti. Scegliere il metodo più adatto per la soluzione Internet delle cose esistente.

> [!div class="checklist"]
> * SecurityModule-opzione
> * Opzione del dispositivo

## <a name="authentication-methods"></a>Metodi di autenticazione

I due metodi per l'agente AzureIoTSecurity per eseguire l'autenticazione:

- Modalità di autenticazione **SecurityModule**<br>
L'agente viene autenticato usando l'identità del modulo di sicurezza indipendentemente dall'identità del dispositivo.
Utilizzare questo tipo di autenticazione se si desidera che l'agente di sicurezza utilizzi un metodo di autenticazione dedicato tramite il modulo di sicurezza (solo chiave simmetrica).

- Modalità di autenticazione del **dispositivo**<br>
In questo metodo, l'agente di sicurezza esegue prima l'autenticazione con l'identità del dispositivo. Dopo l'autenticazione iniziale, il Defender per l'agente Internet esegue una chiamata **Rest** all'hub Internet con l'API REST con i dati di autenticazione del dispositivo. Il Defender per l'agente Internet richiede quindi i dati e il metodo di autenticazione del modulo di sicurezza dall'hub Internet delle cose. Nel passaggio finale, il Defender per l'agente Internet esegue un'autenticazione con il modulo Defender for Internet.

Utilizzare questo tipo di autenticazione se si desidera che l'agente di sicurezza riutilizzi un metodo di autenticazione del dispositivo esistente (certificato autofirmato o chiave simmetrica).

Per informazioni sulla configurazione, vedere [parametri di installazione dell'agente di sicurezza](#security-agent-installation-parameters) .

## <a name="authentication-methods-known-limitations"></a>Limitazioni note ai metodi di autenticazione

- La modalità di autenticazione **SecurityModule** supporta solo l'autenticazione con chiave simmetrica.
- CA-Signed certificato non è supportato dalla modalità di autenticazione del **dispositivo** .

## <a name="security-agent-installation-parameters"></a>Parametri di installazione dell'agente di sicurezza

Quando si [distribuisce un agente sicurezza](how-to-deploy-agent.md), i dettagli di autenticazione devono essere specificati come argomenti.
Questi argomenti sono documentati nella tabella seguente.

|Nome parametro Linux | Nome parametro Windows | Parametro abbreviato |Descrizione|Opzioni|
|---------------------|---------------|---------|---------------|---------------|
|autenticazione-identità|AuthenticationIdentity|AUI|Identità di autenticazione| **SecurityModule** o **dispositivo**|
|authentication-method|AuthenticationMethod|Aum|Metodo di autenticazione|**SymmetricKey** **SelfSignedCertificate**|
|percorso file|FilePath|f|Percorso completo assoluto per il file che contiene il certificato o la chiave simmetrica| |
|nome-host|HostName|HN|FQDN dell'hub Internet delle cose|Esempio: ContosoIotHub.azure-devices.net|
|ID dispositivo|DeviceId|inserimento delle dipendenze|ID dispositivo|Esempio: MyDevice1|
|tipo di certificato-location|CertificateLocationKind|CL|Percorso di archiviazione certificati|**LocalFile** o **Store**|
|

Quando si usa lo script Install Security Agent, la configurazione seguente viene eseguita automaticamente. Per modificare manualmente l'autenticazione dell'agente di sicurezza, modificare il file di configurazione.

## <a name="change-authentication-method-after-deployment"></a>Modificare il metodo di autenticazione dopo la distribuzione

Quando si distribuisce un agente di sicurezza con uno script di installazione, viene creato automaticamente un file di configurazione.

Per modificare i metodi di autenticazione dopo la distribuzione, è necessario modificare manualmente il file di configurazione.

### <a name="c-based-security-agent"></a>Agente sicurezza basato su C#

Modificare _Authentication.config_ con i parametri seguenti:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agente sicurezza basato su C

Modificare _LocalConfiguration.json_ con i parametri seguenti:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Vedere anche

- [Panoramica degli agenti di sicurezza](security-agent-architecture.md)
- [Distribuisci agente sicurezza](how-to-deploy-agent.md)
- [Accedere a dati non elaborati sulla sicurezza](how-to-security-data-access.md)
