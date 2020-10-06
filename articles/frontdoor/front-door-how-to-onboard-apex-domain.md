---
title: Eseguire l'onboarding di un dominio radice o Apex a una porta anteriore esistente-portale di Azure
description: Informazioni su come caricare un dominio radice o Apex in un sportello anteriore esistente usando il portale di Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 6be33df90ed58dc448009fb0e26ca49c800d1931
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766081"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Eseguire l'onboarding di un dominio radice o apex nella frontdoor
Il front-end di Azure usa record CNAME per convalidare la proprietà del dominio per l'onboarding di domini personalizzati. La porta anteriore non espone l'indirizzo IP front-end associato al profilo della porta anteriore. Quindi, non è possibile eseguire il mapping del dominio Apex a un indirizzo IP se lo si vuole caricare in Azure front door.

Il protocollo DNS impedisce l'assegnazione di record CNAME al dominio radice. Ad esempio, se il dominio è `contoso.com` ; è possibile creare record CNAME per `somelabel.contoso.com` , ma non è possibile creare CNAME per `contoso.com` se stesso. Questa restrizione presenta un problema per i proprietari di applicazioni che hanno applicazioni con carico bilanciato dietro Azure front door. Poiché l'uso di un profilo di porta anteriore richiede la creazione di un record CNAME, non è possibile puntare al profilo della porta anteriore dal vertice della zona.

Questo problema può essere risolto usando i record alias nel servizio DNS di Azure. A differenza dei record CNAME, i record alias vengono creati nel vertice della zona. I proprietari dell'applicazione possono usarlo per puntare il record Apex della zona a un profilo di porta anteriore con endpoint pubblici. I proprietari dell'applicazione puntano allo stesso profilo di porta anteriore usato per qualsiasi altro dominio nella zona DNS. Ad esempio, `contoso.com` e `www.contoso.com` possono puntare allo stesso profilo di porta anteriore. 

Per eseguire il mapping del dominio apice o radice al profilo della porta anteriore è fondamentalmente richiesta la conversione in formato CNAME o DNS. Meccanismo in cui il provider DNS risolve in modo ricorsivo la voce CNAME fino a quando non raggiunge un indirizzo IP. Questa funzionalità è supportata da DNS di Azure per gli endpoint della porta anteriore. 

> [!NOTE]
> Sono disponibili anche altri provider DNS che supportano la conversione in formato CNAME o la ricerca DNS. Tuttavia, il front-end di Azure consiglia di usare DNS di Azure per i clienti che ospitano i propri domini.

È possibile usare il portale di Azure per caricare un dominio Apex sulla porta anteriore e abilitare HTTPS su di esso associando tale dominio a un certificato per la terminazione TLS. I domini Apex sono denominati anche domini radice o nudi.

## <a name="create-an-alias-record-for-zone-apex"></a>Creare un record alias per l'apice della zona

1. Aprire la configurazione **DNS di Azure** per il dominio da caricare.

1. Creare o modificare il record per il vertice della zona.

1. Selezionare il **tipo** *di record come record* e quindi selezionare *Sì* per **set di record alias**. Il **tipo di alias** deve essere impostato su risorsa di *Azure*.

1. Selezionare la sottoscrizione di Azure in cui viene ospitato il profilo della porta anteriore. Quindi selezionare la risorsa front door dall'elenco a discesa **delle risorse di Azure** .

1. Selezionare **OK** per inviare le modifiche.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Record alias per l'apice della zona":::

1. Il passaggio precedente creerà un record di apice della zona che punta alla risorsa front-end e anche un mapping di record CNAME ' afdverify ' (example- `afdverify.contosonews.com` ) a che verrà usato per l'onboarding del dominio sul profilo della porta anteriore.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Onboarding del dominio personalizzato nella porta anteriore

1. Nella scheda Progettazione porta anteriore selezionare l'icona ' +' nella sezione host front-end per aggiungere un nuovo dominio personalizzato.

1. Immettere il nome di dominio radice o apice nel campo nome host personalizzato, ad esempio `contosonews.com` .

1. Dopo aver convalidato il mapping CNAME dal dominio alla porta anteriore, selezionare **Aggiungi** per aggiungere il dominio personalizzato.

1. Selezionare **Save (Salva** ) per inviare le modifiche.

    :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Record alias per l'apice della zona":::

## <a name="enable-https-on-your-custom-domain"></a>Abilitare HTTPS nel dominio personalizzato

1. Selezionare il dominio personalizzato che è stato aggiunto e nella sezione **https del dominio personalizzato**, impostare lo stato su **abilitato**.

1. Selezionare il  **tipo di gestione dei certificati** per *usare il proprio certificato*.

    :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="Record alias per l'apice della zona":::    

    > [!WARNING]
    > Il tipo di gestione dei certificati gestiti dalla porta anteriore non è attualmente supportato per i domini di apice o radice. L'unica opzione disponibile per l'abilitazione di HTTPS in un vertice o un dominio radice per la porta anteriore è l'uso di un certificato TLS/SSL personalizzato ospitato in Azure Key Vault.

1. Assicurarsi di aver configurato le autorizzazioni corrette per la porta anteriore per accedere all'insieme di credenziali delle chiavi come indicato nell'interfaccia utente, prima di procedere al passaggio successivo.

1. Scegliere un **account Key Vault** dalla sottoscrizione corrente, quindi selezionare la **versione** **segreta** e segreta appropriata per eseguire il mapping al certificato corretto.

1. Selezionare **Update (Aggiorna** ) per salvare la selezione e quindi selezionare **Save (Salva**).

1. Selezionare **Aggiorna** dopo un paio di minuti e quindi selezionare di nuovo il dominio personalizzato per visualizzare lo stato di avanzamento del provisioning del certificato. 

> [!WARNING]
> Assicurarsi di aver creato le regole di routing appropriate per il dominio Apex o aver aggiunto il dominio alle regole di routing esistenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).