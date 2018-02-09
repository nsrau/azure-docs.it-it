---
title: Introduzione alla rete CDN di Azure | Documentazione Microsoft
description: Questo argomento illustra come abilitare la rete per la distribuzione di contenuti (CDN) di Azure. Questa esercitazione illustra la creazione di un nuovo profilo ed endpoint della rete CDN.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2018
ms.author: mazha
ms.openlocfilehash: 81a88f6495ca9092ca3b55b8ffb3e41def3b4623
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="getting-started-with-azure-cdn"></a>Introduzione alla rete CDN di Azure
Questo articolo illustra come abilitare la rete per la distribuzione di contenuti (CDN) di Azure creando un nuovo profilo e un nuovo endpoint di rete CDN.

> [!IMPORTANT]
> Per un'introduzione alla rete CDN e un elenco delle funzionalità, vedere la [panoramica della rete CDN](cdn-overview.md).
> 
> 

## <a name="create-a-new-cdn-profile"></a>Creare un nuovo profilo di rete CDN
Un profilo di rete CDN è una raccolta di endpoint della rete CDN. Ogni profilo contiene uno o più endpoint di rete CDN. Per organizzare gli endpoint di rete CDN per dominio Internet, applicazione Web o altri criteri, è possibile usare più profili.

> [!NOTE]
> Una sottoscrizione di Azure ha limiti predefiniti per le risorse seguenti:
> - Numero di profili CDN che possono essere creati
> - Numero di endpoint che possono essere creati in un profilo CDN 
> - Numero di domini personalizzati di cui è possibile eseguire il mapping a un endpoint
>
> Per informazioni sui limiti delle sottoscrizioni CDN, vedere [Limiti relativi alla rete CDN](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits).
>
> I prezzi della rete CDN vengono applicati a livello di profilo della rete CDN. Per usare una combinazione di piani tariffari della rete CDN di Azure, è quindi necessario creare più profili di rete CDN.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Creare un nuovo endpoint della rete CDN
**Per creare nuovo endpoint della rete CDN**

