---
title: Definizione di Servizi cloud di Azure Schema NetworkTrafficRules | Microsoft Docs
ms.custom: 
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 37ec6c771ad83be680d95e9b5597d8da3cace9ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Schema NetworkTrafficRules di definizione di Servizi cloud di Azure
Il nodo `NetworkTrafficRules` è un elemento facoltativo nel file di definizione del servizio che specifica come i ruoli comunicano tra loro. Impone un limite ai ruoli che possono accedere agli endpoint interni del ruolo specifico. `NetworkTrafficRules` non è un elemento autonomo. Viene combinato con due o più ruoli Web nel file di definizione del servizio.

L'estensione predefinita per il file di definizione del servizio è csdef.

> [!NOTE]
>  Il nodo `NetworkTrafficRules` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Schema di definizione del servizio di base per le regole di traffico di rete
Il formato di base di un file di definizione del servizio contenente le definizioni del traffico di rete è il seguente.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementi dello schema
Il nodo `NetworkTrafficRules` del file di definizione del servizio include gli elementi descritti in dettaglio nelle sezioni successive di questo argomento:

[Elemento NetworkTrafficRules](#NetworkTrafficRules)

[Elemento OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Elemento Destinations](#Destinations)

[Elemento RoleEndpoint](#RoleEndpoint)

[Elemento AllowAllTraffic](#AllowAllTraffic)

[Elemento WhenSource](#WhenSource)

[Elemento FromRole](#FromRole)

##  <a name="NetworkTrafficRules"></a> Elemento NetworkTrafficRules
L'elemento `NetworkTrafficRules` specifica quali ruoli possono comunicare con quali endpoint in un altro ruolo. Un servizio può contenere una definizione `NetworkTrafficRules`.

##  <a name="OnlyAllowTrafficTo"></a> Elemento OnlyAllowTrafficTo
L'elemento `OnlyAllowTrafficTo` descrive una raccolta di endpoint di destinazione e i ruoli che vi possono comunicare. È possibile specificare più nodi `OnlyAllowTrafficTo`.

##  <a name="Destinations"></a> Elemento Destinations
L'elemento `Destinations` descrive una raccolta di RoleEndpoints con i quali è possibile comunicare.

##  <a name="RoleEndpoint"></a>Elemento RoleEndpoint
L'elemento `RoleEndpoint` descrive un endpoint in un ruolo per consentire le comunicazioni tra i due. È possibile specificare più elementi `RoleEndpoint` se sono presenti più di un endpoint nel ruolo.

| Attributo      | type     | DESCRIZIONE |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Richiesto. Il nome dell'endpoint verso il quale consentire il traffico.|
| `roleName`     | `string` | Richiesto. Il nome del ruolo Web con il quale consentire le comunicazioni.|

## <a name="allowalltraffic-element"></a>Elemento AllowAllTraffic
L'elemento `AllowAllTraffic` è una regola che consente a tutti i ruoli di comunicare con gli endpoint definiti nel nodo `Destinations`.

##  <a name="WhenSource"></a> Elemento WhenSource
L'elemento `WhenSource` descrive una raccolta di ruoli che possono comunicare con gli endpoint definiti nel nodo `Destinations`.

| Attributo | type     | DESCRIZIONE |
| --------- | -------- | ----------- |
| `matches` | `string` | Richiesto. Specifica la regola da applicare quando vengono consentite le comunicazioni. Al momento l'unico valore valido è `AnyRule`.|
  
##  <a name="FromRole"></a> Elemento FromRole
L'elemento `FromRole` specifica i ruoli che possono comunicare con gli endpoint definiti nel nodo `Destinations`. È possibile specificare più elementi `FromRole` se sono presenti più ruoli in grado di comunicare con gli endpoint.

| Attributo  | type     | DESCRIZIONE |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Richiesto. Il nome del ruolo da cui consentire la comunicazione.|

## <a name="see-also"></a>Vedere anche
[Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Schema di definizione di Servizi cloud - Versione classica)