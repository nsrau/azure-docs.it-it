---
title: Metodi di autenticazione per il Centro sicurezza di Azure per ioT Documenti Microsoft
description: Informazioni sui diversi metodi di autenticazione disponibili quando si usa il Centro sicurezza di Azure per il servizio IoT.Learn about the different authentication methods available when using the Azure Security Center for IoT service.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 16f7f91e02d118d9f9a295ebb79a6cd0187dd9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596460"
---
# <a name="security-agent-authentication-methods"></a>Metodi di autenticazione del Security Agent 

Questo articolo illustra i diversi metodi di autenticazione che è possibile usare con l'agente AzureIoTSecurity per eseguire l'autenticazione con l'hub IoT.This article explains the different authentication methods you can use with the AzureIoTSecurity agent to authenticate with the IoT Hub.

Per ogni dispositivo attivato nel Centro sicurezza di Azure per l'IoT nell'hub IoT, è necessario un modulo di sicurezza. Per autenticare il dispositivo, il Centro sicurezza di Azure per ioT può usare uno dei due metodi seguenti. Scegliere il metodo più adatto alla soluzione IoT esistente. 

> [!div class="checklist"]
> * Opzione SecurityModule
> * Opzione del dispositivo

## <a name="authentication-methods"></a>Metodi di autenticazione

I due metodi per l'agente AzureIoTSecurity per eseguire l'autenticazione:The two methods for the AzureIoTSecurity agent to perform authentication:

 - Modalità di autenticazione **SecurityModule**<br>
   L'agente viene autenticato utilizzando l'identità del modulo di sicurezza indipendentemente dall'identità del dispositivo.
   Utilizzare questo tipo di autenticazione se si desidera che l'agente di sicurezza utilizzi un metodo di autenticazione dedicato tramite il modulo di sicurezza (solo chiave simmetrica).
        
 - **Modalità di** autenticazione del dispositivo<br>
    In questo metodo, l'agente di sicurezza esegue innanzitutto l'autenticazione con l'identità del dispositivo. Dopo l'autenticazione iniziale, il Centro sicurezza di Azure per l'agente IoT esegue una chiamata **REST** all'hub IoT usando l'API REST con i dati di autenticazione del dispositivo. The Azure Security Center for IoT agent then requests the security module authentication method and data from the IoT Hub. Nel passaggio finale, il Centro sicurezza di Azure per l'agente IoT esegue un'autenticazione sul modulo Centro sicurezza di Azure per IoT.In the final step, the Azure Security Center for IoT agent performs an authentication against the Azure Security Center for IoT module.
    
    Utilizzare questo tipo di autenticazione se si desidera che l'agente di sicurezza riutilizzi un metodo di autenticazione del dispositivo esistente (certificato autofirmato o chiave simmetrica). 

Per informazioni su come configurare, vedere Parametri di [installazione dell'agente](#security-agent-installation-parameters) di sicurezza.
                                
## <a name="authentication-methods-known-limitations"></a>Problemi noti dei metodi di autenticazione

- La modalità di autenticazione **SecurityModule** supporta solo l'autenticazione a chiave simmetrica.
- Il certificato firmato da CA non è supportato dalla modalità di autenticazione **del dispositivo.**  

## <a name="security-agent-installation-parameters"></a>Parametri di installazione del Security Agent

Quando [si distribuisce un agente di sicurezza,](how-to-deploy-agent.md)i dettagli di autenticazione devono essere forniti come argomenti.
Questi argomenti sono documentati nella tabella seguente.


|Nome parametro Linux | Nome parametro di Windows | Parametro a sintassi abbreviata |Descrizione|Opzioni|
|---------------------|---------------|---------|---------------|---------------|
|authentication-identity (identità autenticazione)|AuthenticationIdentity|Aui|Identità di autenticazione| **SecurityModule** o **dispositivo**|
|authentication-method|AuthenticationMethod (Metodo Authentication)|Aum|Metodo di autenticazione|**SymmetricKey** o **SelfSignedCertificate**|
|percorso file|FilePath|f|Percorso completo assoluto del file contenente il certificato o la chiave simmetrica| |
|nome host|HostName|Hn|FQDN dell'hub IoT|Esempio: ContosoIotHub.azure-devices.net|
|id-dispositivo|deviceId|di di|ID dispositivo|Esempio: MyDevice1|
|tipo di certificato|CertificateLocationKind|Cl|Percorso di archiviazione dei certificati|**LocalFile** o **Store**|
|


Quando si utilizza lo script dell'agente di sicurezza di installazione, viene eseguita automaticamente la configurazione seguente. Per modificare manualmente l'autenticazione dell'agente di sicurezza, modificare il file di configurazione. 

## <a name="change-authentication-method-after-deployment"></a>Modificare il metodo di autenticazione dopo la distribuzioneChange authentication method after deployment

Quando si distribuisce un security agent con uno script di installazione, viene creato automaticamente un file di configurazione.

Per modificare i metodi di autenticazione dopo la distribuzione, è necessaria la modifica manuale del file di configurazione.


### <a name="c-based-security-agent"></a>Agente di sicurezza basato su C

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

### <a name="c-based-security-agent"></a>Agente di sicurezza basato su C

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
- [Distribuire l'agente di sicurezzaDeploy security agent](how-to-deploy-agent.md)
- [Accedere a dati non elaborati sulla sicurezza](how-to-security-data-access.md)
