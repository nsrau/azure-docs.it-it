---
title: Eseguire l'onboarding di un dominio radice o Apex a una porta anteriore esistente-portale di Azure
description: Informazioni su come caricare un dominio radice o Apex in un sportello anteriore esistente usando il portale di Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184622"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Onboarding di un dominio radice o apice nella porta anteriore
Il front-end di Azure usa record CNAME per convalidare la proprietà del dominio per l'onboarding di domini personalizzati. Inoltre, la porta anteriore non espone l'indirizzo IP front-end associato al profilo della porta anteriore e pertanto non è possibile eseguire il mapping del dominio Apex a un indirizzo IP, se lo scopo è quello di eseguire l'onboarding in Azure front door.

Il protocollo DNS impedisce l'assegnazione di record CNAME al dominio radice. Ad esempio, se il dominio è `contoso.com`; è possibile creare record CNAME per `somelabel.contoso.com`; Tuttavia, non è possibile creare CNAME per `contoso.com` stesso. Questa restrizione presenta un problema per i proprietari di applicazioni che hanno applicazioni con carico bilanciato dietro Azure front door. Poiché l'uso di un profilo di porta anteriore richiede la creazione di un record CNAME, non è possibile puntare al profilo della porta anteriore dal vertice della zona.

Questo problema viene risolto usando i record alias nel servizio DNS di Azure. A differenza dei record CNAME, i record di alias vengono creati nel vertice della zona e i proprietari dell'applicazione possono usarli per puntare il record Apex della zona a un profilo di porta anteriore con endpoint pubblici. I proprietari dell'applicazione puntano allo stesso profilo di porta anteriore usato per qualsiasi altro dominio nella zona DNS. Ad esempio, `contoso.com` e `www.contoso.com` possono puntare allo stesso profilo di porta anteriore. 

Per eseguire il mapping del dominio Apex o radice al profilo front-end è necessario fondamentalmente la flat CNAME o la ricerca DNS, che è un meccanismo in cui il provider DNS risolve in modo ricorsivo la voce CNAME finché non raggiunge un indirizzo IP. Questa funzionalità è supportata da DNS di Azure per gli endpoint della porta anteriore. 

> [!NOTE]
> Sono disponibili anche altri provider DNS che supportano la conversione in formato CNAME o la ricerca DNS. Tuttavia, il front-end di Azure consiglia di usare DNS di Azure per i clienti che ospitano i propri domini.

È possibile usare il portale di Azure per caricare un dominio Apex sulla porta anteriore e abilitare HTTPS su di esso associando tale dominio a un certificato per la terminazione SSL. I domini Apex sono denominati anche domini radice o nudi.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un record alias che punti al profilo di porta anteriore
> * Aggiungere il dominio radice alla porta anteriore
> * Configurare HTTPS nel dominio radice

> [!NOTE]
> Per questa esercitazione è necessario che sia già stato creato un profilo di sportello anteriore. Per iniziare, fare riferimento ad altre esercitazioni, ad esempio [avvio rapido: creare una porta anteriore](./quickstart-create-front-door.md) o [creare una porta anteriore con Reindirizzamento da http a https](./front-door-how-to-redirect-https.md) .

## <a name="create-an-alias-record-for-zone-apex"></a>Creare un record alias per l'apice della zona

1. Aprire la configurazione **DNS di Azure** per il dominio da caricare.
2. Creare o modificare il record per il vertice della zona.
3. Selezionare il **tipo** _di record come record_ e quindi selezionare _Sì_ per **set di record alias**. Il **tipo di alias** deve essere impostato su risorsa di _Azure_.
4. Scegliere la sottoscrizione di Azure in cui è ospitato il profilo di porta anteriore, quindi selezionare la risorsa porta anteriore dall'elenco a discesa **delle risorse di Azure** .
5. Fare clic su **OK** per inviare le modifiche.

    ![Record alias per l'apice della zona](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Il passaggio precedente creerà un record di apice della zona che punta alla risorsa front-end e anche un mapping di record CNAME ' afdverify ' (esempio-`afdverify.contosonews.com`) a `afdverify.<name>.azurefd.net` che verrà usato per l'onboarding del dominio sul profilo della porta anteriore.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Onboarding del dominio personalizzato nella porta anteriore

1. Nella scheda Progettazione porta anteriore fare clic sull'icona "+" nella sezione host front-end per aggiungere un nuovo dominio personalizzato.
2. Immettere il nome di dominio radice o Apex nel campo nome host personalizzato, ad esempio `contosonews.com`.
3. Dopo aver convalidato il mapping CNAME dal dominio alla porta anteriore, fare clic su **Aggiungi** per aggiungere il dominio personalizzato.
4. Fare clic su **Salva** per inviare le modifiche.

![Menu del dominio personalizzato](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Abilitare HTTPS nel dominio personalizzato

1. Fare clic sul dominio personalizzato che è stato aggiunto e nella sezione **dominio personalizzato HTTPS**impostare lo stato su **abilitato**.
2. Selezionare il **tipo di gestione dei certificati** per _usare il proprio certificato_.

> [!WARNING]
> Il tipo di gestione dei certificati gestiti dalla porta anteriore non è attualmente supportato per i domini di apice o radice. L'unica opzione disponibile per l'abilitazione di HTTPS in un vertice o un dominio radice per la porta anteriore è l'uso di un certificato SSL personalizzato ospitato in Azure Key Vault.

3. Assicurarsi di aver configurato le autorizzazioni corrette per la porta anteriore per accedere all'insieme di credenziali delle chiavi come indicato nell'interfaccia utente, prima di procedere al passaggio successivo.
4. Scegliere un **account Key Vault** dalla sottoscrizione corrente, quindi selezionare la **versione** **segreta** e segreta appropriata per eseguire il mapping al certificato corretto.
5. Fare clic su **Aggiorna** per salvare la selezione e quindi fare clic su **Salva**.
6. Fare clic su **Aggiorna** dopo un paio di minuti e quindi fare di nuovo clic sul dominio personalizzato per visualizzare lo stato di avanzamento del provisioning del certificato. 

> [!WARNING]
> Assicurarsi di aver creato le regole di routing appropriate per il dominio Apex o aver aggiunto il dominio alle regole di routing esistenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come creare un Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).