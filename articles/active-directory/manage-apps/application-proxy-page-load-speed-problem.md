---
title: Il caricamento dell'applicazione del proxy di applicazione impiega troppo tempo | Microsoft Docs
description: Risoluzione dei problemi delle prestazioni di caricamento della pagina con il proxy di applicazione di Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 0c18647fbfc5e328276eef248f4b6aa1dc7f48a2
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44355550"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Il caricamento dell'applicazione del proxy di applicazione impiega troppo tempo

Questo articolo aiuta a comprendere perché il caricamento di Azure Active Directory Application Proxy potrebbe impiegare troppo tempo. Viene inoltre spiegato come è possibile risolvere questo problema.

## <a name="overview"></a>Panoramica
Sebbene le applicazioni funzionino, può verificarsi una latenza prolungata. Potrebbero esserci modifiche che è possibile apportare alla topologia di rete per migliorare la velocità. Per una valutazione delle diverse topologie, consultare il [documento relativo alle considerazioni sulla rete](application-proxy-network-topology.md).

Oltre alla topologia di rete, non esistono attualmente altri consigli per ottimizzare le prestazioni. Man mano che il servizio proxy di applicazione si espande potrebbe arrivare a un data center fisicamente più vicino. La maggiore prossimità potrebbe migliorare la latenza. Per un elenco dei data center di Azure, vedere la [pagina di prova della latenza](http://www.azurespeed.com/Azure/Latency). 

I data center con il servizio proxy di applicazione sono disponibili con lo [strumento per il test delle porte del connettore](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Commenti sulle posizioni dei data center del proxy di applicazione 
Potrebbero essere presenti data center di Azure che non includono ancora il proxy di applicazione e, di conseguenza, potrebbero migliorare parecchio la latenza. Inviare la posizione del data center a aadapfeedback@microsoft.com. Microsoft usa i commenti per i piani di espansione.

Microsoft sta lavorando a funzionalità aggiuntive per migliorare latenza. Non appena tutti questi miglioramenti saranno disponibili, la documentazione verrà aggiornata.

## <a name="next-steps"></a>Passaggi successivi
[Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
