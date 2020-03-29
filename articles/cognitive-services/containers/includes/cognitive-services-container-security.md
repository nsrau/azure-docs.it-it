---
title: Sicurezza del contenitore
titleSuffix: Azure Cognitive Services
description: Scopri come proteggere il tuo contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/30/2019
ms.author: dapine
ms.openlocfilehash: 20f78d9269d4b2270293c8746157ba495c694562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272944"
---
## <a name="azure-cognitive-services-container-security"></a>Sicurezza dei contenitori di Servizi cognitivi di AzureAzure Cognitive Services container security

La sicurezza deve essere un obiettivo primario ogni volta che si sviluppano applicazioni. L'importanza della sicurezza è una metrica per il successo. Quando si progetta una soluzione software che include contenitori di Servizi cognitivi, è fondamentale comprendere le limitazioni e le funzionalità disponibili. Per altre informazioni sulla sicurezza di rete, vedere Configurare le [reti virtuali di Servizi cognitivi][az-security]di Azure.For more information about network security, see Configure Azure Cognitive Services virtual networks .

> [!IMPORTANT]
> Per impostazione predefinita, non è *disponibile alcuna sicurezza* nell'API del contenitore Servizi cognitivi. La ragione di questo è che il più delle volte il contenitore verrà eseguito come parte di un pod che è protetto dall'esterno da un bridge di rete. Tuttavia, è possibile abilitare l'autenticazione che funziona in modo identico all'autenticazione utilizzata quando si accede ai [servizi cognitivi basati su cloud.][request-authentication]

Il diagramma seguente illustra l'approccio predefinito e non protetto:The diagram below illustrates the default and **non-secure** approach:

![Sicurezza del contenitore](../media/container-security.svg)

Come approccio alternativo e *sicuro,* gli consumer di contenitori di Servizi cognitivi potrebbero aumentare un contenitore con un componente frontale, mantenendo privato l'endpoint contenitore. Consideriamo uno scenario in cui usiamo [Istio][istio] come gateway in ingresso. Istio supporta HTTPS/TLS e l'autenticazione del certificato client. In questo scenario, il frontend Istio espone l'accesso al contenitore, presentando il certificato client che è whitelisted in anticipo con Istio.

[Nginx][nginx] è un'altra scelta popolare nella stessa categoria. Sia Istio che Nginx fungono da rete di servizio e offrono funzionalità aggiuntive, tra cui il bilanciamento del carico, il routing e il controllo delle tariffe.

### <a name="container-networking"></a>Rete del contenitore

I contenitori di Servizi cognitivi sono necessari per inviare informazioni di misurazione a scopo di fatturazione. L'unica eccezione è *che i contenitori non in linea* seguono una metodologia di fatturazione diversa. Se non si consente l'elenco di vari canali di rete su cui si basano i contenitori di Servizi cognitivi, il contenitore non funziona.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Accesso consenti ai domini e alle porte di Servizi cognitivi

L'host deve consentire l'elenco **della porta 443** e dei seguenti domini:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Disabilitare l'ispezione approfondita dei pacchetti

> [L'ispezione completa dei pacchetti](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI, Deep Packet Inspection) è un tipo di elaborazione dei dati che controlla in dettaglio i dati inviati tramite una rete di computer e in genere interviene bloccando, instradandoli nuovamente o registrandoli di conseguenza.

Disabilitare DPI nei canali protetti creati dai contenitori di Servizi cognitivi nei server Microsoft. In caso contrario, il contenitore non funzionerà correttamente.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
