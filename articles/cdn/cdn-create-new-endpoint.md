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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 80e8e85f058a5cec2e3ae6a6cff5cb8a363370e1
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="getting-started-with-azure-cdn"></a>Introduzione alla rete CDN di Azure
Questo articolo illustra come abilitare la rete per la distribuzione di contenuti di Azure creando un nuovo profilo e un nuovo endpoint della rete CDN.

> [!IMPORTANT]
> Per un'introduzione alla rete CDN e un elenco delle funzionalità, vedere la [panoramica della rete CDN](cdn-overview.md).
> 
> 

## <a name="create-a-new-cdn-profile"></a>Creare un nuovo profilo di rete CDN
Un profilo di rete CDN è una raccolta di endpoint della rete CDN.  Ogni profilo contiene uno o più endpoint della rete CDN.  Si consiglia di usare più profili per organizzare gli endpoint della rete CDN tramite il dominio internet, l’applicazione web o altri criteri.

> [!NOTE]
> Una sottoscrizione di Azure ha limiti predefiniti per le risorse seguenti:
> - Numero di profili CDN che possono essere creati
> - Numero di endpoint che possono essere creati in un profilo CDN 
> - Numero di domini personalizzati di cui è possibile eseguire il mapping a un endpoint
>
> Per informazioni sui limiti delle sottoscrizioni CDN, vedere [Limiti relativi alla rete CDN](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits).
>
> I prezzi della rete CDN vengono applicati a livello di profilo della rete CDN. Se si vuole usare una combinazione di piani tariffari della rete CDN di Azure, è necessario avere più profili CDN.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Creare un nuovo endpoint della rete CDN
**Per creare nuovo endpoint della rete CDN**

1. Nel [portale di Azure](https://portal.azure.com)passare al profilo della rete CDN.  Lo si potrebbe aver bloccato nel dashboard nel passaggio precedente.  Se così non fosse, è possibile trovarlo facendo clic su **Esplora**, quindi su **Profili rete CDN** e facendo clic sul profilo in cui si prevede di aggiungere l'endpoint.
   
    Viene visualizzato il pannello del profilo di rete CDN.
   
    ![Profilo di rete CDN][cdn-profile-settings]
2. Fare clic sul pulsante **Aggiungi Endpoint** .
   
    ![Pulsante Aggiungi endpoint][cdn-new-endpoint-button]
   
    Viene visualizzato il pannello **Aggiungi un endpoint** .
   
    ![Pannello Aggiungi endpoint][cdn-add-endpoint]
3. Immettere un **Nome** per questo endpoint della rete CDN.  Questo nome viene usato per accedere alle risorse memorizzate nella cache nel dominio `<endpointname>.azureedge.net`.
4. Nell'elenco a discesa **Tipo origine** selezionare il tipo di origine.  Selezionare **Archiviazione** per un account di archiviazione di Azure, **Servizio Cloud** per un servizio cloud di Azure, **App Web** per un'app Web di Azure oppure **Origine personalizzata** per qualsiasi altra origine di server Web accessibile pubblicamente, ospitata in Azure o altrove.
   
    ![Tipo di origine della rete CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
5. Nell'elenco a discesa **Nome host origine** selezionare o digitare il dominio di origine.  Nell'elenco a discesa compaiono tutte le origini disponibili del tipo specificato nel passaggio 4.  Se come **Tipo origine** è stata selezionata l'opzione *Origine personalizzata*, immettere il dominio dell'origine personalizzata.
6. Nella casella di testo **Percorso origine** inserire il percorso per le risorse che si desidera memorizzare nella cache oppure lasciare vuoto per consentire la memorizzazione nella cache di qualsiasi risorsa nel dominio specificato nel passaggio 5.
7. Nell’ **Intestazione dell’host di origine**, inserire l'intestazione dell’host che si desidera che la rete CDN invii con ogni richiesta di immettere, o lasciare il valore predefinito.
   
   > [!WARNING]
   > Per alcuni tipi di origini, ad esempio Archiviazione di Azure e App Web, è necessario che l'intestazione host corrisponda al dominio dell'origine. A meno che non si abbia un'origine che richiede un'intestazione host diversa dal dominio, è consigliabile lasciare il valore predefinito.
   > 
   > 
8. Per **Protocollo** e **Porta dell'origine** specificare i protocolli e le porte usate per accedere alle risorse in corrispondenza dell'origine. È necessario selezionare almeno un protocollo (HTTP o HTTPS). Usare il dominio fornito dalla rete CDN, `<endpointname>.azureedge.net`, per accedere al contenuto HTTPS. 
   
   > [!NOTE]
   > **Porta dell'origine** interessa solo la porta usata dall'endpoint per recuperare informazioni dall'origine.  L'endpoint stesso è disponibile solo per i client finali sulle porte HTTP e HTTPS (80 e 443), indipendentemente dalla **porta dell'origine**.  
   > 
   > **Rete CDN di Azure da Akamai** non consentono l'intera gamma di porte TCP per le origini.  Per un elenco delle porte di origine non consentite, vedere l'articolo relativo ai [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx)(Porte di origine consentite in Rete CDN di Azure da Akamai).  
   > 
   > L'accesso al contenuto della rete CDN tramite HTTPS presenta i vincoli seguenti:
   > 
   > * È necessario usare il certificato SSL fornito dalla rete CDN. I certificati di terze parti non sono supportati.
   > * Il supporto di HTTPS per i domini personalizzati della rete CDN di Azure è disponibile solo con i prodotti **rete CDN di Azure fornita da Verizon** (Standard e Premium). Non è disponibile nei prodotti della **rete CDN di Azure fornita da Akamai**. Per altre informazioni, vedere [Configurare HTTPS in un dominio personalizzato della rete CDN di Azure](cdn-custom-ssl.md).
  
9. Per creare il nuovo endpoint, fare clic sul pulsante **Aggiungi** .
   
   Dopo la creazione, l'endpoint verrà visualizzato nell'elenco di endpoint per il profilo.
    
   ![Endpoint della rete CDN][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > Dato che la propagazione della registrazione richiede tempo, l'endpoint non sarà disponibile immediatamente per l'uso.  La propagazione dei profili della <b>rete CDN di Azure fornita da Akamai</b> di solito dura meno di un minuto. Per i profili della <b>rete CDN di Azure fornita da Verizon</b>, la propagazione viene in genere completata entro 90 minuti, ma in alcuni casi può richiedere più tempo.
    > 
    > Gli utenti che provano a usare il nome di dominio della rete CDN prima che la configurazione dell'endpoint sia stata propagata ai POP possono ricevere codici di risposta HTTP 404.  Se sono trascorse diverse ore da quando è stato creato l'endpoint e si ricevono ancora risposte 404, vedere [Risoluzione dei problemi degli endpoint della rete CDN che restituiscono stati 404](cdn-troubleshoot-endpoint.md).
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
