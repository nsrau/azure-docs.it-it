---
title: Comunicazione protetta con il proxy inverso di Azure Service Fabric | Microsoft Docs
description: Configurare il proxy inverso per abilitare la comunicazione end-to-end protetta.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 568f9638c59282bcd7d3fae058a1588a889c22dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Connettersi a un servizio protetto con il proxy inverso

In questo articolo viene spiegato come stabilire una connessione protetta tra il proxy inverso e i servizi, abilitando un canale protetto end-to-end.

La connessione ai servizi protetti è supportata solo quando il proxy inverso è configurato per l'ascolto su HTTPS. In questo documento si presuppone che questo sia il caso.
Fare riferimento a [Proxy inverso in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) per configurare il proxy inverso in Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Stabilire una connessione protetta tra il proxy inverso e i servizi 

### <a name="reverse-proxy-authenticating-to-services"></a>Autenticazione del proxy inverso per i servizi:
Il proxy inverso si autoidentifica per i servizi usando il certificato, specificato con la proprietà ***reverseProxyCertificate*** nella [sezione relativa al tipo di risorsa](../azure-resource-manager/resource-group-authoring-templates.md) del **cluster** . I servizi possono implementare la logica per verificare il certificato presentato dal proxy inverso. I servizi possono specificare i dettagli del certificato client accettati come impostazioni di configurazione nel pacchetto di configurazione. Questo può essere letto in fase di runtime e usato per convalidare il certificato presentato dal proxy inverso. Fare riferimento a [Gestire i parametri dell'applicazione](service-fabric-manage-multiple-environment-app-configuration.md) per aggiungere le impostazioni di configurazione. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Verifica dell'identità del servizio da parte del proxy inverso tramite il certificato presentato dal servizio:
Per eseguire la convalida del certificato del server per i certificati presentati dai servizi, il proxy inverso supporta una delle opzioni seguenti: None, ServiceCommonNameAndIssuer e ServiceCertificateThumbprints.
Per selezionare una di queste tre opzioni, specificare **ApplicationCertificateValidationPolicy** nella sezione dei parametri dell'elemento ApplicationGateway/Http in [fabricSettings](service-fabric-cluster-fabric-settings.md).

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "None"
              }
            ]
          }
        ],
        ...
}
```

Fare riferimento alla sezione successiva per informazioni sulla configurazione aggiuntiva per ognuna di queste opzioni.

### <a name="service-certificate-validation-options"></a>Opzioni di convalida dei certificati del servizio 

- **None**: il proxy inverso ignora la verifica del certificato del servizio di proxy e stabilisce la connessione sicura. Questo è il comportamento predefinito.
Specificare **ApplicationCertificateValidationPolicy** con il valore **None** nella sezione dei parametri dell'elemento ApplicationGateway/Http.

- **ServiceCommonNameAndIssuer**: il proxy inverso verifica il certificato presentato dal servizio in base al nome comune del certificato e all'identificazione personale immediata dell'autorità di certificazione: specificare **ApplicationCertificateValidationPolicy** con valore **ServiceCommonNameAndIssuer** nella sezione dei parametri dell'elemento ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCommonNameAndIssuer"
              }
            ]
          }
        ],
        ...
}
```

Per specificare l'elenco del nome comune del servizio e l'identificazione personale dell'autorità di certificazione, aggiungere un elemento **ApplicationGateway/Http/ServiceCommonNameAndIssuer** in fabricsettings, come illustrato di seguito. Nell'elemento della matrice di parametri è possibile aggiungere associazioni multiple del nome comune del certificato e dell'identificazione personale dell'autorità di certificazione. 

Se il proxy inverso dell'endpoint si connette per presentare un certificato il cui nome comune e l'identificazione personale dell'autorità di certificazione corrisponde a uno qualsiasi dei valori specificati in questo caso, viene stabilito il canale SSL. In caso di errore nella corrispondenza dei dettagli del certificato, il proxy inverso non esegue correttamente la richiesta del client e presenta un codice di stato 502, ovvero Gateway non valido. La riga di stato HTTP conterrà anche la frase "Invalid SSL Certificate" (Certificato SSL non valido). 

```json
{
"fabricSettings": [
          ...
          {
             "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
            "parameters": [
              {
                "name": "WinFabric-Test-Certificate-CN1",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
              },
              {
                "name": "WinFabric-Test-Certificate-CN2",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
              }
            ]
          }
        ],
        ...
}
```


