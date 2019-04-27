---
title: Configurare i nomi DNS per le app che usano Gestione traffico - Servizio app di Azure
description: Usare un nome di dominio personalizzato per un'app Web nel servizio app di Azure che usa Gestione traffico per il bilanciamento del carico.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b207d9e3123245e5cb7dddb625f7488886591ae5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128536"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurazione di un nome di dominio personalizzato per un'app Web nel servizio app di Azure con Gestione traffico
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato con un'app del [servizio app](overview.md) integrata con [Gestione traffico](../traffic-manager/traffic-manager-overview.md) per il bilanciamento del carico.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Informazioni sui record DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Configurare le app Web per la modalità standard
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Aggiunta di un record DNS per il dominio personalizzato
> [!NOTE]
> Se si è acquistato un dominio tramite App Web del servizio app di Azure, ignorare i passaggi seguenti e fare riferimento all'ultimo passaggio dell'articolo [Acquistare un dominio per app Web](manage-custom-dns-buy-domain.md) .
> 
> 

Per associare il dominio personalizzato a un'app Web nel servizio app di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato. A tale scopo, vengono usati gli strumenti di gestione del provider di dominio.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Anche se le specifiche di ogni provider di dominio possono variare, in genere viene eseguito il mapping *dal* nome di dominio personalizzato, ad esempio **contoso.com**, *al* nome di dominio di Gestione traffico (**contoso.trafficmanager.net**) integrato con l'app Web.

> [!NOTE]
> Se un record è già in uso ed è necessario associare le app in modalità preemptive, è possibile creare un altro record CNAME. Ad esempio, per associare preventivamente **www\.contoso.com** alla tua app web, creare un record CNAME da **awverify. www** alla **contoso.trafficmanager.net**. È quindi possibile aggiungere "www\.contoso.com" per l'App Web senza modificare il record CNAME "www". Per altre informazioni, vedere [Creare record DNS per un'app Web in un dominio personalizzato][CREATEDNS].

Dopo aver completato l'aggiunta o la modifica di record DNS presso il provider di dominio, salvare le modifiche.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Abilitare Gestione traffico
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere il [Centro per sviluppatori di Node.js](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
