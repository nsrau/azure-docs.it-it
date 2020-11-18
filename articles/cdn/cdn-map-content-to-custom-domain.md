---
title: "Esercitazione: Aggiungere un dominio personalizzato all'endpoint"
titleSuffix: Azure Content Delivery Network
description: Questa esercitazione descrive come aggiungere un dominio personalizzato a un endpoint Rete CDN di Azure affinché il nome di dominio sia visibile nell'URL.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 03ed47ee97f52aca708118f202fad583753549bf
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331234"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Esercitazione: Aggiungere un dominio personalizzato all'endpoint

Questa esercitazione illustra come aggiungere un dominio personalizzato a un endpoint della rete CDN di Azure. 

Il nome dell'endpoint nel profilo di rete CDN è un sottodominio di azureedge.net. Per impostazione predefinita, quando viene distribuito il contenuto, il dominio del profilo di rete CDN è incluso nell'URL,

ad esempio **https://contoso.azureedge.net/photo.png**.

La rete CDN di Azure offre la possibilità di associare un dominio personalizzato a un endpoint della rete CDN. Questa opzione consente di distribuire il contenuto con un dominio personalizzato nell'URL anziché con il dominio predefinito.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Creare un record DNS CNAME.
> - Associare il dominio personalizzato all'endpoint della rete CDN.
> - Verificare il dominio personalizzato.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Prima di poter completare i passaggi di questa esercitazione, è necessario creare un profilo di rete CDN e almeno un endpoint della rete CDN. 
    * Per altre informazioni, vedere [Avvio rapido: Creare un profilo e un endpoint della rete CDN di Azure](cdn-create-new-endpoint.md).

* Se non si dispone già di un dominio personalizzato, acquistarne uno con un provider di domini. 
    * Per altre informazioni, vedere [Acquistare un nome di dominio personalizzato](../app-service/manage-custom-dns-buy-domain.md).

* Se si usa Azure per ospitare i [domini DNS](../dns/dns-overview.md), delegare il dominio personalizzato al servizio DNS di Azure. 
    * Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](../dns/dns-delegate-domain-azure-dns.md).

