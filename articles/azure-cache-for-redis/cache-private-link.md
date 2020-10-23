---
title: Cache di Azure per Redis con collegamento privato di Azure (anteprima)
description: L'endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro alla cache di Azure per il collegamento di redis basato su Azure privato. In questo articolo si apprenderà come creare una cache di Azure, una rete virtuale di Azure e un endpoint privato usando il portale di Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 93a21b627acfb127c98ead465ebeadc8a472bdfd
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122705"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Cache di Azure per Redis con collegamento privato di Azure (anteprima pubblica)
Questo articolo illustra come creare una rete virtuale e una cache di Azure per l'istanza di redis con un endpoint privato usando il portale di Azure. Si apprenderà anche come aggiungere un endpoint privato a una cache di Azure esistente per l'istanza di Redis.

L'endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro alla cache di Azure per il collegamento di redis basato su Azure privato. 

## <a name="prerequisites"></a>Prerequisiti
* Sottoscrizione di Azure- [crearne una gratuitamente](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Per usare gli endpoint privati, è necessario creare la cache di Azure per l'istanza di redis dopo il 28 luglio 2020.
> Attualmente, non è supportata la replica geografica, le regole del firewall, il supporto della console del portale, più endpoint per cache in cluster, la persistenza per il firewall e le cache VNet inserite. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Creare un endpoint privato con una nuova cache di Azure per l'istanza di redis 

In questa sezione verrà creata una nuova cache di Azure per l'istanza di redis con un endpoint privato.

### <a name="create-a-virtual-network"></a>Crea rete virtuale 

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selezionare Crea una risorsa.":::

2. Nella pagina **nuovo** selezionare **rete e quindi** **rete virtuale**.

3. Selezionare **Aggiungi** per creare una rete virtuale.

4. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare la rete virtuale. | 
   | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare la rete virtuale e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Nome** | Immettere un nome di rete virtuale. | Il nome deve iniziare con una lettera o un numero, terminare con una lettera, un numero o un carattere di sottolineatura e può contenere solo lettere, numeri, caratteri di sottolineatura, punti o trattini. | 
   | **Area** | Elenco a discesa e selezionare un'area. | Selezionare un' [area](https://azure.microsoft.com/regions/) accanto ad altri servizi che utilizzeranno la rete virtuale. |

5. Selezionare la scheda **indirizzi IP** oppure fare clic sul pulsante **Avanti: indirizzi IP** nella parte inferiore della pagina.

6. Nella scheda **indirizzi IP** specificare lo spazio degli **indirizzi IPv4** come uno o più prefissi di indirizzo nella notazione CIDR (ad esempio 192.168.1.0/24).

7. In **nome subnet**fare clic su **predefinito** per modificare le proprietà della subnet.

8. Nel riquadro **modifica subnet** specificare un **nome** per la subnet e l'intervallo di **indirizzi della subnet**. L'intervallo di indirizzi della subnet deve essere in notazione CIDR, ad esempio 192.168.1.0/24. Deve essere incluso nello spazio indirizzi della rete virtuale.

9. Selezionare **Salva**.

10. Selezionare la scheda **Verifica + crea** oppure fare clic sul pulsante **Verifica + crea** .

11. Verificare che tutte le informazioni siano corrette e fare clic su **Crea** per effettuare il provisioning della rete virtuale.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Creare una cache di Azure per l'istanza di redis con un endpoint privato
Per creare un'istanza della cache, attenersi alla seguente procedura.

1. Tornare alla Home page di portale di Azure o aprire il menu sidebar, quindi selezionare **Crea una risorsa**. 
   
1. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selezionare Crea una risorsa.":::
   
1. Nella pagina **Nuova cache Redis** configurare le impostazioni per la nuova cache.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
   | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Posizione** | Nell'elenco a discesa selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
   | **Piano tariffario** | Nell'elenco a discesa selezionare un [piano tariffario](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |

1. Selezionare la scheda **Rete** o fare clic sul pulsante **Rete** nella parte inferiore della pagina.

1. Nella scheda **rete** selezionare **endpoint privato** per il metodo di connettività.

1. Fare clic sul pulsante **Aggiungi** per creare l'endpoint privato.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Selezionare Crea una risorsa.":::

1. Nella pagina **Crea un endpoint privato** configurare le impostazioni per l'endpoint privato con la rete virtuale e la subnet create nell'ultima sezione e selezionare **OK**. 

1. Fare clic sul pulsante **Avanti: Avanzate** oppure fare clic sulla scheda **Avanti: Avanzate** nella parte inferiore della pagina.

1. Nella scheda **Avanzate** per un'istanza della cache Basic o Standard selezionare l'interruttore Abilita se si vuole abilitare una porta non TLS.

1. Nella scheda **Avanzate** per l'istanza della cache Premium configurare le impostazioni per la porta non TLS, il clustering e la persistenza dei dati.


1. Fare clic sul pulsante **Avanti: Tag** o fare clic sulla scheda **Avanti: Tag** nella parte inferiore della pagina.

1. Facoltativamente, nella scheda **Tag** immettere il nome e il valore se si vuole categorizzare la risorsa. 

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla scheda Rivedi e crea in cui Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio di convalida verde, selezionare **Crea**.

La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina  **Panoramica**  della cache di Azure per Redis. Quando la voce  **Stato**  indica  **In esecuzione**, la cache è pronta per l'uso. 
    
> [!IMPORTANT]
> 
> È presente un `publicNetworkAccess` flag che è `Enabled` per impostazione predefinita. 
> Questo flag ha lo scopo di consentire facoltativamente di consentire l'accesso dell'endpoint pubblico e privato alla cache se è impostato su `Enabled` . Se impostato su `Disabled` , consente l'accesso solo agli endpoint privati. È possibile impostare il valore su `Disabled` con la seguente richiesta patch.
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> Per connettersi a una cache in cluster, `publicNetworkAccess` è necessario impostare su `Disabled` e può essere presente una sola connessione all'endpoint privato. 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Creare un endpoint privato con una cache di Azure esistente per l'istanza di redis 

In questa sezione si aggiungerà un endpoint privato a una cache di Azure esistente per l'istanza di Redis. 

### <a name="create-a-virtual-network"></a>Crea rete virtuale 
Per creare una rete virtuale, attenersi alla seguente procedura.

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**.

2. Nella pagina **nuovo** selezionare **rete e quindi** **rete virtuale**.

3. Selezionare **Aggiungi** per creare una rete virtuale.

4. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare la rete virtuale. | 
   | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare la rete virtuale e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Nome** | Immettere un nome di rete virtuale. | Il nome deve iniziare con una lettera o un numero, terminare con una lettera, un numero o un carattere di sottolineatura e può contenere solo lettere, numeri, caratteri di sottolineatura, punti o trattini. | 
   | **Area** | Elenco a discesa e selezionare un'area. | Selezionare un' [area](https://azure.microsoft.com/regions/) accanto ad altri servizi che utilizzeranno la rete virtuale. |

5. Selezionare la scheda **indirizzi IP** oppure fare clic sul pulsante **Avanti: indirizzi IP** nella parte inferiore della pagina.

6. Nella scheda **indirizzi IP** specificare lo spazio degli **indirizzi IPv4** come uno o più prefissi di indirizzo nella notazione CIDR (ad esempio 192.168.1.0/24).

7. In **nome subnet**fare clic su **predefinito** per modificare le proprietà della subnet.

8. Nel riquadro **modifica subnet** specificare un **nome** per la subnet e l'intervallo di **indirizzi della subnet**. L'intervallo di indirizzi della subnet deve essere in notazione CIDR, ad esempio 192.168.1.0/24. Deve essere incluso nello spazio indirizzi della rete virtuale.

9. Selezionare **Salva**.

10. Selezionare la scheda **Verifica + crea** oppure fare clic sul pulsante **Verifica + crea** .

11. Verificare che tutte le informazioni siano corrette e fare clic su **Crea** per effettuare il provisioning della rete virtuale.

### <a name="create-a-private-endpoint"></a>Creare un endpoint privato 

Per creare un endpoint privato, attenersi alla seguente procedura.

1. Nella portale di Azure cercare **cache di Azure per Redis** e premere INVIO o selezionarla dai suggerimenti per la ricerca.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Selezionare Crea una risorsa.":::

2. Selezionare l'istanza della cache a cui si vuole aggiungere un endpoint privato.

3. Sul lato sinistro della schermata selezionare **(anteprima) endpoint privato**.

4. Fare clic sul pulsante **endpoint privato** per creare l'endpoint privato.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Selezionare Crea una risorsa.":::

5. Nella **pagina Crea un endpoint privato**configurare le impostazioni per l'endpoint privato.

   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in base alla quale creare questo endpoint privato. | 
   | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare l'endpoint privato e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Nome** | Immettere un nome di endpoint privato. | Il nome deve iniziare con una lettera o un numero, terminare con una lettera, un numero o un carattere di sottolineatura e può contenere solo lettere, numeri, caratteri di sottolineatura, punti o trattini. | 
   | **Area** | Elenco a discesa e selezionare un'area. | Selezionare un' [area](https://azure.microsoft.com/regions/) accanto ad altri servizi che utilizzeranno l'endpoint privato. |

6. Fare clic sul pulsante **Avanti: risorsa** nella parte inferiore della pagina.

7. Nella scheda **risorsa** selezionare la sottoscrizione, scegliere il tipo di risorsa `Microsoft.Cache/Redis` , quindi selezionare la cache a cui si vuole connettere l'endpoint privato.

8. Fare clic sul pulsante **Avanti: configurazione** nella parte inferiore della pagina.

9. Nella scheda **configurazione** selezionare la rete virtuale e la subnet create nella sezione precedente.

10. Fare clic sul pulsante **Next: Tags (tag** ) nella parte inferiore della pagina.

11. Facoltativamente, nella scheda **Tag** immettere il nome e il valore se si vuole categorizzare la risorsa.

12. Selezionare **Rivedi e crea**. Si passa alla scheda **Rivedi e crea**in   cui Azure convalida la configurazione.

13. Dopo che è stato visualizzato il messaggio di **convalida** verde, selezionare **Crea**.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul collegamento privato di Azure, vedere la [documentazione del collegamento privato di Azure](https://docs.microsoft.com/azure/private-link/private-link-overview). 

