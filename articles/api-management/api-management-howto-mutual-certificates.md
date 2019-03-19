---
title: Proteggere i servizi back-end usando l'autenticazione con certificati client - Gestione API di Azure | Documentazione Microsoft
description: Come proteggere i servizi back-end usando l'autenticazione con certificati client in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 13a2eb080c6822a8a6786be1952bc588fa8afd80
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729210"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Come proteggere i servizi back-end usando l'autenticazione con certificati client in Gestione API di Azure

Gestione API consente di proteggere l'accesso al servizio back-end di un'API usando i certificati client. Questa guida illustra come gestire i certificati nell'istanza del servizio Gestione API di Azure nel portale di Azure. Viene inoltre spiegato come configurare un'API per usare un certificato per accedere a un servizio back-end.

Per informazioni sulla gestione dei certificati con l'API REST di Gestione API, vedere <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Entità certificato dell'API REST di Gestione API di Azure</a>.

## <a name="prerequisites"> </a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In questa guida viene illustrato come configurare un'istanza del servizio di Gestione API per l'uso dell'autenticazione con certificati client per accedere al servizio back-end di un'API. Prima di seguire la procedura descritta in questo articolo, è necessario che il servizio back-end sia configurato per l'autenticazione del certificato client ([per configurare l'autenticazione del certificato in siti Web di Azure fare riferimento a questo articolo][to configure certificate authentication in Azure WebSites refer to this article]). È necessario accedere al certificato e alla password per caricarlo nel servizio Gestione API.

## <a name="step1"></a>Caricare un certificato client

![Aggiungere certificati client](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Seguire questa procedura per caricare un nuovo certificato client. Se non è ancora stata creata un'istanza del servizio Gestione API, vedere l'esercitazione [Creare un'istanza di Gestione API][Create an API Management service instance].

1. Passare all'istanza del servizio Gestione API di Azure nel portale di Azure.
2. Scegliere **Certificati client** dal menu.
3. Fare clic sul pulsante **+ Aggiungi**.  
    ![Aggiungere certificati client](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Selezionare il certificato, quindi specificarne ID e password.  
5. Fare clic su **Create**(Crea).

> [!NOTE]
> Il certificato deve essere nel formato **.pfx** . Sono consentiti i certificati autofirmati.

Dopo il caricamento, il certificato viene visualizzato nella scheda **Certificati client**.  Se sono disponibili molti certificati, prendere nota dell'identificazione digitale del certificato desiderato per [configurare un'API per l'uso di un certificato client per l'autenticazione del gateway][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Per disattivare la convalida della catena di certificati quando si usa, ad esempio, un certificato autofirmato, seguire i passaggi descritti in questa [voce](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end) delle Domande frequenti.

## <a name="step1a"></a>Eliminare un certificato client

Per eliminare un certificato, fare clic sul menu di scelta rapida **...** e selezionare **Elimina** accanto al certificato.

![Eliminare certificati client](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Se il certificato è in uso da parte di un'API, verrà visualizzata una schermata di avviso. Per eliminare il certificato, è prima necessario rimuoverlo da tutte le API configurate per il suo uso.

![Errore di eliminazione dei certificati client](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"></a>Configurare un'API per l'uso di un certificato client per l'autenticazione gateway

1. Fare clic su **API** dal menu **Gestione API** sulla sinistra e passare all'API.  
    ![Abilitare i certificati client](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Nella scheda **Progettazione** fare clic sull'icona a forma di matita nella sezione **Back-end**. 
3. Modificare **Credenziali gateway** impostando **Certificato client** e selezionare il certificato nell'elenco a discesa.  
    ![Abilitare i certificati client](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Fare clic su **Save**. 

> [!WARNING]
> Questa modifica ha effetto immediato e le chiamate alle operazioni di quell'API useranno il certificato per autenticarsi sul server back-end.


> [!TIP]
> Quando un certificato è specificato per l'autenticazione gateway del servizio back-end di un'API, diventa parte dei criteri di quell'API e può essere visualizzato nell'editor dei criteri.

## <a name="self-signed-certificates"></a>Certificati autofirmati

Se si usano i certificati autofirmati, è necessario disabilitare la convalida della catena di certificati affinché il servizio Gestione API possa comunicare con il sistema back-end. In caso contrario, verrà restituito un codice di Errore 500. Per la configurazione, usare i cmdlet PowerShell [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (per il nuovo back-end) o [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (per il back-end esistente) e impostare il parametro `-SkipCertificateChainValidation` su `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
