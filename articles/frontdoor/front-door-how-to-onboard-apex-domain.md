---
title: Eseguire l'onboarding di un dominio radice o apice in un server Front Door esistente - Portale di AzureOnboard a root or apex domain to an existing Front Door - Azure portal
description: Informazioni su come eseguire l'onboarding di un dominio radice o apice in un Front Door esistente usando il portale di Azure.Learn how to onboard a root or apex domain to an existing Front Door using the Azure portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 4b74338f22a82d76ef13126ee0862b841bd89a99
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878885"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>A bordo di un dominio root o apice sulla porta anteriore
Azure Front Door usa i record CNAME per convalidare la proprietà del dominio per l'onboarding di domini personalizzati. Inoltre, Front Door non espone l'indirizzo IP front-end associato al profilo Front Door e pertanto non è possibile eseguire il mapping del dominio apice a un indirizzo IP, se l'intento è quello di eseguire l'onboarding alla porta frontale di Azure.

Il protocollo DNS impedisce l'assegnazione di record CNAME al dominio radice. Ad esempio, se `contoso.com`il dominio è ; è possibile creare record `somelabel.contoso.com`CNAME per ; ma non è possibile creare `contoso.com` CNAME per se stesso. Questa restrizione rappresenta un problema per i proprietari di applicazioni che dispongono di applicazioni con carico bilanciato dietro Azure Front Door.This restriction presents a problem for application owners who have load-balanced applications behind Azure Front Door. Poiché l'utilizzo di un profilo Front Door richiede la creazione di un record CNAME, non è possibile puntare al profilo Front Door dall'apice della zona.

Questo problema viene risolto utilizzando i record alias nel DNS di Azure.This problem is solved using alias records on Azure DNS. A differenza dei record CNAME, i record alias vengono creati in corrispondenza dell'apice della zona e i proprietari dell'applicazione possono utilizzarlo per indirizzare il record di apice della zona a un profilo Front Door con endpoint pubblici. I proprietari delle applicazioni puntano allo stesso profilo Porta frontale utilizzato per qualsiasi altro dominio all'interno della zona DNS. Ad `contoso.com` esempio, `www.contoso.com` e può puntare allo stesso profilo Porta anteriore. 

Il mapping dell'apice o del dominio radice al profilo Front Door richiede fondamentalmente la conversione CNAME o la ricerca DNS, che è un meccanismo in cui il provider DNS risolve in modo ricorsivo la voce CNAME fino a quando non raggiunge un indirizzo IP. Questa funzionalità è supportata da DNS di Azure per gli endpoint Front Door.This functionality is supported by Azure DNS for Front Door endpoints. 

> [!NOTE]
> Esistono anche altri provider DNS che supportano la conversione CNAME o la ricerca DNS, tuttavia, Azure Front Door consiglia di usare il DNS di Azure per i clienti per l'hosting dei domini.

È possibile usare il portale di Azure per eseguire l'onboarding di un dominio apice sulla porta frontale e abilitare HTTPS su di esso associandolo a un certificato per la terminazione TLS. I domini Apex sono anche indicati come domini root o naked.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare un record alias che punti al profilo della porta frontale
> * Aggiungere il dominio radice alla porta anteriore
> * Configurazione di HTTPS nel dominio radice

> [!NOTE]
> Per questa esercitazione è necessario che sia già stato creato un profilo Porta anteriore. Per iniziare, fare riferimento ad altre esercitazioni come [Guida introduttiva: Creare una porta frontale](./quickstart-create-front-door.md) o [Creare una porta frontale con il reindirizzamento da HTTP a HTTPS.](./front-door-how-to-redirect-https.md)

## <a name="create-an-alias-record-for-zone-apex"></a>Creare un record alias per l'apice della zona

1. Aprire la configurazione DNS di **Azure** per il dominio da eseguire l'onboarding.
2. Creare o modificare il record per l'apice della zona.
3. Selezionare il **tipo di** record come _Record A,_ quindi selezionare _Sì_ per Set di **record alias**. **Il tipo di alias** deve essere impostato su _Risorsa di Azure._
4. Scegliere la sottoscrizione di Azure in cui è ospitato il profilo Front Door e quindi selezionare la risorsa Porta frontale dall'elenco a discesa Risorsa di Azure.Choose the Azure subscription where your Front Door profile is hosted and then select the Front Door resource from the **Azure resource** dropdown.
5. Fare clic **su OK** per inviare le modifiche.

    ![Record alias per l'apice della zona](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Il passaggio precedente creerà un record di apice di zona che punta alla risorsa Front `afdverify.contosonews.com`Door `afdverify.<name>.azurefd.net` e anche un mapping di record CNAME 'afdverify' (esempio - ) a cui verrà utilizzato per l'onboarding del dominio sul profilo Front Door.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>A bordo del dominio personalizzato sulla porta anteriore

1. Nella scheda della finestra di progettazione della porta anteriore, fare clic sull'icona '' nella sezione Host front-end per aggiungere un nuovo dominio personalizzato.
2. Immettere il nome di dominio radice o apice nel campo del nome host personalizzato, ad esempio `contosonews.com`.
3. Una volta convalidato il mapping CNAME dal dominio alla porta anteriore, fare clic su **Aggiungi** per aggiungere il dominio personalizzato.
4. Fare clic su **Salva** per inviare le modifiche.

![Menu del dominio personalizzato](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Abilitare HTTPS nel dominio personalizzatoEnable HTTPS on your custom domain

1. Fare clic sul dominio personalizzato aggiunto e nella sezione **Dominio personalizzato HTTPS**modificare lo stato in **Abilitato**.
2. Selezionare il **tipo di gestione certificati** per _'Usa certificato personale'_.

> [!WARNING]
> Il tipo di gestione dei certificati gestiti Front Door non è attualmente supportato per i domini apice o radice. L'unica opzione disponibile per abilitare HTTPS in un apice o un dominio radice per Front Door consiste nell'usare il proprio certificato TLS/SSL personalizzato ospitato in Azure Key Vault.The only option available for enabling HTTPS on an apex or root domain for Front Door is using your own custom TLS/SSL certificate hosted on Azure Key Vault.

3. Assicurarsi di disporre delle autorizzazioni appropriate per Front Door per accedere alla chiave Vault come indicato nell'interfaccia utente, prima di procedere al passaggio successivo.
4. Scegliere un **account Key Vault** dall'abbonamento corrente, quindi selezionare la versione **segreta** e **segreta** appropriata per eseguire il mapping al certificato corretto.
5. Fare clic su **Aggiorna** per salvare la selezione e quindi fare clic su **Salva**.
6. Fare clic su **Aggiorna** dopo un paio di minuti e quindi fare di nuovo clic sul dominio personalizzato per visualizzare l'avanzamento del provisioning dei certificati. 

> [!WARNING]
> Assicurarsi di aver creato regole di routing appropriate per il dominio apice o di aver aggiunto il dominio alle regole di routing esistenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).