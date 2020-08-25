---
title: Più origini dell'endpoint della rete CDN di Azure
description: Introduzione a più origini dell'endpoint della rete CDN di Azure.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 8/20/2020
ms.author: allensu
ms.openlocfilehash: ed6c60b4f66361e87f67f3c64bb60846b2df757b
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817580"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Più origini dell'endpoint della rete CDN di Azure

Il supporto di più origini Elimina i tempi di inattività e stabilisce la ridondanza globale. 

Scegliendo più origini all'interno di un endpoint della rete CDN di Azure, la ridondanza fornita diffonde il rischio eseguendo il probe dell'integrità di ogni origine ed eseguendo il failover, se necessario.

Configurare uno o più gruppi di origine e scegliere un gruppo di origine predefinito. Ogni gruppo di origine è una raccolta di una o più origini che possono richiedere carichi di lavoro simili.

> [!NOTE]
> Attualmente questa funzionalità è disponibile solo dalla rete CDN di Azure di Microsoft. 

## <a name="create-the-origin-group"></a>Creare il gruppo di origine

1. Accedere al [portale di Azure](https://portal.azure.com)

2. Selezionare il profilo della rete CDN di Azure e quindi selezionare l'endpoint da configurare per la funzionalità multiorigine.

3. Selezionare **Origin** in **Impostazioni** nella configurazione dell'endpoint:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Endpoint della rete CDN" border="true":::

4. Per abilitare la funzionalità multiorigine, è necessario almeno un gruppo di origine. Selezionare **Crea gruppo di origine**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Impostazioni origine" border="true":::

5. Nella configurazione **Aggiungi gruppo di origine** immettere o selezionare le informazioni seguenti:

   | Impostazione           | Valore                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Nome gruppo di origine | Immettere un nome per il gruppo di origine.                                   |
   | Stato Probe      | Selezionare **Enabled**. </br> La rete CDN di Azure eseguirà Probe di integrità da punti diversi nel mondo per determinare l'integrità di origine. Non abilitare se il gruppo di origine corrente non è attivo per evitare costi aggiuntivi.
   | Percorso probe        | Percorso nell'origine utilizzato per determinare l'integrità. |
   | Intervallo Probe    | Selezionare un intervallo di probe di 1, 2 o 4 minuti.                        |
   | Protocollo Probe    | Selezionare **http** o **https**.                                         |
   | Metodo Probe      | Selezionare **Head** o **Get**.                                           |
   | Gruppo di origine predefinito | Selezionare la casella da impostare come gruppo di origine predefinito.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Aggiungi gruppo di origine" border="true":::

6. Selezionare **Aggiungi**.

## <a name="add-multiple-origins"></a>Aggiungi più origini

1. Nelle impostazioni origine per l'endpoint selezionare **+ Crea origine**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Crea origine" border="true":::

2. Immettere o selezionare le informazioni seguenti nella configurazione **Aggiungi origine** :

   | Impostazione           | Valore                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Tipo di origine | Selezionare **archiviazione**, **servizio cloud**, **app Web**o **origine personalizzata**.                                   |
   | Nome host dell'origine        | Selezionare o immettere il nome host di origine.  Nell'elenco a discesa sono elencate tutte le origini disponibili del tipo specificato nell'impostazione precedente. Se è stata selezionata l'opzione **origine personalizzata** come tipo di origine, immettere il dominio del server di origine del cliente. |
   | Intestazione host di origine    | Immettere l'intestazione host che si vuole che la rete CDN di Azure invii con ogni richiesta o lasciare il valore predefinito.                        |
   | Porta HTTP   | Immettere la porta HTTP.                                         |
   | Porta HTTPS     | Immettere la porta HTTPS.                                           |
   | Priorità    | Immettere un numero compreso tra 1 e 5.       |
   | Peso      | Immettere un numero compreso tra 1 e 1000.   |

    > [!NOTE]
    > Quando un'origine viene creata all'interno di un gruppo di origine, è necessario accordare una priorità e un peso. Se un gruppo di origine ha una sola origine, la priorità e il peso predefiniti vengono impostati su 1. Il traffico viene indirizzato alle origini con la priorità più alta se l'origine è integra. Se un'origine viene determinata come non integra, le connessioni vengono deviate a un'altra origine nell'ordine di priorità. Se due origini hanno la stessa priorità, il traffico viene distribuito in base al peso specificato per l'origine 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Aggiungi origine aggiuntiva" border="true":::

3. Selezionare **Aggiungi**.

4. Selezionare **Configura origine** per impostare il percorso di origine per tutte le origini:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Configurare il percorso di origine" border="true":::

5. Selezionare **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Configurare le origini e le impostazioni del gruppo di origine

Dopo aver creato diverse origini e un gruppo di origine, è possibile aggiungere o rimuovere le origini in gruppi diversi. Le origini all'interno dello stesso gruppo dovrebbero fornire carichi di lavoro simili. Il traffico verrà distribuito in queste origini in base al relativo stato integro, priorità e valore del peso. 

1. Nelle impostazioni di origine dell'endpoint della rete CDN di Azure selezionare il nome del gruppo di origine che si vuole configurare:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Configurare le origini e le impostazioni del gruppo di origine" border="true":::

2. In **gruppo di origine dell'aggiornamento**selezionare **+ Seleziona origine**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Aggiorna gruppo di origine" border="true":::

4. Selezionare l'origine da aggiungere al gruppo nella casella a discesa e selezionare **OK**.

5. Verificare che l'origine sia stata aggiunta al gruppo e quindi selezionare **Salva**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Verifica aggiunta origine aggiuntiva al gruppo" border="true":::

## <a name="remove-origin-from-origin-group"></a>Rimuovi origine dal gruppo di origine

1. Nelle impostazioni di origine dell'endpoint della rete CDN di Azure selezionare il nome del gruppo di origine:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Rimuovi origine dal gruppo" border="true":::

2. Per rimuovere un'origine dal gruppo di origine, selezionare l'icona del cestino accanto all'origine e selezionare **Salva**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Aggiornare l'origine di eliminazione del gruppo di origine" border="true":::

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata abilitata la funzionalità multiorigine dell'endpoint della rete CDN di Azure.

Per altre informazioni sulla rete CDN di Azure e sugli altri servizi di Azure menzionati in questo articolo, vedere:

* [Rete CDN di Azure](./cdn-overview.md)
* [Confrontare la funzionalità del prodotto rete CDN di Azure](./cdn-features.md)
