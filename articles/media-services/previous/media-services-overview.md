---
title: Panoramica di Servizi multimediali di Azure | Microsoft Docs
description: Servizi multimediali di Microsoft Azure costituisce una piattaforma estensibile basata sul cloud che consente agli sviluppatori di creare applicazioni di distribuzione e gestione di contenuti multimediali altamente scalabili. Questo articolo offre una panoramica di Servizi multimediali di Azure.This article gives an overview of Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: juliako
ms.openlocfilehash: 1c2d6287a89c7816c30cf26978859c07dba0251d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197505"
---
# <a name="azure-media-services-overview"></a>Panoramica di Servizi multimediali di Azure 

> [!div class="op_single_selector" title1="Selezionare la versione di Servizi multimediali in uso:"]
> * [Versione 3](../latest/media-services-overview.md)
> * [Versione 2](media-services-overview.md)

> [!NOTE]
> Non saranno aggiunte nuove funzionalità a Servizi multimediali v2. <br/>Scopri la versione più recente, [Servizi multimediali v3](https://docs.microsoft.com/azure/media-services/latest/). Vedere anche le linee guida per la [migrazione dalla v2 alla v3](../latest/migrate-from-v2-to-v3.md)

Servizi multimediali di Microsoft Azure è una piattaforma estensibile basata sul cloud che consente agli sviluppatori di creare applicazioni scalabili per la distribuzione e gestione di contenuti multimediali. Servizi multimediali si basa su API REST che consentono di caricare, archiviare e codificare contenuti video o audio in modo sicuro, nonché creare pacchetti di tali contenuti per la distribuzione in streaming live e on demand a vari client (ad esempio, TV, PC e dispositivi mobili).

È possibile creare flussi di lavoro end-to-end usando unicamente Servizi multimediali. È anche possibile usare componenti di terze parti per alcune parti del flusso di lavoro, ad esempio, la codifica con un codificatore di terze parti. Inoltre, sono possibili operazioni di caricamento, protezione, creazione di pacchetti e invio tramite Servizi multimediali. È possibile optare per lo streaming dei contenuti live o on demand. 


## <a name="compliance-privacy-and-security"></a>Conformità, privacy e sicurezza

Come promemoria importante, è necessario rispettare tutte le leggi applicabili nell'uso di Servizi multimediali di Azure e non è possibile usare Servizi multimediali o qualsiasi servizio di Azure in modo da violare i diritti di altri utenti o che potrebbe essere dannoso per altri utenti.

Prima di caricare qualsiasi video/immagine in Servizi multimediali, è necessario disporre di tutti i diritti appropriati per utilizzare il video/immagine, inclusi, se richiesto dalla legge, tutti i consensi necessari da parte di individui (se presenti) nel video/immagine, per l'uso, l'elaborazione e l'archiviazione dei dati in Servizi multimediali e Azure. Alcune giurisdizioni possono imporre particolari requisiti legali per la raccolta, l'elaborazione online e l'archiviazione di determinate categorie di dati, come i dati biometrici. Prima di usare Servizi multimediali e Azure per l'elaborazione e l'archiviazione di dati soggetti a requisiti legali speciali, è necessario garantire la conformità a tali requisiti legali applicabili all'utente.

Per informazioni su conformità, privacy e sicurezza in Servizi multimediali, visitare il [Centro protezione](https://www.microsoft.com/trust-center/?rtc=1)Microsoft . Per gli obblighi di Microsoft in materia di privacy, la gestione e le procedure di conservazione dei dati, incluse le modalità di eliminazione dei dati, consultare [l'Informativa sulla privacy](https://privacy.microsoft.com/PrivacyStatement)di Microsoft, le [Condizioni](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) per i servizi online ("OST") e [l'Aggiunta del trattamento dei dati](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Utilizzando Servizi multimediali, l'utente accetta di essere vincolato dall'OST, dal DPA e dall'Informativa sulla privacy.
 
## <a name="prerequisites"></a>Prerequisiti

Per iniziare a utilizzare Servizi multimediali di Azure, è necessario disporre di quanto segue:

* Un account Azure. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere Versione di valutazione gratuita di Azure .For [details,](https://azure.microsoft.com)see Azure Free Trial .
* Un account di Servizi multimediali di Azure. Per altre informazioni, vedere [Creare un account](media-services-portal-create-account.md).
* (Facoltativo) Configurare l'ambiente di sviluppo. Scegliere .NET o API REST per l'ambiente di sviluppo. Per altre informazioni, vedere [Configurare l'ambiente](media-services-dotnet-how-to-use.md).

    Sono anche disponibili informazioni su come [connettersi all'API AMS a livello di codice](media-services-use-aad-auth-to-access-ams-api.md).
* Un endpoint di streaming Standard o Premium con stato avviato.  Per altre informazioni, vedere [Gestione degli endpoint di streaming](media-services-portal-manage-streaming-endpoints.md).

## <a name="sdks-and-tools"></a>SDK e strumenti

Per creare soluzioni di Servizi multimediali, è possibile usare:

* [API REST di Servizi multimediali](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Uno dei client SDK disponibili:
    * SDK di Servizi multimediali di Azure per .NETAzure Media Services SDK for .NET
    
        * [Pacchetto NuGet](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [Codice sorgente GitHub](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK per Java](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Servizi multimediali di Azure per Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js). Questa è una versione non Microsoft di Node.js SDK. È gestita da una community e attualmente non copre al 100% le API AMS.
* Strumenti esistenti:
    * [Portale di Azure](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) è un'applicazione Winforms/C# per Windows)

> [!NOTE]
> Per ottenere la versione più recente di Java SDK e iniziare a sviluppare con Java, vedere [Introduzione a Java Client SDK per Servizi multimediali](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Per scaricare la versione più recente di PHP SDK per Servizi multimediali, cercare la versione 0.5.7 del pacchetto Microsoft/WindowAzure nel [repository Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Esempi di codice

Gli esempi di codice sono disponibili nella raccolta di **Esempi di codice per Azure**: [Esempi di codice per Servizi multimediali di Azure](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Concetti

Per i concetti relativi ai Servizi multimediali di Azure, vedere [Concetti su Servizi multimediali di Azure](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Scenari supportati e disponibilità di Servizi multimediali nei data center

Per informazioni dettagliate, vedere [Scenari supportati e disponibilità di Servizi multimediali nei data center](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Contratto di servizio (SLA)

Per altre informazioni, vedere [Contratto di servizio di Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

Per informazioni sulla disponibilità nei data center, vedere la sezione relativa alla [disponibilità](scenarios-and-availability.md#availability).

## <a name="support"></a>Supporto

[supporto tecnico di Azure](https://azure.microsoft.com/support/options/) fornisce opzioni di supporto per Azure, compreso Servizi multimediali.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
