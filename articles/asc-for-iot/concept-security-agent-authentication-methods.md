---
title: Metodi di autenticazione per Centro sicurezza di AZURE per l'anteprima di IoT | Microsoft Docs
description: Informazioni sui metodi di autenticazione diversi disponibili quando si usa il Centro sicurezza di AZURE per il servizio IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 2ace8ffd82efe70251b48e20593906986173cbb0
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577801"
---
# <a name="security-agent-authentication-methods"></a>Metodi di autenticazione di sicurezza dell'agente 

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra i diversi metodi di autenticazione che è possibile usare con l'agente AzureIoTSecurity per eseguire l'autenticazione con l'IoT Hub.

Per ogni dispositivo eseguito l'onboarding a Centro sicurezza di AZURE per IoT nell'IoT Hub, è necessario un modulo di protezione. Per autenticare il dispositivo, Centro sicurezza di AZURE per IoT possono usare uno dei due metodi. Scegliere il metodo adatto per la soluzione IoT esistente. 

> [!div class="checklist"]
> * Opzione di modulo di protezione
> * Opzione del dispositivo

## <a name="authentication-methods"></a>Metodi di autenticazione

I due metodi per l'agente AzureIoTSecurity eseguire l'autenticazione:

 - **Modulo** modalità di autenticazione<br>
   Il modulo di autenticazione viene eseguito indipendentemente dal dispositivo gemello.
   Se si vuole che l'agente di sicurezza da utilizzare un metodo di autenticazione dedicato tramite modulo di protezione (solo chiave simmetrica), usare questo tipo di autenticazione.
        
 - **Dispositivo** modalità di autenticazione<br>
    In questo metodo, l'agente protezione viene autenticata prima con l'identità del dispositivo. Al termine dell'autenticazione iniziale, il Centro sicurezza di AZURE per IoT agente esegue una **REST** chiamate all'IoT Hub usando l'API REST con i dati di autenticazione del dispositivo. Il Centro sicurezza di AZURE per l'agente di IoT richiede quindi il metodo di autenticazione modulo di protezione e i dati dall'IoT Hub. Nel passaggio finale, il Centro sicurezza di AZURE per IoT agente esegue l'autenticazione con il Centro sicurezza di AZURE per il modulo di IoT.
    
    Se si vuole che l'agente di protezione per riutilizzare un metodo di autenticazione dispositivo esistente (autofirmato certificato o chiave simmetrica), usare questo tipo di autenticazione. 

Visualizzare [parametri di installazione dell'agente protezione](#security-agent-installation-parameters) per imparare a configurare.
                                
## <a name="authentication-methods-known-limitations"></a>Metodi di autenticazione limitazioni note

- **Modulo** modalità di autenticazione supporta solo l'autenticazione con chiave simmetrica.
- Certificato CA firmato non è supportata dal **dispositivo** modalità di autenticazione.  

## <a name="security-agent-installation-parameters"></a>Parametri di installazione dell'agente protezione

Quando [distribuzione di un agente protezione](how-to-deploy-agent.md), è necessario specificare i dettagli di autenticazione come argomenti.
Questi argomenti sono documentati nella tabella seguente.


|Parametro|DESCRIZIONE|Opzioni|
|---------|---------------|---------------|
|**identity**|Modalità di autenticazione| **Modulo** o **dispositivo**|
|**type**|Tipo di autenticazione|**Classe SymmetricKey** o **SelfSignedCertificate**|
|**filePath**|Percorso completo assoluto del file che contiene il certificato o la chiave simmetrica| |
|**gatewayHostname**|Nome di dominio completo dell'Hub IoT|Esempio: ContosoIotHub.azure-devices.net|
|**deviceId**|ID dispositivo|Esempio: MyDevice1|
|**certificateLocationKind**|Percorso dell'archivio certificati|**LocalFile** o **Store**|


Quando si usa lo script dell'agente protezione di installazione, la configurazione seguente viene eseguita automaticamente.

Per modificare manualmente l'autenticazione di sicurezza dell'agente, modificare il file di configurazione. 

## <a name="change-authentication-method-after-deployment"></a>Modifica il metodo di autenticazione dopo la distribuzione

Quando si distribuisce un agente protezione con uno script di installazione, viene creato automaticamente un file di configurazione.

Per modificare i metodi di autenticazione dopo la distribuzione, è richiesta la modifica manuale del file di configurazione.


### <a name="c-based-security-agent"></a>C#-agente di protezione basato su

Modificare _config_ con i parametri seguenti:

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

### <a name="c-based-security-agent"></a>Agente di protezione basato sul linguaggio C

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
- [Panoramica degli agenti protezione](security-agent-architecture.md)
- [Distribuire l'agente protezione](how-to-deploy-agent.md)
- [Accedere ai dati di sicurezza non elaborati](how-to-security-data-access.md)
