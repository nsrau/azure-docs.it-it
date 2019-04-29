---
title: Creare un endpoint della rete CDN di Azure | Microsoft Docs
description: Questo articolo illustra come creare un nuovo endpoint della rete per la distribuzione di contenuti di Azure (rete CDN), incluse le impostazioni avanzate.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 296f1df9175f4595274a57ed936282abb73da18b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679551"
---
# <a name="create-an-azure-cdn-endpoint"></a>Creare un endpoint della rete CDN di Azure
Questo articolo descrive tutte le impostazioni per la creazione di un endpoint della [rete per la distribuzione di contenuti (CDN) di Azure](cdn-overview.md) in un profilo della rete CDN esistente. Dopo aver creato un profilo e un endpoint, è possibile avviare la distribuzione di contenuti ai clienti. Per una Guida introduttiva sulla creazione di un profilo e un endpoint, vedere [Guida introduttiva: Creare un profilo e un endpoint della rete CDN di Azure](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di poter creare un endpoint della rete CDN, è necessario avere creato almeno un profilo della rete CDN, che può contenere uno o più endpoint della rete CDN. Per organizzare gli endpoint di rete CDN per dominio Internet, applicazione Web o altri criteri, è possibile usare più profili. Dato che i prezzi della rete CDN vengono applicati a livello del profilo della rete CDN, è necessario creare più profili di rete CDN se si vuole usare una combinazione di piani tariffari per la rete CDN di Azure. Per creare un profilo di rete CDN, vedere [Creare un nuovo profilo di rete CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure
Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="create-a-new-cdn-endpoint"></a>Creare un nuovo endpoint della rete CDN

1. Nel [portale di Azure](https://portal.azure.com)passare al profilo della rete CDN. Lo si potrebbe aver bloccato nel dashboard nel passaggio precedente. In caso contrario, è possibile trovarlo selezionando **Tutti i servizi** e quindi **Profili CDN**. Nel riquadro **Profili CDN** selezionare il profilo a cui si intende aggiungere l'endpoint. 
   
    Verrà visualizzato il riquadro del profilo di rete CDN.

2. Selezionare **Endpoint**.
   
    ![Selezionare l'endpoint della rete CDN](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Verrà visualizzata la pagina **Aggiungere un endpoint**.
   
    ![Pagina Aggiungi endpoint](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. In **Nome** immettere un nome univoco per il nuovo endpoint di rete CDN. Questo nome viene usato per accedere alle risorse memorizzate nella cache nel dominio  _\<endpointname >_. azureedge.net.

4. Per **Tipo di origine** scegliere uno dei tipi di origine seguenti: 
   - **Archiviazione** per Archiviazione di Azure
   - **Servizio cloud** per Servizi cloud di Azure
   - **App Web** per App Web di Azure
   - **Origine personalizzata** per qualsiasi altro server Web di origine accessibile pubblicamente (ospitato in Azure o altrove)

5. In **Nome host dell'origine** selezionare o immettere il dominio del server di origine. L'elenco a discesa include tutti i server di origine disponibili del tipo specificato nel passaggio 4. Se come tipo di origine è stata selezionata l'opzione **Origine personalizzata**, immettere il dominio del server di origine personalizzato.
    
6. Per **Percorso origine** immettere il percorso alle risorse che si vuole memorizzare nella cache. Per consentire la memorizzazione nella cache di qualsiasi risorsa nel dominio specificato nel passaggio 5, lasciare vuota questa impostazione.
    
7. In **Intestazione host dell'origine** immettere l'intestazione host che dovrà essere inviata dalla rete CDN di Azure con ogni richiesta oppure lasciare l'impostazione predefinita.
   
   > [!NOTE]
   > Per alcuni tipi di origini, ad esempio Archiviazione di Azure e App Web, è necessario che l'intestazione host corrisponda al dominio dell'origine. A meno che non si abbia un'origine che richiede un'intestazione host diversa dal dominio, è consigliabile lasciare il valore predefinito.
   > 
    
8. Per **Protocollo** e **Porta dell'origine** specificare i protocolli e le porte da usare per accedere alle risorse nel server di origine. È necessario selezionare almeno un protocollo (HTTP o HTTPS). Usare il dominio della rete CDN-fornito (_\<endpointname >_. azureedge.net) per accedere al contenuto HTTPS. 
   
   > [!NOTE]
   > Il valore di **Porta dell'origine** determina solo la porta usata dall'endpoint per recuperare informazioni dal server di origine. L'endpoint stesso è disponibile per i client finali solo sulle porte HTTP e HTTPS predefinite (80 e 443), indipendentemente dal valore in **Porta dell'origine**.  
   > 
   > Gli endpoint nel profilo della **rete CDN di Azure fornita da Akamai** non consentono di usare tutto l'intervallo di porte TCP per le porte dell'origine. Per un elenco delle porte di origine non consentite, vedere l'articolo relativo ai [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100))(Porte di origine consentite in Rete CDN di Azure da Akamai).  
   > 
   > Il supporto di HTTPS per i domini personalizzati di rete CDN di Azure non è disponibile per i prodotti di **rete CDN di Azure con tecnologia Akamai**. Per altre informazioni, vedere [Configurare HTTPS in un dominio personalizzato della rete CDN di Azure](cdn-custom-ssl.md).
    
9. In **Ottimizzato per** selezionare un tipo di ottimizzazione che meglio corrisponde allo scenario e al tipo di contenuto che si vuole distribuire tramite l'endpoint. Per altre informazioni, vedere [Ottimizzare la rete CDN di Azure per il tipo di distribuzione di contenuti](cdn-optimization-overview.md).

    Sono supportate le seguenti impostazioni del tipo di ottimizzazione, in base al tipo di profilo:
    - Profili di **rete CDN Standard di Azure con tecnologia Microsoft**:
       - [**Distribuzione Web generale**](cdn-optimization-overview.md#general-web-delivery)

    - Profili di **rete CDN Standard di Azure con tecnologia Verizon** e **rete CDN Premium di Azure con tecnologia Verizon**:
       - [**Distribuzione Web generale**](cdn-optimization-overview.md#general-web-delivery)
       - [**Accelerazione sito dinamico**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - Profili di **rete CDN Standard di Azure con tecnologia Akamai**:
       - [**Distribuzione Web generale**](cdn-optimization-overview.md#general-web-delivery)
       - [**Streaming multimediale generale**](cdn-optimization-overview.md#general-media-streaming)
       - [**Streaming multimediale video on demand**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Download di file di grandi dimensioni**](cdn-optimization-overview.md#large-file-download)
       - [**Accelerazione sito dinamico**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Selezionare **Aggiungi** per creare il nuovo endpoint.
   
    Dopo la creazione, l'endpoint verrà visualizzato nell'elenco di endpoint per il profilo.
    
    ![Endpoint della rete CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Dato che la propagazione della registrazione richiede tempo, l'endpoint non è immediatamente disponibile per l'uso: 
    - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
    - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
    - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata entro 90 minuti. 
   
    Se si prova a usare il nome di dominio della rete CDN prima che la configurazione dell'endpoint sia stata propagata ai server POP (Point-Of-Presence), si potrebbe ricevere lo stato di risposta HTTP 404. Se sono trascorse diverse ore da quando è stato creato l'endpoint e si riceve ancora uno stato di risposta 404, vedere [Risoluzione dei problemi degli endpoint della rete CDN di Azure che restituiscono un codice stato 404](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Pulire le risorse
Per eliminare un endpoint quando non è più necessario, selezionarlo e quindi selezionare **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi
Per scoprire di più sui domini personalizzati, continuare con l'esercitazione per l'aggiunta di un dominio personalizzato all'endpoint della rete CDN.

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](cdn-map-content-to-custom-domain.md)


