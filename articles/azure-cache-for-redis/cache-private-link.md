---
title: Cache di Azure per Redis con collegamento privato di Azure (anteprima)
description: L'endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro alla cache di Azure per il collegamento di redis basato su Azure privato. In questo articolo si apprenderà come creare una cache di Azure, una rete virtuale di Azure e un endpoint privato usando il portale di Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 59d5c6c4a9c3af70eb08e9d5a05d516f4b8b8bdd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099665"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Cache di Azure per Redis con collegamento privato di Azure (anteprima)
L'endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro alla cache di Azure per il collegamento di redis basato su Azure privato. 

Questo articolo illustra come creare una cache di Azure, una rete virtuale di Azure e un endpoint privato usando il portale di Azure.  

## <a name="prerequisites"></a>Prerequisiti
* Sottoscrizione di Azure- [crearne una gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
  > Questa funzionalità è attualmente in anteprima: [Contattaci](mailto:azurecache@microsoft.com) se sei interessato.
  >


## <a name="create-a-cache"></a>Creare una cache
1. Per creare una cache, accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**. 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selezionare Crea una risorsa.":::
   
1. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selezionare cache di Azure per Redis.":::
   
1. Nella pagina **Nuova cache Redis** configurare le impostazioni per la nuova cache.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà * \<DNS name> . Redis.cache.Windows.NET*. | 
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
   | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Posizione** | Nell'elenco a discesa selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
   | **Piano tariffario** | Nell'elenco a discesa selezionare un [piano tariffario](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |
   
1. Selezionare **Crea**. 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Creare cache di Azure per Redis.":::
   
   La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Cache di Azure per Redis creata.":::

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Questa sezione illustra come creare una rete virtuale e una subnet.

1. Selezionare **Crea una risorsa**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selezionare Crea una risorsa.":::

2. Nella pagina **nuovo** selezionare **rete e quindi** **rete virtuale**.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="Creare una rete virtuale.":::

3. In **Crea rete virtuale**immettere o selezionare queste informazioni nella scheda **nozioni di base** :

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli progetto**  |                                                                 |
    | Sottoscrizione     | Nell'elenco a discesa selezionare la sottoscrizione.                                  |
    | Gruppo di risorse   | Elenco a discesa e selezionare un gruppo di risorse. |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Entrare**\<virtual-network-name>**                                    |
    | Area           | Selezionare**\<region-name>** |

4. Selezionare la scheda **indirizzi IP** oppure fare clic sul pulsante **Avanti: indirizzi IP** nella parte inferiore della pagina.

5. Nella scheda **indirizzi IP** immettere queste informazioni:

    | Impostazione            | Valore                      |
    |--------------------|----------------------------|
    | Spazio indirizzi IPv4 | Entrare**\<IPv4-address-space>** |

6. In **nome subnet**selezionare la parola **default**.

7. In **modifica subnet**immettere queste informazioni:

    | Impostazione            | Valore                      |
    |--------------------|----------------------------|
    | Nome della subnet | Entrare**\<subnet-name>** |
    | Intervallo di indirizzi subnet | Entrare**\<subnet-address-range>**

8. Selezionare **Salva**.

9. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica + crea** .

10. Selezionare **Crea**.


## <a name="create-a-private-endpoint"></a>Creare un endpoint privato 

In questa sezione si creerà un endpoint privato che verrà connesso alla cache creata in precedenza.

1. Cercare il **collegamento privato** e premere INVIO o selezionarlo dai suggerimenti per la ricerca.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Cercare un collegamento privato.":::

2. Sul lato sinistro della schermata selezionare **endpoint privati**.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="Selezionare collegamento privato.":::

3. Selezionare il pulsante **+ Aggiungi** per creare l'endpoint privato. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="Aggiungere un collegamento privato.":::

4. Nella **pagina Crea un endpoint privato**configurare le impostazioni per l'endpoint privato.

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Nell'elenco a discesa selezionare la sottoscrizione. |
    | Gruppo di risorse | Elenco a discesa e selezionare un gruppo di risorse. |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome |Immettere un nome per l'endpoint privato.  |
    | Area |Nell'elenco a discesa selezionare una località. |
    |||

5. Selezionare il pulsante **Next: Resource (risorsa** ) nella parte inferiore della pagina.

6. Nella scheda **risorsa** selezionare la sottoscrizione, scegliere il tipo di risorsa Microsoft. cache/Redis, quindi selezionare la cache creata nella sezione precedente.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="Risorse per il collegamento privato.":::

7. Selezionare il pulsante **Next: Configuration (configurazione** ) nella parte inferiore della pagina.

8. Nella scheda **configurazione** selezionare la rete virtuale e la subnet create nella sezione precedente.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="Configurazione per il collegamento privato.":::

9. Selezionare il pulsante **Next: Tags (tag** ) nella parte inferiore della pagina.

10. Nella scheda **tag** immettere il nome e il valore se si vuole categorizzare la risorsa. Questo passaggio è facoltativo.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="Tag per il collegamento privato.":::

11. Selezionare **Rivedi e crea**. Si passa alla scheda **Rivedi e crea**in   cui Azure convalida la configurazione.

12. Una volta visualizzato il messaggio di **convalida** verde, selezionare **Crea**.
