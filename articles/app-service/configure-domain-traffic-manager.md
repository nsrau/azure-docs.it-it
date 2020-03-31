---
title: Configurare i nomi DNS con Gestione traffico
description: Informazioni su come configurare un dominio personalizzato per un'app del servizio app di Azure che si integra con Gestione traffico per il bilanciamento del carico.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944154"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Configurare un nome di dominio personalizzato nel servizio app di Azure con l'integrazione di Gestione trafficoConfigure a custom domain name in Azure App Service with Traffic Manager integration

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Per i servizi cloud, vedere [Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure](../cloud-services/cloud-services-custom-domain-name.md).

Quando si usa [Gestione traffico](/azure/traffic-manager/) di Azure per bilanciare il carico del traffico verso il servizio app di [Azure,](overview.md)è possibile accedere all'app del servizio app tramite ** \<>.trafficmanager.net.** Puoi assegnare un nome di\.dominio personalizzato, ad esempio www contoso.com, con l'app del servizio app per fornire un nome di dominio più riconoscibile per gli utenti.

Questo articolo illustra come configurare un nome di dominio personalizzato con un'app del servizio app integrata con [Gestione traffico.](../traffic-manager/traffic-manager-overview.md)

> [!NOTE]
> Solo i record [CNAME](https://en.wikipedia.org/wiki/CNAME_record) sono supportati quando si configura un nome di dominio utilizzando l'endpoint di Gestione traffico. Poiché i record A non sono supportati, non è supportato un mapping del dominio radice, ad esempio contoso.com.
> 

## <a name="prepare-the-app"></a>Preparare l'app

Per eseguire il mapping di un nome DNS personalizzato a un'app integrata con Gestione traffico di Azure, il piano di [servizio app](https://azure.microsoft.com/pricing/details/app-service/) dell'app Web deve essere nel livello **Standard** o superiore. In questo passaggio, verificare che l''app del servizio app si trovi nel piano tariffario supportato.

### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario

Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **App Services**.

Nella pagina **Servizi app** selezionare il nome dell'app di Azure.

![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/select-app.png)

Nel riquadro di spostamento sinistro della pagina dell'app selezionare **Scalabilità verticale (piano di servizio app)**.

![Menu di scalabilità verticale](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Il livello corrente dell'app è evidenziato da un bordo blu. Verificare che l'app si trova nel livello **Standard** o superiore (qualsiasi livello nella categoria **Produzione** o **isolato).** In caso [affermativo,](#create-the-cname-mapping)chiudere la pagina **Scalabilità verticale** e passare a Crea il mapping CNAME .

![Controllare il piano tariffario](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Passare a un piano di servizio app di livello superiore

Se è necessario aumentare la scalabilità dell'app, selezionare uno dei piani tariffari nella categoria **Produzione.** Per altre opzioni, fare clic su **Visualizza opzioni aggiuntive**.

Fare clic su **Applica**.

## <a name="create-traffic-manager-endpoint"></a>Creare un endpoint di Gestione trafficoCreate Traffic Manager endpoint

Seguendo la procedura descritta in [Aggiungere o eliminare endpoint,](../traffic-manager/traffic-manager-endpoints.md)aggiungere l'app del servizio app come endpoint nel profilo di Gestione traffico.

Quando l'app del servizio app è in un piano tariffario supportato, viene visualizzata nell'elenco delle destinazioni del servizio app disponibili quando si aggiunge l'endpoint. Se l'app non è presente nell'elenco, [verifica il piano tariffario dell'app.](#prepare-the-app)

## <a name="create-the-cname-mapping"></a>Creare il mapping CNAME
> [!NOTE]
> Per configurare un [dominio del servizio app acquistato,](manage-custom-dns-buy-domain.md)ignorare questa sezione e passare a Abilita dominio [personalizzato](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Sebbene le specifiche di ogni provider di dominio varino, si esegue il mapping *dal* nome di dominio personalizzato (ad esempio **contoso.com**) *al* nome di dominio di Gestione traffico (**contoso.trafficmanager.net**) integrato con l'app.

> [!NOTE]
> Se un record è già in uso ed è necessario associare le app in modalità preemptive, è possibile creare un altro record CNAME. Ad esempio, per associare preventivamente **www\.contoso.com** all'app, creare un record CNAME da **awverify.www** a **contoso.trafficmanager.net**. È quindi possibile\.aggiungere "www contoso.com" all'app senza la necessità di modificare il record CNAME "www". Per altre informazioni, vedere Eseguire la migrazione di un nome DNS attivo al servizio app di Azure.For more information, [see Migrate an active DNS name to Azure App Service.](manage-custom-dns-migrate-domain.md)

Dopo aver completato l'aggiunta o la modifica di record DNS presso il provider di dominio, salvare le modifiche.

## <a name="enable-custom-domain"></a>Abilitare il dominio personalizzatoEnable custom domain
Dopo la propagazione dei record per il nome di dominio, usa il browser per verificare che il nome di dominio personalizzato venga risolto nell'app del servizio app.

> [!NOTE]
> La propagazione del record CNAME in tutto il sistema DNS può richiedere tempo. È possibile utilizzare un <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> servizio, ad esempio per verificare che il CNAME sia disponibile.
> 
> 

1. Una volta completata la risoluzione del dominio, per tornare alla pagina dell'app nel portale di [AzureOnce](https://portal.azure.com) domain resolution succeeds, to back to your app page in the Azure Portal
2. Nel riquadro di spostamento sinistro selezionare **Domini** > personalizzati**Aggiungi nome host**.
4. Digitare il nome di dominio personalizzato di cui è stato eseguito il mapping in precedenza e selezionare **Convalida**.
5. Assicurarsi che **il tipo di record Nome host** sia impostato su **CNAME (www\.example.com o qualsiasi sottodominio)**.

6. Poiché l'app del servizio app è ora integrata con un endpoint di Gestione traffico, il nome di dominio di Gestione traffico dovrebbe essere visualizzato in **Configurazione CNAME**. Selezionarlo e fare clic su **Aggiungi dominio personalizzato**.

    ![Aggiunta del nome DNS all'app](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Proteggere un nome DNS personalizzato con un binding SSL nel Servizio app di Azure](configure-ssl-bindings.md)
