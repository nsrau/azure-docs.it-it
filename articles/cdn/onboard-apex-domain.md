---
title: Caricare un dominio radice o Apex in un endpoint della rete CDN di Azure esistente-portale di Azure
description: Informazioni su come caricare un dominio radice o Apex in un endpoint della rete CDN di Azure esistente usando il portale di Azure.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370355"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Eseguire l'onboarding di un dominio radice o Apex in un endpoint della rete CDN di Azure esistente

La rete CDN di Azure usa record CNAME per convalidare la proprietà del dominio per l'onboarding di domini personalizzati. La rete CDN non espone l'indirizzo IP front-end associato al profilo di rete CDN. Non è possibile eseguire il mapping del dominio Apex a un indirizzo IP se lo si intende caricare nella rete CDN di Azure.

Il protocollo DNS impedisce l'assegnazione di record CNAME al dominio radice. Ad esempio, se il dominio è `contoso.com` ; è possibile creare record CNAME per `somelabel.contoso.com` , ma non è possibile creare CNAME per `contoso.com` se stesso. Questa restrizione presenta un problema per i proprietari di applicazioni che hanno applicazioni con carico bilanciato dietro la rete CDN di Azure. Poiché l'uso di un profilo della rete CDN richiede la creazione di un record CNAME, non è possibile puntare al profilo della rete CDN dal vertice della zona.

Questo problema può essere risolto usando i record alias nel servizio DNS di Azure. A differenza dei record CNAME, i record alias vengono creati nel vertice della zona. I proprietari dell'applicazione possono usarlo per puntare il record Apex della zona a un profilo di rete CDN con endpoint pubblici. I proprietari dell'applicazione puntano allo stesso profilo della rete CDN usato per qualsiasi altro dominio nella zona DNS. Ad esempio, `contoso.com` e `www.contoso.com` possono puntare allo stesso profilo di rete CDN. 

Per eseguire il mapping di un dominio Apex o radice al profilo della rete CDN è necessario il CNAME bidimensionale o DNS. Meccanismo in cui il provider DNS risolve in modo ricorsivo la voce CNAME fino a quando non raggiunge un indirizzo IP. Questa funzionalità è supportata da DNS di Azure per gli endpoint della rete CDN. 

> [!NOTE]
> Sono disponibili anche altri provider DNS che supportano la conversione in formato CNAME o la ricerca DNS. Tuttavia, la rete CDN di Azure consiglia di usare DNS di Azure per i clienti che ospitano i propri domini.

È possibile usare il portale di Azure per caricare un dominio Apex nella rete CDN e abilitare HTTPS su di esso associando tale dominio a un certificato per la terminazione TLS. I domini Apex sono denominati anche domini radice o nudi.

## <a name="create-an-alias-record-for-zone-apex"></a>Creare un record alias per l'apice della zona

1. Aprire la configurazione **DNS di Azure** per il dominio da caricare.

2. Selezionare **+ Set di record**.

3. In **Aggiungi set di record** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ------|
    | Nome | Immettere **@** . |
    | Type | Selezionare **un**. |
    | Set di record alias | Selezionare **Sì**. |
    | Tipo di alias | Selezionare **risorsa di Azure**. |
    | Scegliere una sottoscrizione. | Selezionare la propria sottoscrizione. |
    | Risorsa di Azure | Selezionare l'endpoint della rete CDN. |

4. Immettere il valore per **TTL**.

5. Selezionare **OK** per inviare le modifiche.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Record alias per l'apice della zona":::

6. Il passaggio precedente creerà un record di apice della zona che punta alla risorsa della rete CDN. Un **cdnverify** di mapping dei record CNAME viene usato per l'onboarding del dominio nel profilo di rete CDN.
    1. Esempio: **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Onboarding del dominio personalizzato nella rete CDN

Dopo avere registrato il dominio personalizzato, è possibile aggiungerlo all'endpoint della rete CDN. 

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare al profilo della rete CDN contenente l'endpoint di cui si vuole eseguire il mapping a un dominio personalizzato.
    
2. Nella pagina **Profilo CDN** selezionare l'endpoint della rete CDN da associare al dominio personalizzato.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="Selezione endpoint rete CDN" border="true":::
    
3. Selezionare **+ dominio personalizzato**. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Pulsante Aggiungi dominio personalizzato" border="true":::

4. In **Aggiungi un dominio personalizzato** , **nome host dell'endpoint** , è precompilato ed è derivato dall'URL dell'endpoint della rete CDN: **\<endpoint-hostname>** . azureedge.NET. Non può essere modificato.

5. Per **nome host personalizzato** immettere il dominio personalizzato radice o Apex da usare come dominio di origine del record CNAME. 
    1. Ad esempio, **contoso.com**. **Non usare il nome del sottodominio cdnverify**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Aggiungere il dominio personalizzato" border="true":::

6. Selezionare **Aggiungi**.

   Azure verifica l'esistenza del record CNAME per il nome di dominio personalizzato immesso. Se il record CNAME è corretto, il dominio personalizzato verrà convalidato. 

   La propagazione delle impostazioni del nuovo dominio personalizzato a tutti i nodi perimetrali della rete CDN può richiedere tempo: 
    - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
    - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
    - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata in 10 minuti.   

## <a name="enable-https-on-your-custom-domain"></a>Abilitare HTTPS nel dominio personalizzato

Per altre informazioni sull'abilitazione di HTTPS nel dominio personalizzato per la rete CDN di Azure, vedere [esercitazione: configurare HTTPS in un dominio personalizzato](cdn-custom-ssl.md) della rete CDN di Azure

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un endpoint](cdn-create-new-endpoint.md)della rete CDN.
