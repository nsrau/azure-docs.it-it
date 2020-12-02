---
title: Esercitazione - Accedere al portale per sviluppatori e personalizzarlo - Gestione API di Azure | Microsoft Docs
description: Seguire questa esercitazione per apprendere come personalizzare il portale per sviluppatori di Gestione API, un sito Web generato automaticamente e completamente personalizzabile con la documentazione delle API.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012979"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Esercitazione: Accedere al portale per sviluppatori e personalizzarlo

Il *portale per sviluppatori* è un sito Web generato automaticamente e completamente personalizzabile con la documentazione delle API. È la posizione in cui i consumer delle API possono trovare le API, imparare a usarle e richiederne l'accesso.

In questa esercitazione viene illustrato come:

> [!div class="checklist"]
> * Accedere alla versione gestita del portale per sviluppatori
> * Esplorare l'interfaccia di amministrazione del portale
> * Personalizzare il contenuto
> * Pubblicare le modifiche
> * Visualizzare il portale pubblicato

Per altre informazioni sul portale per sviluppatori, vedere [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="Portale per sviluppatori di Gestione API - modalità amministratore" border="false":::

## <a name="prerequisites"></a>Prerequisiti

- Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
- Importare e pubblicare un'istanza di Gestione API di Azure. Per altre informazioni, vedere [Importare e pubblicare la prima API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Accedere al portale come amministratore

Seguire questa procedura per accedere alla versione gestita del portale.

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. Selezionare il pulsante **Portale per sviluppatori** nella barra di spostamento superiore. Si aprirà una nuova scheda del browser con una versione amministrativa del portale.

## <a name="understand-the-portals-administrative-interface"></a>Informazioni sull'interfaccia di amministrazione del portale

### <a name="default-content"></a>Contenuto predefinito 

Se si accede al portale per la prima volta, il contenuto predefinito viene fornito automaticamente in background. Il contenuto predefinito è stato progettato per presentare le funzionalità del portale e ridurre al minimo le modifiche necessarie per personalizzare il portale. Per altre informazioni su ciò che è incluso nel contenuto del portale, vedere [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Editor visivo

È possibile personalizzare il contenuto del portale con l'editor visivo. 
* Le sezioni di menu a sinistra consentono di creare o modificare pagine, elementi multimediali, layout, menu, stili o impostazioni del sito Web. 
* Le voci di menu in basso consentono di passare da un riquadro di visualizzazione all'altro (ad esempio, dispositivo mobile o desktop), visualizzare gli elementi del portale visibili agli utenti autenticati o anonimi oppure salvare o annullare azioni.
* È possibile aggiungere righe a una pagina facendo clic su un'icona blu con un segno più. 
* È possibile aggiungere widget (ad esempio testo, immagini o un elenco di API) selezionando un'icona grigia con un segno più.
* È possibile ridisporre gli elementi in una pagina tramite trascinamento della selezione. 

### <a name="layouts-and-pages"></a>Layout e pagine

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Pagine e layout" border="false":::

I layout definiscono il modo in cui le pagine vengono visualizzate. Ad esempio, nel contenuto predefinito sono presenti due layout: uno si applica alla home page e l'altro a tutte le pagine rimanenti.

Un layout viene applicato a una pagina abbinando il relativo modello di URL all'URL della pagina. Ad esempio, un layout con un modello di URL `/wiki/*` viene applicato a ogni pagina che contiene il segmento `/wiki/` nell'URL: `/wiki/getting-started`, `/wiki/styles` e così via.

Nell'immagine riportata sopra, il contenuto appartenente al layout è contrassegnato in blu, mentre la pagina è contrassegnata in rosso. Le sezioni di menu sono contrassegnate rispettivamente in blu e rosso.

### <a name="styling-guide"></a>Guida per l'applicazione di stili

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Guida per l'applicazione di stili" border="false":::

La guida per l'applicazione di stili è un pannello creato a beneficio dei progettisti. Consente di controllare e applicare lo stile a tutti gli elementi visivi nel portale. L'applicazione dello stile è gerarchica: molti elementi ereditano le proprietà da altri elementi. Ad esempio, gli elementi Button usano i colori per il testo e lo sfondo. Per cambiare il colore di un pulsante, è necessario modificare la variante di colore originale.

Per modificare una variante, selezionarla e quindi selezionare l'icona a forma di matita visualizzata sopra di essa. Dopo avere apportato le modifiche nella finestra popup, chiuderla.

### <a name="save-button"></a>Pulsante per il salvataggio

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Pulsante per il salvataggio" border="false":::

Ogni volta che si apportano modifiche nel portale, è necessario salvarle manualmente selezionando il pulsante **Salva** nel menu in basso oppure premendo [CTRL]+[S]. Quando si salvano le modifiche, il contenuto modificato viene caricato automaticamente nel servizio Gestione API.

## <a name="customize-the-portals-content"></a>Personalizzare il contenuto del portale

Prima di rendere il portale disponibile per i visitatori, è opportuno personalizzare il contenuto generato automaticamente. Le modifiche consigliate includono i layout, gli stili e il contenuto della home page.

> [!NOTE]
> Per motivi di integrazione, le pagine seguenti non possono essere rimosse o spostate sotto un URL diverso: `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, `/signin`, `/signin-sso`, `/signup`.

### <a name="home-page"></a>Home page

La **home page** è compilata con contenuti segnaposto. È possibile rimuovere intere sezioni contenenti questi contenuti oppure mantenere la struttura e modificare gli elementi uno alla volta. Sostituire il testo e le immagini generati con testo e immagini personalizzati e assicurarsi che i collegamenti puntino alle posizioni desiderate.

### <a name="layouts"></a>Layout

Sostituire il logo generato automaticamente nella barra di spostamento con l'immagine del proprio logo.

### <a name="styling"></a>Stile

Sebbene non sia necessario modificare gli stili, si possono modificare determinati elementi. Ad esempio, è possibile cambiare il colore primario in modo che corrisponda al colore del proprio marchio.

### <a name="customization-example"></a>Esempio di personalizzazione

Il video seguente illustra come modificare il contenuto del portale, personalizzare l'aspetto del sito Web e pubblicare le modifiche.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Pubblicare il portale

Per rendere il portale e le modifiche più recenti disponibili per i visitatori, è necessario *pubblicarlo*. È possibile pubblicare il portale all'interno della relativa interfaccia di amministrazione o dal portale di Azure.

### <a name="publish-from-the-administrative-interface"></a>Pubblicare il portale dall'interfaccia di amministrazione

1. Assicurarsi di salvare le modifiche selezionando l'icona **Salva**.
1. Nella sezione **Operazioni** del menu selezionare **Pubblica sito Web**. Questa operazione può richiedere qualche minuto.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Pubblicare il portale" border="false":::

### <a name="publish-from-the-azure-portal"></a>Pubblicare il portale dal portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. Nel menu a sinistra, in **Portale per sviluppatori**, selezionare **Panoramica del portale**.
1. Nella finestra **Panoramica del portale** selezionare **Pubblica**.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Pubblicare il portale dal portale di Azure":::

> [!NOTE]
> Il portale deve essere ripubblicato ogni volta che vengono apportate modifiche alla configurazione del servizio Gestione API. Ad esempio, ripubblicare il portale dopo l'assegnazione di un dominio personalizzato, l'aggiornamento dei provider di identità, l'impostazione della delega o la specifica delle condizioni per l'accesso e il prodotto.


## <a name="visit-the-published-portal"></a>Visitare il portale pubblicato

Dopo aver pubblicato il portale, è possibile accedervi allo stesso URL del pannello di amministrazione, ad esempio `https://contoso-api.developer.azure-api.net`. Visualizzarlo in una sessione del browser separata (in modalità privata o anonima) come visitatore esterno.

## <a name="apply-the-cors-policy-on-apis"></a>Applicare i criteri CORS alle API

Per consentire ai visitatori del portale di testare le API tramite la console interattiva integrata, abilitare la condivisione di risorse tra le origini (CORS) sulle API. Per informazioni dettagliate, vedere [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md#cors).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)
- [Eseguire la migrazione al nuovo portale per sviluppatori](developer-portal-deprecated-migration.md) dal portale legacy deprecato.