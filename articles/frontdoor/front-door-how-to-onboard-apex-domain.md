---
title: Eseguire l'onboarding un dominio radice o vertice a una porta principale esistente usando il portale di Azure
description: Informazioni su come eseguire l'onboarding a un dominio radice o vertice a una porta principale esistente usando il portale di Azure.
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 464c38b0ece274d2ea7df89ab9fd7c0a60dc4b58
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332771"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Eseguire l'onboarding un dominio radice o vertice nell'ingresso principale
Porta d'ingresso Azure utilizza i record CNAME per convalidare la proprietà del dominio per l'onboarding dei domini personalizzati. Inoltre, porta d'ingresso non espone l'indirizzo IP front-end associato al profilo di porta principale e pertanto non è possibile mappare il dominio di apex a un indirizzo IP, se si desidera eseguire l'onboarding è di ingresso principale di Azure.

Il protocollo DNS impedisce l'assegnazione di record CNAME al dominio radice. Ad esempio, se il dominio è `contoso.com`; è possibile creare record CNAME per il `somelabel.contoso.com`; ma non è possibile creare record CNAME per `contoso.com` stesso. Questa restrizione presenta un problema per i proprietari delle applicazioni che dispongono di applicazioni con carico bilanciato dietro l'ingresso principale di Azure. Poiché usa un profilo di porta d'ingresso richiede la creazione di un record CNAME, non è possibile in modo da indicare il profilo di porta d'ingresso dal dominio radice della zona.

Questo problema viene risolto utilizzando alias record in DNS di Azure. A differenza dei record CNAME, record di alias vengono creati al vertice della zona e i proprietari delle applicazioni possono utilizzarlo in modo da puntare i record vertice della zona a un profilo di porta d'ingresso che ha endpoint pubblici. I proprietari delle applicazioni scegliere lo stesso profilo di porta d'ingresso che viene usato per qualsiasi altro dominio all'interno della zona DNS. Ad esempio, `contoso.com` e `www.contoso.com` possono puntare allo stesso profilo di porta principale. 

Mapping del dominio radice o vertice al profilo di porta d'ingresso fondamentalmente richiede la bidimensionalità CNAME DNS o chasing, ovvero un meccanismo in cui nel DNS provider in modo ricorsivo risolve la voce CNAME fino al raggiungimento di un indirizzo IP. Questa funzionalità è supportata da DNS di Azure per gli endpoint di ingresso principale. 

> [!NOTE]
> Sono disponibili altri provider DNS e che supportano la bidimensionalità CNAME o DNS di ricerca, tuttavia, porta principale di Azure consiglia di usare DNS di Azure per i clienti per ospitare i propri domini.

È possibile usare il portale di Azure per eseguire l'onboarding di un dominio di apex sull'ingresso principale e abilitare HTTPS su di essa mediante l'associazione con un certificato per la terminazione SSL. Domini vertice sono anche denominati radice o i domini naked.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un record alias che punta al profilo di porta principale
> * Aggiungere il dominio radice per l'ingresso principale
> * Configurare HTTPS nel dominio radice

> [!NOTE]
> Questa esercitazione è necessario aver già creato un profilo di porta principale. Vedere altre esercitazioni, ad esempio [Guida introduttiva: Creare una porta Front](./quickstart-create-front-door.md) oppure [creare una porta d'ingresso tramite HTTP per il reindirizzamento HTTPS](./front-door-how-to-redirect-https.md) per iniziare.

## <a name="create-an-alias-record-for-zone-apex"></a>Creare un record di alias per vertice della zona

1. Aprire **DNS di Azure** configurazione per il dominio da caricare.
2. Creare o modificare il record per il vertice della zona.
3. Selezionare il record **tipo** come _oggetto_ record e quindi selezionare _Sì_ per **set di record Alias**. **Tipo di alias** deve essere impostata su _risorse di Azure_.
4. Scegliere la sottoscrizione di Azure in cui è ospitato il profilo di porta principale e quindi selezionare la risorsa di ingresso principale dal **risorse di Azure** elenco a discesa.
5. Fare clic su **OK** per inviare le modifiche.

    ![Record di alias per vertice della zona](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Il passaggio precedente creerà un record di vertice della zona che punta alla risorsa di ingresso principale, nonché un mapping dei record CNAME 'afdverify' (esempio: `afdverify.contosonews.com`) a `afdverify.<name>.azurefd.net` che sarà utilizzato per l'onboarding del dominio nel proprio profilo di porta principale.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Eseguire l'onboarding del dominio personalizzato nell'ingresso principale

1. Nella scheda della finestra di progettazione porta principale, fare clic su '+' icona negli host front-end sezione per aggiungere un nuovo dominio personalizzato.
2. Immettere il nome di dominio radice o vertice nel campo nome host personalizzato, esempio `contosonews.com`.
3. Dopo aver convalidato il mapping CNAME dal dominio per l'ingresso principale, fare clic su **Add** per aggiungere il dominio personalizzato.
4. Fare clic su **salvare** per inviare le modifiche.

![Menu del dominio personalizzato](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Abilitare HTTPS nel dominio personalizzato

1. Fare clic sul dominio personalizzato è stato aggiunto e nella sezione **HTTPS dominio personalizzato**, impostare lo stato su **abilitato**.
2. Selezionare il **tipo di gestione di certificati** al _'Usa certificato personale'_ .

> [!WARNING]
> Front-tipo di gestione di certificati sportello gestito non è attualmente supportato per i domini radice o vertice. L'unica opzione disponibile per l'abilitazione di HTTPS in un dominio radice o vertice di ingresso principale Usa il tuo certificato SSL personalizzato ospitato in Azure Key Vault.

3. Assicurarsi di aver impostato le autorizzazioni appropriate per l'ingresso principale accedere alla chiave dell'insieme di credenziali come indicato nell'interfaccia utente, prima di procedere al passaggio successivo.
4. Scegliere una **account Key Vault** dalla sottoscrizione corrente e quindi selezionare l'appropriata **segreto** e **versione del segreto** per eseguire il mapping al certificato corretto.
5. Fare clic su **Update** per salvare la selezione e quindi fare clic su **salvare**.
6. Fare clic su **Aggiorna** dopo un paio di minuti e quindi fare clic sul dominio personalizzato per visualizzare lo stato di avanzamento del provisioning del certificato. 

> [!WARNING]
> Assicurarsi di aver creato le regole di routing appropriate per il dominio di apex o aggiunto al dominio per le regole di routing esistente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).