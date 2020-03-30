---
title: Accesso e personalizzazione del portale per sviluppatori gestiti - Gestione API di Azure Documenti Microsoft
description: Informazioni su come usare la versione gestita del portale per sviluppatori in Gestione API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244095"
---
# <a name="access-and-customize-developer-portal"></a>Accesso e personalizzazione del portale per sviluppatori

Il portale per sviluppatori è un sito Web generato automaticamente e completamente personalizzabile con la documentazione delle API. È dove i consumer di API possono individuare le API, imparare a usarle e richiedere l'accesso.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Accedere alla versione gestita del portale per sviluppatori
> * Navigare nella sua interfaccia amministrativa
> * Personalizzare il contenuto
> * Pubblicare le modifiche
> * Visualizzare il portale pubblicato

Per ulteriori informazioni sul portale per sviluppatori, [vedere Panoramica](api-management-howto-developer-portal.md)del portale per sviluppatori di Gestione API di Azure.You can find more details on the developer portal in the Azure API Management developer portal overview .

![Portale per sviluppatori di Gestione API - modalità amministratore](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza](get-started-create-service-instance.md) di Gestione API di AzureComplete the following quickstart: Create an Azure API Management instance
- Importare e pubblicare un'istanza di Gestione API di Azure. Per ulteriori informazioni, vedere [Importare e pubblicare](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Accedere al portale come amministratore

Attenersi alla procedura seguente per accedere alla versione gestita del portale.

1. Passare all'istanza del servizio Gestione API nel portale di Azure.Go to your API Management service instance in the Azure portal.
1. Fare clic sul pulsante **Portale per sviluppatori** nella barra di spostamento superiore. Si aprirà una nuova scheda del browser con una versione amministrativa del portale.

## <a name="understand-the-portals-administrative-interface"></a>Comprendere l'interfaccia amministrativa del portale

### <a name="default-content"></a>Contenuto predefinito 

Se si accede al portale per la prima volta, il provisioning del contenuto predefinito verrà eseguito automaticamente in background. Il contenuto predefinito è stato progettato per mostrare le funzionalità del portale e ridurre al minimo la quantità di personalizzazioni necessarie per personalizzare il portale. Per altre informazioni sugli elementi inclusi nel contenuto del [portale, vedere Panoramica](api-management-howto-developer-portal.md)del portale Di Gestione API di Azure.You can learn more about what is included in the portal content in the Azure API Management developer portal overview .

### <a name="visual-editor"></a>Editor visivo

È possibile personalizzare il contenuto del portale con l'editor visivo. Le sezioni dei menu a sinistra consentono di creare o modificare pagine, elementi multimediali, layout, menu, stili o impostazioni del sito Web. Le voci di menu nella parte inferiore consentono di passare da una finestra all'altra, ad esempio dispositivi mobili o desktop, visualizzare gli elementi del portale visibili agli utenti autenticati o anonimi oppure salvare o annullare azioni.

È possibile aggiungere righe a una pagina facendo clic su un'icona blu con un segno più. I widget (ad esempio, testo, immagini o elenco API) possono essere aggiunti premendo un'icona grigia con un segno più. È possibile ridisporre gli elementi in una pagina con l'interazione di trascinamento della selezione. 

### <a name="layouts-and-pages"></a>Layout e pagine

![Pagine e layout](media/api-management-howto-developer-portal-customize/pages-layouts.png)

I layout definiscono la modalità di visualizzazione delle pagine. Ad esempio, nel contenuto predefinito sono disponibili due layout: uno si applica alla home page e l'altro a tutte le pagine rimanenti.

Un layout viene applicato a una pagina facendo corrispondere il relativo modello di URL all'URL della pagina. Ad esempio, il layout `/wiki/*` con un modello di `/wiki/` URL di verrà `/wiki/getting-started` `/wiki/styles`applicato a ogni pagina con il segmento nell'URL: , , e così via.

Nell'immagine precedente, il contenuto appartenente al layout è contrassegnato in blu, mentre la pagina è contrassegnata in rosso. Le sezioni del menu sono contrassegnate rispettivamente.

