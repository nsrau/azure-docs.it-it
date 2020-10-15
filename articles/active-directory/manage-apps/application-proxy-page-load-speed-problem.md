---
title: Il caricamento dell'applicazione del proxy di applicazione impiega troppo tempo | Microsoft Docs
description: Risoluzione dei problemi delle prestazioni di caricamento della pagina con il proxy di applicazione di Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1dc1b14a917786e124775a4505c53f63238a383
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86203855"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Il caricamento dell'applicazione del proxy di applicazione impiega troppo tempo

Questo articolo aiuta a comprendere perché il caricamento di Azure Active Directory Application Proxy potrebbe impiegare troppo tempo. Viene inoltre spiegato come è possibile risolvere questo problema.

## <a name="overview"></a>Panoramica
Sebbene le applicazioni funzionino, può verificarsi una latenza prolungata. Potrebbero esserci modifiche che è possibile apportare alla topologia di rete per migliorare la velocità. Per una valutazione delle diverse topologie, consultare il [documento relativo alle considerazioni sulla rete](application-proxy-network-topology.md).

Oltre alla topologia di rete, non esistono attualmente altri consigli per ottimizzare le prestazioni. Man mano che il servizio proxy di applicazione si espande potrebbe arrivare a un data center fisicamente più vicino. La maggiore prossimità potrebbe migliorare la latenza. Per un elenco dei data center di Azure, vedere la [pagina di prova della latenza](http://www.azurespeed.com/Azure/Latency). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Commenti sulle posizioni dei data center del proxy di applicazione 
Potrebbero essere presenti data center di Azure che non includono ancora il proxy di applicazione e, di conseguenza, potrebbero migliorare parecchio la latenza. Inviare la posizione del data center a aadapfeedback@microsoft.com. Microsoft usa i commenti per i piani di espansione.

Microsoft sta lavorando a funzionalità aggiuntive per migliorare latenza. Non appena tutti questi miglioramenti saranno disponibili, la documentazione verrà aggiornata.

## <a name="next-steps"></a>Passaggi successivi
[Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
