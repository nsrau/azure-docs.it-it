---
title: Accedere e personalizzare il portale per sviluppatori gestiti-gestione API di Azure | Microsoft Docs
description: Informazioni su come usare la versione gestita del portale per sviluppatori in gestione API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 28ef082d6aac5557998f7b67443d4dafd3a0fb15
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285588"
---
# <a name="access-and-customize-developer-portal"></a>Accedere e personalizzare il portale per sviluppatori

Il portale per sviluppatori è un sito Web completamente personalizzabile e completamente personalizzabile con la documentazione delle API. Si tratta del punto in cui gli utenti dell'API possono individuare le API, informazioni su come usarle e richiedere l'accesso.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Accedere alla versione gestita del portale per sviluppatori
> * Esplorare l'interfaccia amministrativa
> * Personalizzare il contenuto
> * Pubblicare le modifiche
> * Visualizzare il portale pubblicato

Per ulteriori informazioni, vedere il portale per sviluppatori nel [portale per sviluppatori di gestione API di Azure](api-management-howto-developer-portal.md).

![Portale per sviluppatori di gestione API-modalità di amministrazione](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
- Importare e pubblicare un'istanza di Gestione API di Azure. Per altre informazioni, vedere [importare e pubblicare](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Accedere al portale come amministratore

Attenersi alla procedura seguente per accedere alla versione gestita del portale.

1. Passare all'istanza del servizio gestione API nella portale di Azure.
1. Fare clic sul pulsante **portale per sviluppatori** nella barra di spostamento in alto. Viene aperta una nuova scheda del browser con una versione amministrativa del portale.

## <a name="understand-the-portals-administrative-interface"></a>Informazioni sull'interfaccia amministrativa del portale

### <a name="default-content"></a>Contenuto predefinito 

Se si accede al portale per la prima volta, verrà eseguito automaticamente il provisioning del contenuto predefinito in background. Il contenuto predefinito è stato progettato per mostrare le funzionalità del portale e ridurre al minimo la quantità di personalizzazioni necessarie per personalizzare il portale. Per altre informazioni su ciò che è incluso nel contenuto del portale, vedere [Panoramica del portale per sviluppatori di gestione API di Azure](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Editor visivo

È possibile personalizzare il contenuto del portale con l'editor visivo. Le sezioni di menu a sinistra consentono di creare o modificare pagine, elementi multimediali, layout, menu, stili o impostazioni del sito Web. Le voci di menu nella parte inferiore consentono di passare da un viewport all'altra (ad esempio, mobile o desktop), visualizzare gli elementi del portale visibili agli utenti autenticati o anonimi oppure salvare o annullare le azioni.

È possibile aggiungere righe a una pagina facendo clic su un'icona blu con un segno più. È possibile aggiungere widget (ad esempio, testo, immagini o elenco di API) premendo un'icona grigia con un segno più. È possibile ridisporre gli elementi in una pagina con l'interazione con trascinamento della selezione. 

### <a name="layouts-and-pages"></a>Layout e pagine

![Pagine e layout](media/api-management-howto-developer-portal-customize/pages-layouts.png)

I layout definiscono la modalità di visualizzazione delle pagine. Nel contenuto predefinito, ad esempio, sono presenti due layout: uno si applica al home page e l'altro a tutte le pagine rimanenti.

Un layout viene applicato a una pagina abbinando il relativo modello URL all'URL della pagina. Ad esempio, il layout con un modello di URL di `/wiki/*` verrà applicato a ogni pagina con `/wiki/` segmento nell'URL: `/wiki/getting-started`, `/wiki/styles`e così via.

Nell'immagine precedente, il contenuto appartenente al layout è contrassegnato come blu, mentre la pagina è contrassegnata in rosso. Le sezioni di menu sono contrassegnate rispettivamente.

### <a name="styling-guide"></a>Guida dello stile

![Guida dello stile](media/api-management-howto-developer-portal-customize/styling-guide.png)

La Guida di stile è un pannello creato con le finestre di progettazione. Consente di supervisionare e applicare lo stile a tutti gli elementi visivi nel portale. Lo stile è gerarchico. molti elementi ereditano le proprietà da altri elementi. Gli elementi Button, ad esempio, usano i colori per il testo e lo sfondo. Per modificare il colore di un pulsante, è necessario modificare la variante di colore originale.

Per modificare una variante, fare clic su di essa e selezionare l'icona a matita visualizzata sopra. Dopo aver apportato le modifiche nella finestra popup, chiuderla.

### <a name="save-button"></a>Pulsante Salva

![Pulsante Salva](media/api-management-howto-developer-portal-customize/save-button.png)

Ogni volta che si apportano modifiche nel portale, è necessario salvarle manualmente premendo il pulsante **Salva** nel menu in basso. Quando si salvano le modifiche, il contenuto modificato viene caricato automaticamente nel servizio gestione API.

## <a name="customize-the-portals-content"></a>Personalizzare il contenuto del portale

Prima di rendere disponibile il portale per i visitatori, è necessario personalizzare il contenuto generato automaticamente. Le modifiche consigliate includono i layout, gli stili e il contenuto del home page.

> [!NOTE]
> A causa di considerazioni sull'integrazione, le pagine seguenti non possono essere rimosse o spostate in un URL diverso: `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef``/publish`, `/signin`, `/signin-sso`, `/signup`.

### <a name="home-page"></a>Home page

La **Home** page predefinita viene riempita con contenuto fittizio. È possibile rimuovere tutte le sezioni con il contenuto o mantenere la struttura e modificare gli elementi uno alla volta. Sostituire il testo e le immagini generati con il proprio e assicurarsi che i collegamenti puntino alle posizioni desiderate.

### <a name="layouts"></a>Layout

Sostituire il logo generato automaticamente nella barra di spostamento con la propria immagine.

### <a name="styling"></a>Stili

Sebbene non sia necessario modificare alcuno stile, è possibile modificare determinati elementi. Ad esempio, modificare il colore primario in modo che corrisponda al colore del marchio.

### <a name="customization-example"></a>Esempio di personalizzazione

Nel video seguente viene illustrato come modificare il contenuto del portale, personalizzare l'aspetto del sito Web e pubblicare le modifiche.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a>Pubblicare il portale

Per rendere disponibili il portale e le modifiche più recenti ai visitatori, è necessario pubblicarlo.

1. Assicurarsi di aver salvato le modifiche facendo clic sull'icona **Salva** .
1. Fare clic su **Pubblica sito Web** nella sezione **operazioni** del menu. Questa operazione può richiedere qualche minuto.  
    portale di pubblicazione ![](media/api-management-howto-developer-portal-customize/publish-portal.png)

## <a name="visit-the-published-portal"></a>Visita il portale pubblicato

Dopo aver pubblicato il portale, è possibile accedervi con lo stesso URL del pannello amministrativo, ad esempio `https://contoso-api.developer.azure-api.net`. Visualizzarlo in una sessione del browser separata (modalità di esplorazione in incognito/privata) come visitatore esterno.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)
