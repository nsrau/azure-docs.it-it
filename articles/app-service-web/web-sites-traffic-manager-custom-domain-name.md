---
title: Configurare un nome di dominio personalizzato per un'app Web nel servizio app di Azure che usa Gestione traffico per il bilanciamento del carico.
description: Usare un nome di dominio personalizzato per un'app Web nel servizio app di Azure che usa Gestione traffico per il bilanciamento del carico.
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 532a953c02ef40eb55705eac885d74c5574deed5
ms.contentlocale: it-it
ms.lasthandoff: 01/05/2017

---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurazione di un nome di dominio personalizzato per un'app Web nel servizio app di Azure con Gestione traffico
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato con il servizio app di Azure in cui viene usato Gestione traffico per il bilanciamento del carico.

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
> Se si è acquistato un dominio tramite App Web del servizio app di Azure, ignorare i passaggi seguenti e fare riferimento all'ultimo passaggio dell'articolo [Acquistare un dominio per app Web](custom-dns-web-site-buydomains-web-app.md) .
> 
> 

Per associare il dominio personalizzato a un'app Web nel servizio app di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti dal registrar da cui è stato acquistato il nome di dominio. Per individuare e utilizzare gli strumenti DNS, attenersi alla procedura seguente.

1. Accedere all'account presso il registrar e cercare la pagina in cui gestire i record DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**. Un collegamento a questa pagina è spesso disponibile nelle informazioni dell'account, cercando una voce simile a **My domains**.
2. Dopo aver trovato la pagina di gestione del nome di dominio, cercare un collegamento che consenta di modificare i record DNS. Questo collegamento può essere denominato **Zone file** o **DNS Records** oppure figurare come collegamento di configurazione in **Advanced**.
   
   * La pagina conterrà molto probabilmente alcuni record già creati, ad esempio una voce per associare '**@**' o '\*' a una pagina di registrazione semplice. Può anche contenere record per sottodomini comuni, ad esempio **www**.
   * Nella pagina saranno presenti voci per record **CNAME**oppure verrà visualizzato un elenco a discesa per selezionare il tipo di record. È anche possibile che siano presenti voci per altri record, ad esempio **record A** e **record MX**. In alcuni casi, i record CNAME avranno una denominazione diversa, come ad esempio nel caso dei record **Alias**.
   * La pagina conterrà inoltre campi che consentono di eseguire il **mapping** da un **nome host** o da un **nome di dominio** a un altro nome di dominio.
3. Anche se le specifiche di ogni registrar possono variare, in genere viene eseguito il mapping *dal* nome di dominio personalizzato, ad esempio **contoso.com**, *al* nome di dominio di Gestione traffico (**contoso.trafficmanager.net**) usato per l'app Web.
   
   > [!NOTE]
   > In alternativa, se un record è già in uso ed è necessario associare le app in modalità preemptive, è possibile creare un altro record CNAME. Ad esempio, per associare **www.contoso.com** all'app Web in modalità preemptive, creare un record CNAME da **awverify.www** a **contoso.trafficmanager.net**. Aggiungere quindi "www.contoso.com" all'app Web senza modificare il record CNAME "www". Per altre informazioni, vedere [Creare record DNS per un'app Web in un dominio personalizzato][CREATEDNS].
   > 
   > 
4. Dopo aver completato l'aggiunta o la modifica di record DNS presso il registrar, salvare le modifiche.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Abilitare Gestione traffico
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere il [Centro per sviluppatori di Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md

