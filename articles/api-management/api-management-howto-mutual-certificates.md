---
title: Come proteggere i servizi back-end usando l&quot;autenticazione con certificati client in Gestione API di Azure
description: Come proteggere i servizi back-end usando l&quot;autenticazione con certificati client in Gestione API di Azure.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 43453331-39b2-4672-80b8-0a87e4fde3c6
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ad14ed8b36d6d0a2121c32fd9a54de97e8b02342


---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Come proteggere i servizi back-end usando l'autenticazione con certificati client in Gestione API di Azure
Gestione API offre la possibilità di proteggere l'accesso al servizio back-end di un'API usando i certificati client. Questa guida illustra come gestire i certificati nel portale di pubblicazione delle API e come configurare un'API per l'uso di un certificato per accedere al servizio back-end.

Per informazioni sulla gestione dei certificati mediante l'API REST di Gestione API, vedere [Entità certificato dell'API REST di Gestione API di Azure][Entità certificato dell'API REST di Gestione API di Azure].

## <a name="prerequisites"> </a>Prerequisiti
In questa guida viene illustrato come configurare un'istanza del servizio di Gestione API per l'uso dell'autenticazione con certificati client per accedere al servizio back-end di un'API. Prima di seguire la procedura indicata in questo argomento è necessario aver configurato il servizio back-end per l'autenticazione del certificato client ([per la configurazione dell'autenticazione del certificato client nei siti Web di Azure][per la configurazione dell'autenticazione del certificato client nei siti Web di Azure]) e disporre dell'accesso al certificato e alla relativa password per il caricamento nel portale di pubblicazione di Gestione API.

## <a name="step1"> </a>Caricare un certificato client
Per iniziare, fare clic sul **portale di pubblicazione** nel Portale di Azure relativo al servizio Gestione API. Verrà visualizzato il portale di pubblicazione di Gestione API.

![Portale di pubblicazione delle API][api-management-management-console]

> Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Creare un'istanza di Gestione API] nell'esercitazione [Introduzione a Gestione API di Azure][Introduzione a Gestione API di Azure].
> 
> 

Fare clic su **Sicurezza** dal menu **Gestione API** a sinistra, quindi scegliere **Certificati client**.

![Certificati client][api-management-security-client-certificates]

Per caricare un nuovo certificato, fare clic su **Carica certificato**.

![Carica certificato][api-management-upload-certificate]

Passare al certificato e immettere la relativa password.

> Il certificato deve essere nel formato **.pfx** . Sono consentiti i certificati autofirmati.
> 
> 

![Carica certificato][api-management-upload-certificate-form]

Fare clic su **Carica** per caricare il certificato.

> A questo punto la password del certificato viene convalidata. Se non è corretta, viene visualizzato un messaggio di errore.
> 
> 

![Certificato caricato][api-management-certificate-uploaded]

Una volta caricato il certificato, questo viene visualizzato nella scheda **Certificati client** . Se si hanno più certificati, prendere nota dell'oggetto o degli ultimi quattro caratteri dell'identificazione personale, che vengono usati per selezionare il certificato quando si configura un'API per l'uso dei certificati, come illustrato nella sezione [Configurare un'API per l'uso di un certificato client per l'autenticazione gateway][Configurare un'API per l'uso di un certificato client per l'autenticazione gateway] che segue.

> Per disattivare la convalida della catena di certificati quando si usa, ad esempio, un certificato autofirmato, seguire i passaggi descritti in questa [voce](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end) delle Domande frequenti.
> 
> 

## <a name="step1a"> </a>Eliminare un certificato client
Per eliminare un certificato, fare clic su **Elimina** accanto al certificato desiderato.

![Eliminazione di un certificato][api-management-certificate-delete]

Fare clic su **Sì, elimina** per confermare.

![Conferma dell'eliminazione][api-management-confirm-delete]

Se il certificato è in uso da parte di un'API, verrà visualizzata una schermata di avviso. Per eliminare il certificato è necessario prima rimuoverlo da tutte le API configurate per il suo uso.

![Conferma dell'eliminazione][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurare un'API per l'uso di un certificato client per l'autenticazione gateway
Fare clic su **API** dal menu **Gestione API** a sinistra, fare clic sul nome dell'API desiderata, quindi sulla scheda **Sicurezza**.

![Sicurezza API][api-management-api-security]

Selezionare **Certificati client** dall'elenco a discesa **Con credenziali**.

![Certificati client][api-management-mutual-certificates]

Selezionare il certificato desiderato dall'elenco a discesa **Certificato client** . Se esistono diversi certificati, fare riferimento all'oggetto o agli ultimi quattro caratteri dell'identificazione personale, come spiegato nella sezione precedente, per determinare il certificato corretto.

![Selezione del certificato][api-management-select-certificate]

Fare clic su **Salva** per salvare la modifica di configurazione nell'API.

> Questa modifica ha effetto immediato e le chiamate alle operazioni di quell'API useranno il certificato per autenticarsi sul server back-end.
> 
> 

![Salvataggio delle modifiche API][api-management-save-api]

> Quando un certificato è specificato per l'autenticazione gateway del servizio back-end di un'API, diventa parte dei criteri di quell'API e può essere visualizzato nell'editor dei criteri.
> 
> 

![Criteri dei certificati][api-management-certificate-policy]

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su altri modi per proteggere il servizio back-end, ad esempio autenticazione HTTP di base o segreto condiviso, vedere il video seguente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Last-mile-Security/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[Come aggiungere operazioni a un'API in Gestione API di Azure]: api-management-howto-add-operations.md
[Come aggiungere e pubblicare un prodotto]: api-management-howto-add-products.md
[Monitoraggio e analisi]: ../api-management-monitoring.md
[Aggiungere API a un prodotto]: api-management-howto-add-products.md#add-apis
[Pubblicare un prodotto]: api-management-howto-add-products.md#publish-product
[Introduzione a Gestione API di Azure]: api-management-get-started.md
[Informazioni di riferimento per i criteri di Gestione API di Azure]: api-management-policy-reference.md
[Criteri di memorizzazione nella cache]: api-management-policy-reference.md#caching-policies
[Creare un'istanza di Gestione API]: api-management-get-started.md#create-service-instance

[Entità certificato dell'API REST di Gestione API di Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[per la configurazione dell'autenticazione del certificato client nei siti Web di Azure]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisiti]: #prerequisites
[Caricare un certificato client]: #step1
[Eliminare un certificato client]: #step1a
[Configurare un'API per l'uso di un certificato client per l'autenticazione gateway]: #step2
[Testare la configurazione chiamando un'operazione nel portale per sviluppatori]: #step3
[Passaggi successivi]: #next-steps






<!--HONumber=Nov16_HO3-->