- **ServiceCertificateThumbprints**: il proxy inverso consente di verificare il certificato del servizio del proxy in base all'identificazione personale. È possibile scegliere di intraprendere questa strada quando i servizi vengono configurati con certificati autofirmati : specificare **ApplicationCertificateValidationPolicy** con valore **ServiceCertificateThumbprints** nella sezione dei parametri dell'elemento ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCertificateThumbprints"
              }
            ]
          }
        ],
        ...
}
```

Specificare anche le identificazioni personali con una voce **ServiceCertificateThumbprints** nella sezione parametri dell'elemento ApplicationGateway/Http. Nel campo del valore è possibile specificare più identificazioni personali come un elenco delimitato da virgole, come illustrato di seguito:

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "ServiceCertificateThumbprints",
                "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
              }
            ]
          }
        ],
        ...
}
```

Se l'identificazione personale del certificato del server è elencata in questa voce di configurazione, il proxy inverso esegue correttamente la connessione SSL. In caso contrario, termina la connessione e non esegue correttamente la richiesta del client con un errore 502, ovvero Gateway non valido. La riga di stato HTTP conterrà anche la frase "Invalid SSL Certificate" (Certificato SSL non valido).

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Logica di scelta dell'endpoint quando i servizi espongono endpoint sicuri e non sicuri
Service Fabric supporta la configurazione di più endpoint per un servizio. Vedere [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md).

Il proxy inverso consente di selezionare uno degli endpoint per inoltrare la richiesta in base al parametro di query **ListenerName**. Se non viene specificato, viene scelto un endpoint qualsiasi nell'elenco degli endpoint. A questo punto potrebbe essere un endpoint HTTP o HTTPS. Potrebbero verificarsi situazioni in cui si desidera o è necessario che il proxy inverso funzioni "solo in modalità protetta", per evitare che si inoltrino richieste agli endpoint non protetti. A tale scopo è necessario impostare il valore della voce della configurazione **SecureOnlyMode** su **true** nella sezione dei parametri dell'elemento ApplicationGateway/Http.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> 
> Quando si opera in **SecureOnlyMode**, se il client ha specificato un **ListenerName** corrispondente a un endpoint HTTP, non sicuro, il proxy inverso non esegue correttamente la richiesta con un codice di stato HTTP 404, ovvero Non trovato.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Configurazione dell'autenticazione del certificato client tramite il proxy inverso
La terminazione SSL si verifica sul proxy inverso e si perdono tutti i dati del certificato client. Affinché i servizi eseguano l'autenticazione del certificato client, impostare l'impostazione **ForwardClientCertificate** nella sezione dei parametri dell'elemento ApplicationGateway/Http.

1. Quando **ForwardClientCertificate** è impostato su **false**, il proxy inverso non richiederà il certificato del client durante l'handshake SSL con il client.
Questo è il comportamento predefinito.

2. Quando **ForwardClientCertificate** è impostato su **true**, il proxy inverso richiede il certificato del client durante l'handshake SSL con il client.
I dati del certificato client verranno quindi inviati in un'intestazione HTTP personalizzata denominata **X-Client-Certificate**. Il valore dell'intestazione è la stringa in formato PEM con codifica base64 del certificato del client. Il servizio può eseguire correttamente o meno la richiesta con il codice di stato appropriato dopo aver esaminato i dati del certificato.
Se il client non presenta un certificato, il proxy inverso inoltra un'intestazione vuota e il caso viene gestito dal servizio.

> Il proxy inverso è un semplice server d'inoltro, pertanto non esegue alcuna convalida del certificato del client.


## <a name="next-steps"></a>Passaggi successivi
* Fare riferimento a [Configure reverse proxy to connect to secure services](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) (Configurare il proxy inverso per la connessione ai servizi protetti) per il modello di Azure Resource Manager per configurare il proxy inverso protetto con le diverse opzioni di convalida del certificato del servizio .
* Vedere un esempio di comunicazione HTTP tra i servizi in un [progetto di esempio in GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chiamate di procedura remota con i Reliable Services remoti](service-fabric-reliable-services-communication-remoting.md)
* [Web API che usa OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Gestire i certificati cluster](service-fabric-cluster-security-update-certs-azure.md)