### <a name="styling-guide"></a>Guida allo stile

![Guida allo stile](media/api-management-howto-developer-portal-customize/styling-guide.png)

Guida di stile è un pannello creato con i progettisti in mente. Consente di supervisionare e applicare stili a tutti gli elementi visivi nel portale. Lo stile è gerarchico: molti elementi ereditano proprietà da altri elementi. Ad esempio, gli elementi pulsante utilizzano colori per il testo e lo sfondo. Per modificare il colore di un pulsante, è necessario modificare la variante di colore originale.

Per modificare una variante, fare clic su di essa e selezionare l'icona a forma di matita che appare sopra di essa. Dopo aver apportato le modifiche nella finestra popup, chiuderla.

### <a name="save-button"></a>Pulsante per il salvataggio

![Pulsante per il salvataggio](media/api-management-howto-developer-portal-customize/save-button.png)

Ogni volta che si apporta una modifica nel portale, è necessario salvarla manualmente premendo il pulsante **Salva** nel menu in basso. Quando si salvano le modifiche, il contenuto modificato viene caricato automaticamente nel servizio Gestione API.

## <a name="customize-the-portals-content"></a>Personalizzare il contenuto del portale

Prima di rendere il portale disponibile per i visitatori, è necessario personalizzare i contenuti generati automaticamente. Le modifiche consigliate includono i layout, gli stili e il contenuto della home page.

> [!NOTE]
> Per considerazioni sull'integrazione, le pagine seguenti non possono `/404` `/500`essere `/captcha` `/change-password`rimosse o spostate in un URL diverso: , , , `/config.json`, `/confirm/invitation` `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, , `/internal-status-0123456789abcdef`, , `/publish`, `/signin`, `/signin-sso`, `/signup`, .

### <a name="home-page"></a>Home page

La **home page** predefinita è piena di contenuto fittizio. È possibile rimuovere tutte le sezioni con il contenuto o mantenere la struttura e regolare gli elementi uno per uno. Sostituire il testo e le immagini generati con i propri e assicurarsi che i collegamenti puntino alle posizioni desiderate.

### <a name="layouts"></a>Layout

Sostituire il logo generato automaticamente nella barra di navigazione con la propria immagine.

### <a name="styling"></a>Stile

Anche se non è necessario regolare alcun ostistili, è consigliabile regolare particolari elementi. Ad esempio, modifica il colore principale in modo che corrisponda al colore del tuo brand.

### <a name="customization-example"></a>Esempio di personalizzazione

Nel video seguente viene illustrato come modificare il contenuto del portale, personalizzare l'aspetto del sito Web e pubblicare le modifiche.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Pubblicare il portale

Per rendere disponibile ai visitatori il portale e le modifiche più recenti, è necessario pubblicarlo.

1. Assicurati di aver salvato le modifiche facendo clic sull'icona **Salva.**
1. Fare clic su **Pubblica sito Web** nella sezione **Operazioni** del menu. Questa operazione può richiedere qualche minuto.  
    ![Pubblica portale](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> Il portale deve essere ripubblicato dopo le modifiche alla configurazione del servizio Gestione API, ad esempio l'assegnazione di un dominio personalizzato, l'aggiornamento dei provider di identità, l'impostazione della delega, la specifica dei termini di accesso e di prodotto e altro ancora.

## <a name="visit-the-published-portal"></a>Visita il portale pubblicato

Dopo aver pubblicato il portale, è possibile accedervi allo stesso `https://contoso-api.developer.azure-api.net`URL del pannello amministrativo, ad esempio . Visualizzarlo in una sessione separata del browser (modalità di navigazione in incognito / privata) come visitatore esterno.

## <a name="apply-the-cors-policy-on-apis"></a>Applicare il criterio CORS alle API

È necessario abilitare CORS (condivisione di risorse tra origini) nelle API per consentire ai visitatori del portale di testare le API tramite la console interattiva incorporata. Fare riferimento a [questo articolo della documentazione](api-management-howto-developer-portal.md#cors) per ulteriori dettagli.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)
