---
title: Sicurezza del contenitore
titleSuffix: Azure Cognitive Services
description: Informazioni su come proteggere il contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: 0e9fa9146292bf7dabbbf06d3bb436aa6cd2e6e2
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71124083"
---
## <a name="azure-cognitive-services-container-security"></a>Sicurezza del contenitore dei servizi cognitivi di Azure

La protezione deve essere un obiettivo principale quando si sviluppano applicazioni. L'importanza della sicurezza è una metrica per il successo. Quando si progetta una soluzione software che include contenitori di servizi cognitivi, è fondamentale comprendere le limitazioni e le funzionalità disponibili. Per altre informazioni, vedere [sicurezza di Azure][az-security].

> [!IMPORTANT]
> Per impostazione predefinita, non esiste *alcuna sicurezza* sull'API del contenitore di servizi cognitivi. Il motivo è che la maggior parte delle volte il contenitore verrà eseguito come parte di un pod protetto dall'esterno da un Bridge di rete. Tuttavia, è possibile abilitare l'autenticazione che funziona in modo identico all'autenticazione usata quando si accede ai [Servizi cognitivi basati sul cloud][request-authentication].

Il diagramma seguente illustra l'approccio predefinito e non **sicuro** :

![Sicurezza del contenitore](../media/container-security.svg)

Come approccio alternativo e *sicuro* , i consumer di contenitori di servizi cognitivi possono aumentare un contenitore con un componente front-end, mantenendo privato l'endpoint contenitore. Si prenda in considerazione uno scenario in cui si usa [Istio][istio] come gateway di ingresso. Istio supporta HTTPS/SSL e l'autenticazione del certificato client. In questo scenario, il front-end Istio espone l'accesso al contenitore, presentando il certificato client inserito nell'elenco elementi consentiti in anticipo con Istio.

[Nginx][nginx] è un'altra scelta comune nella stessa categoria. Sia Istio che Nginx fungono da mesh del servizio e offrono funzionalità aggiuntive, tra cui bilanciamento del carico, routing e controllo delle tariffe.

### <a name="container-networking"></a>Rete del contenitore

I contenitori di servizi cognitivi sono necessari per inviare le informazioni di misurazione per scopi di fatturazione. L'unica eccezione è che i contenitori non in *linea* seguono una metodologia di fatturazione diversa. Se non è possibile consentire l'elenco di diversi canali di rete su cui si basano i contenitori di servizi cognitivi, il contenitore non funzionerà.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Consenti elenco di domini e porte di servizi cognitivi

L'host deve consentire la **porta elenco 443** e i domini seguenti:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Disabilitare l'ispezione approfondita dei pacchetti

> [Ispezione approfondita dei pacchetti](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) è un tipo di elaborazione dei dati che esamina in dettaglio i dati inviati su una rete di computer e in genere interviene, bloccando, reindirizzando o registrando di conseguenza.

Disabilitare DPI nei canali protetti creati dai contenitori di servizi cognitivi nei server Microsoft. In caso contrario, il contenitore non funzionerà correttamente.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