* In caso contrario, se si usa un provider di domini per gestire il dominio DNS, continuare con [Creare un record DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Creare un record DNS CNAME

Prima di poter usare un dominio personalizzato con un endpoint della rete CDN di Azure, è necessario creare un record di nome canonico (CNAME) che punti all'endpoint di rete CDN con il servizio DNS di Azure o con il provider del servizio DNS. 

Un record CNAME è un tipo di record DNS che esegue il mapping di un nome di dominio di origine a uno di destinazione. 

Per la rete CDN di Azure il nome di dominio di origine è il nome di dominio personalizzato, mentre il nome di dominio di destinazione è il nome host dell'endpoint della rete CDN. 

La rete CDN di Azure instrada il traffico indirizzato al dominio personalizzato di origine al nome host dell'endpoint della rete CDN di destinazione dopo aver verificato il record CNAME.

È possibile associare un dominio personalizzato e il relativo sottodominio a un singolo endpoint alla volta. 

Usare più record CNAME per sottodomini diversi dello stesso dominio personalizzato per servizi di Azure diversi.

È possibile eseguire il mapping di un dominio personalizzato con sottodomini diversi allo stesso endpoint della rete CDN.

> [!NOTE]
> In questa esercitazione viene usato il tipo di record CNAME. Se si usano tipi di record A o AAAA, seguire questi stessi passaggi e sostituire CNAME con il tipo di record scelto.

---
# <a name="azure-dns"></a>[**DNS di Azure**](#tab/azure-dns)

DNS di Azure usa record alias per le risorse di Azure all'interno della stessa sottoscrizione.

Per aggiungere un record alias per l'endpoint della rete CDN di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu a sinistra selezionare **Tutte le risorse** quindi la zona DNS di Azure per il dominio personalizzato.

3. Nella zona DNS del dominio personalizzato selezionare **+ Set di record**.

4. In **Aggiungi set di record** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome  | Immettere l'alias da usare per l'endpoint della rete CDN. ad esempio **www**. |
    | Type  | Selezionare **CNAME**. |
    | Set di record alias | Selezionare **Sì**. |
    | Tipo di alias | Selezionare **Risorsa di Azure**. |
    | Scegliere una sottoscrizione. | Selezionare la propria sottoscrizione. |
    | Risorsa di Azure | Selezionare l'endpoint della rete CDN di Azure. |

5. Modificare il valore **TTL** per il record specificando il valore appropriato.

6. Selezionare **OK**.

# <a name="dns-provider"></a>[**Provider DNS**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Eseguire il mapping del sottodominio cdnverify temporaneo

Quando si esegue il mapping di un dominio esistente in fase di produzione, è necessario tenere presenti alcune considerazioni speciali. 

Durante la registrazione del dominio personalizzato nel portale di Azure, può verificarsi un breve tempo di inattività per il dominio.

Per evitare l'interruzione del traffico Web, eseguire prima il mapping del dominio personalizzato al nome host dell'endpoint della rete CDN con il sottodominio **cdnverify** di Azure per creare un mapping CNAME temporaneo. 

Con questo metodo, gli utenti possono accedere al dominio senza interruzioni durante l'esecuzione del mapping DNS. 

Se gli eventuali tempi di inattività dell'ambiente di produzione non sono un problema, è possibile eseguire il mapping diretto del dominio personalizzato all'endpoint della rete CDN. Continuare con [Eseguire il mapping del dominio personalizzato permanente](#map-the-permanent-custom-domain).

Per creare un record CNAME con il sottodominio cdnverify:

1. Accedere al sito Web del provider DNS per il dominio personalizzato.

2. Creare una voce di record CNAME per il dominio personalizzato e completare i campi come illustrato nella tabella seguente (i nomi dei campi possono variare):

    | Source (Sorgente)                    | Type  | Destination                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify. www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Origine: immettere il nome di dominio personalizzato, incluso il sottodominio cdnverify, nel formato seguente: **cdnverify.\<custom-domain-name>**
        - Ad esempio: **cdnverify. www.contoso.com**

    - Digitare: Immettere o selezionare **CNAME**.

    - Destinazione: immettere il nome host dell'endpoint della rete CDN, incluso il sottodominio cdnverify, nel formato seguente: **cdnverify.\<endpoint-name>.azureedge.net**. 
        - Ad esempio: **cdnverify.contoso.azureedge.net**

3. Salvare le modifiche.

## <a name="map-the-permanent-custom-domain"></a>Eseguire il mapping del dominio personalizzato permanente

In questa sezione verrà eseguito il mapping del dominio personalizzato permanente all'endpoint della rete CDN. 

Per creare un record CNAME per il dominio personalizzato:

1. Accedere al sito Web del provider del dominio personalizzato.

2. Creare una voce di record CNAME per il dominio personalizzato e completare i campi come illustrato nella tabella seguente (i nomi dei campi possono variare):

    | Source (Sorgente)          | Type  | Destination           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Origine: immettere il nome di dominio personalizzato.
        - Ad esempio: **www.contoso.com**

    - Digitare: Immettere o selezionare **CNAME**.

    - Destinazione: immettere il nome host dell'endpoint della rete CDN nel formato seguente: **\<endpoint-name>.azureedge.net**. 
        - Ad esempio: **contoso.azureedge.net**

3. Salvare le modifiche.

4. Se si è creato in precedenza un record CNAME del sottodominio cdnverify temporaneo, eliminarlo. 

5. Se si usa questo dominio personalizzato nell'ambiente di produzione per la prima volta, seguire i passaggi illustrati in [Associare il dominio personalizzato all'endpoint della rete CDN](#associate-the-custom-domain-with-your-cdn-endpoint) e [Verificare il dominio personalizzato](#verify-the-custom-domain).

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Associare il dominio personalizzato all'endpoint della rete CDN

Dopo avere registrato il dominio personalizzato, è possibile aggiungerlo all'endpoint della rete CDN. 

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare al profilo della rete CDN contenente l'endpoint di cui si vuole eseguire il mapping a un dominio personalizzato.
    
2. Nella pagina **Profilo CDN** selezionare l'endpoint della rete CDN da associare al dominio personalizzato.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="Selezione dell'endpoint della rete CDN" border="true":::
    
3. Selezionare **+ Dominio personalizzato**. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Pulsante Aggiungi dominio personalizzato" border="true":::

4. In **Aggiungere un dominio personalizzato** il **Nome host dell'endpoint** è precompilato ed è derivato dall'URL dell'endpoint della rete CDN: **\<endpoint-hostname>** .azureedge.net. Non può essere modificato.

5. Per **Nome host personalizzato**, immettere il dominio personalizzato, incluso il sottodominio, da usare come dominio di origine del record CNAME. 
    1. Ad esempio: **www.contoso.com** o **cdn.contoso.com**. **Non usare il nome del sottodominio cdnverify**.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Aggiungere il dominio personalizzato" border="true":::

6. Selezionare **Aggiungi**.

   Azure verifica l'esistenza del record CNAME per il nome di dominio personalizzato immesso. Se il record CNAME è corretto, il dominio personalizzato verrà convalidato. 

   La propagazione delle impostazioni del nuovo dominio personalizzato a tutti i nodi perimetrali della rete CDN può richiedere tempo: 
    - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
    - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
    - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata in 10 minuti.   


## <a name="verify-the-custom-domain"></a>Verificare il dominio personalizzato

Dopo aver completato la registrazione del dominio personalizzato, verificare che il dominio personalizzato faccia riferimento all'endpoint della rete CDN.
 
1. Assicurarsi di disporre di contenuto pubblico memorizzato nella cache nell'endpoint. Ad esempio, se l'endpoint della rete CDN è associato a un account di archiviazione, la rete CDN di Azure memorizzerà il contenuto in un contenitore pubblico della cache. Per testare il dominio personalizzato, impostare il contenitore affinché consenta l'accesso pubblico e assicurarsi che contenga almeno un file.

2. Nel browser passare all'indirizzo del file usando il dominio personalizzato. Ad esempio, se il dominio personalizzato è `www.contoso.com`, l'URL del file memorizzato nella cache dovrebbe essere simile all'URL seguente: `http://www.contoso.com/my-public-container/my-file.jpg`. Verificare che il risultato corrisponda a quanto si verifica quando si accede all'endpoint della rete CDN direttamente da **\<endpoint-hostname>** .azureedge.net.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si vuole più associare l'endpoint a un dominio personalizzato, seguire questa procedura per rimuovere il dominio personalizzato:
 
1. Nel profilo della rete CDN selezionare l'endpoint con il dominio personalizzato che si vuole rimuovere.

2. Nella pagina **Endpoint**, in Domini personalizzati fare clic con il pulsante destro del mouse sul dominio personalizzato che si vuole rimuovere, quindi scegliere **Elimina** dal menu di scelta rapida. Selezionare **Sì**.

   Il dominio personalizzato viene dissociato dell'endpoint.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> - Creare un record DNS CNAME.
> - Associare il dominio personalizzato all'endpoint della rete CDN.
> - Verificare il dominio personalizzato.

Passare alla prossima esercitazione per imparare a configurare HTTPS in un dominio personalizzato della rete CDN di Azure.

> [!div class="nextstepaction"]
> [Esercitazione: Configurare HTTPS in un dominio personalizzato della rete CDN di Azure](cdn-custom-ssl.md)