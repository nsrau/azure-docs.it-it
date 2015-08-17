<properties 
	pageTitle="Come proteggere i servizi back-end usando l'autenticazione reciproca dei certificati in Gestione API di Azure" 
	description="Informazioni su come proteggere i servizi back-end tramite l'autenticazione reciproca dei certificati in Gestione API di Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Come proteggere i servizi back-end usando l'autenticazione reciproca dei certificati in Gestione API di Azure

Gestione API offre la possibilità di proteggere l'accesso al servizio back-end di un'API usando i certificati reciproci. Questa guida illustra come gestire i certificati nel portale di pubblicazione delle API e come configurare un'API per l'uso di un certificato per accedere al servizio back-end.

Per informazioni sulla gestione dei certificati con l'API REST di Gestione API, vedere [Entità certificato dell'API REST di Gestione API di Azure][].

## <a name="prerequisites"> </a>Prerequisiti

In questa guida viene illustrato come configurare un'istanza del servizio di Gestione API per l'uso dell'autenticazione reciproca dei certificati per accedere al servizio back-end di un'API. Prima di eseguire le procedure di questo argomento, è necessario aver configurato il servizio back-end per l'autenticazione reciproca dei certificati e avere l'accesso al certificato e alla relativa password per il caricamento nel portale di pubblicazione di Gestione API.

## <a name="step1"> </a>Caricare un certificato client

Per iniziare, fare clic su **Gestisci** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale di pubblicazione di Gestione API.

![Portale di pubblicazione delle API][api-management-management-console]

>Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][] nell'esercitazione [Introduzione a Gestione API di Azure][].

Fare clic su **Sicurezza** dal menu **Gestione API** sulla sinistra, quindi scegliere **Certificati client**.

![Certificati client][api-management-security-client-certificates]

Per caricare un nuovo certificato, fare clic su **Carica certificato**.

![Caricamento del certificato][api-management-upload-certificate]

Passare al certificato e immettere la relativa password.

>Il certificato deve essere nel formato **.pfx**. Sono consentiti i certificati autofirmati.

![Caricamento del certificato][api-management-upload-certificate-form]

Fare clic su **Carica** per caricare il certificato.

>A questo punto la password del certificato viene convalidata. Se non è corretta, viene visualizzato un messaggio di errore.

![Certificato caricato][api-management-certificate-uploaded]

Una volta caricato il certificato, questo viene visualizzato nella scheda **Certificati client**. Se si hanno più certificati, prendere nota dell'oggetto o degli ultimi quattro caratteri dell'identificazione personale, che vengono usati per selezionare il certificato quando si configura un'API per l'uso dei certificati, come illustrato nella sezione [Configurare un'API per l'uso di un certificato reciproco per l'autenticazione proxy][] che segue.

## <a name="step1a"> </a>Eliminare un certificato client

Per eliminare un certificato, fare clic su **Elimina** accanto al certificato desiderato.

![Eliminazione di un certificato][api-management-certificate-delete]

Fare clic su **Sì, elimina** per confermare.

![Conferma dell'eliminazione][api-management-confirm-delete]

Se il certificato è in uso da parte di un'API, verrà visualizzata una schermata di avviso. Per eliminare il certificato è necessario prima rimuoverlo da tutte le API configurate per il suo uso.

![Conferma dell'eliminazione][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurare un'API per l'uso di un certificato reciproco per l'autenticazione proxy

Fare clic su **API** dal menu **Gestione API** sulla sinistra, fare clic sul nome dell'API desiderata, quindi sulla scheda **Sicurezza**.

![Sicurezza API][api-management-api-security]

Selezionare **Mutual certificates** dall'elenco a discesa **With credentials**.

![Certificati reciproci][api-management-mutual-certificates]

Selezionare il certificato desiderato dall'elenco a discesa **Certificato client**. Se esistono diversi certificati, fare riferimento all'oggetto o agli ultimi quattro caratteri dell'identificazione personale, come spiegato nella sezione precedente, per determinare il certificato corretto.

![Selezione del certificato][api-management-select-certificate]

Fare clic su **Salva** per salvare la modifica di configurazione nell'API.

>Questa modifica ha effetto immediato e le chiamate alle operazioni di quell'API useranno il certificato per autenticarsi sul server back-end.

![Salvataggio delle modifiche API][api-management-save-api]

>Quando un certificato è specificato per l'autenticazione proxy del servizio back-end di un'API, diventa parte dei criteri di quell'API e può essere visualizzato nell'editor dei criteri.

![Criteri dei certificati][api-management-certificate-policy]

## Passaggi successivi

Per altre informazioni, vedere il video seguente:

> [AZURE.VIDEO last-mile-security]

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



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introduzione a Gestione API di Azure]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Creare un'istanza del servizio Gestione API]: api-management-get-started.md#create-service-instance

[Entità certificato dell'API REST di Gestione API di Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configurare un'API per l'uso di un certificato reciproco per l'autenticazione proxy]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



 

<!---HONumber=August15_HO6-->