1. Nel [portale di Azure](https://portal.azure.com)passare al profilo della rete CDN. Lo si potrebbe aver bloccato nel dashboard nel passaggio precedente. In caso contrario, è possibile trovarlo selezionando **Tutti i servizi** e quindi **Profili CDN**. Nel riquadro **Profili CDN** selezionare il profilo a cui si intende aggiungere l'endpoint. 
   
    Verrà visualizzato il riquadro del profilo di rete CDN.
   
    ![Profilo di rete CDN][cdn-profile-settings]

2. Selezionare **Endpoint**.
   
    ![Pulsante Aggiungi endpoint][cdn-new-endpoint-button]
   
    Verrà visualizzato il riquadro **Aggiungi un endpoint**.
   
    ![Riquadro Aggiungi un endpoint][cdn-add-endpoint]

3. In **Nome** immettere un nome univoco per il nuovo endpoint di rete CDN. Questo nome viene usato per accedere alle risorse memorizzate nella cache nel dominio `<endpointname>.azureedge.net`.

4. In **Tipo di origine** selezionare un tipo di origine. Selezionare **Archiviazione** per un account di archiviazione di Azure, **Servizio Cloud** per un servizio cloud di Azure, **App Web** per un'app Web di Azure oppure **Origine personalizzata** per qualsiasi altra origine di server Web accessibile pubblicamente, ospitata in Azure o altrove.
   
    ![Tipo di origine della rete CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. In **Nome host dell'origine** selezionare o immettere il dominio di origine. L'elenco a discesa include tutte le origini disponibili del tipo specificato nel passaggio 4. Se come tipo di origine è stata selezionata l'opzione **Origine personalizzata**, immettere il dominio dell'origine personalizzata.
    
6. In **Percorso dell'origine** immettere il percorso delle risorse da memorizzare nella cache oppure lasciare il campo vuoto per consentire la memorizzazione nella cache di qualsiasi risorsa nel dominio specificato nel passaggio 5.
    
7. In **Intestazione host dell'origine** immettere l'intestazione host che dovrà essere inviata dalla rete CDN di Azure con ogni richiesta oppure lasciare l'impostazione predefinita.
   
   > [!WARNING]
   > Per alcuni tipi di origini, ad esempio Archiviazione di Azure e App Web, è necessario che l'intestazione host corrisponda al dominio dell'origine. A meno che non si abbia un'origine che richiede un'intestazione host diversa dal dominio, è consigliabile lasciare il valore predefinito.
   > 
    
8. Per **Protocollo** e **Porta dell'origine** specificare i protocolli e le porte usate per accedere alle risorse in corrispondenza dell'origine. È necessario selezionare almeno un protocollo (HTTP o HTTPS). Usare il dominio fornito dalla rete CDN, `<endpointname>.azureedge.net`, per accedere al contenuto HTTPS. 
   
   > [!NOTE]
   > Il valore in **Porta dell'origine** determina solo la porta usata dall'endpoint per recuperare informazioni dall'origine. L'endpoint stesso è disponibile per i client finali solo sulle porte HTTP e HTTPS predefinite (80 e 443), indipendentemente dal valore in **Porta dell'origine**.  
   > 
   > Gli endpoint nel profilo della **rete CDN di Azure fornita da Akamai** non consentono di usare tutto l'intervallo di porte TCP per le porte dell'origine. Per un elenco delle porte di origine non consentite, vedere l'articolo relativo ai [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx)(Porte di origine consentite in Rete CDN di Azure da Akamai).  
   > 
   > Quando si accede al contenuto della rete CDN con HTTPS, esistono i vincoli seguenti:
   > 
   > * Usare il certificato SSL fornito dalla rete CDN. I certificati di terze parti non sono supportati.
   > * Il supporto di HTTPS per i domini personalizzati della rete CDN di Azure è disponibile solo con i prodotti **rete CDN di Azure fornita da Verizon** (Standard e Premium). Non è disponibile nei prodotti della **rete CDN di Azure fornita da Akamai**. Per altre informazioni, vedere [Configurare HTTPS in un dominio personalizzato della rete CDN di Azure](cdn-custom-ssl.md).
    
9. Selezionare **Aggiungi** per creare il nuovo endpoint.
   
   Dopo la creazione, l'endpoint verrà visualizzato nell'elenco di endpoint per il profilo.
    
   ![Endpoint della rete CDN][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > Dato che la propagazione della registrazione richiede tempo, l'endpoint non è disponibile immediatamente per l'uso. La propagazione dei profili della **rete CDN di Azure fornita da Akamai** di solito dura meno di un minuto. Per i profili della **rete CDN di Azure fornita da Verizon**, la propagazione viene in genere completata entro 90 minuti, ma in alcuni casi può richiedere più tempo.
    > 
    > Se si prova a usare il nome di dominio della rete CDN prima che la configurazione dell'endpoint sia stata propagata ai POP, si potrebbe ricevere lo stato di risposta HTTP 404. Se sono trascorse diverse ore da quando è stato creato l'endpoint e si riceve ancora uno stato di risposta 404, vedere [Risoluzione dei problemi degli endpoint della rete CDN che restituiscono stati 404](cdn-troubleshoot-endpoint.md).
    > 
    > 

## <a name="see-also"></a>Vedere anche
* [Controllo del comportamento di memorizzazione nella cache delle richieste della rete CDN con le stringhe di query](cdn-query-string.md)
* [Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](cdn-map-content-to-custom-domain.md)
* [Precaricamento di risorse in un endpoint della rete CDN di Azure](cdn-preload-endpoint.md)
* [Ripulire un endpoint della rete CDN di Azure](cdn-purge-endpoint.md)
* [Risoluzione dei problemi degli endpoint della rete CDN che restituiscono stati 404](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
