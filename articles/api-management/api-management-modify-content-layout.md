---
title: Modificare i contenuti della pagina nel portale per sviluppatori in Gestione API di Azure | Microsoft Docs
description: Informazioni su come modificare i contenuti di una pagina nel portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modificare il contenuto e il layout delle pagine nel portale per sviluppatori in Gestione API di Azure
Per personalizzare il portale per sviluppatori in Gestione API di Azure ci sono tre modalità principali:

* [Modificare il contenuto di pagine statiche e gli elementi di layout di pagina][modify-content-layout] (descritto in questa guida)
* [Aggiornare gli stili usati per gli elementi della pagina nel portale per sviluppatori][customize-styles]
* [Modificare i modelli usati per le pagine generate dal portale][portal-templates] (ad esempio documenti API, prodotti, autenticazione utente e così via)

## <a name="page-structure"></a>Struttura delle pagine del portale per sviluppatori

Il portale per sviluppatori è basato su un sistema di gestione dei contenuti. Il layout delle pagine viene creato in base al set di elementi di pagine di piccole dimensioni noto come widget:

![Struttura della pagina del portale per sviluppatori][api-management-customization-widget-structure]

Tutti i widget sono modificabili. 
* I contenuti principali specifici per ogni singola pagina si trovano nel widget "Contenuto". La modifica di una pagina comporta la modifica del contenuto di questo widget.
* Tutti gli elementi di layout della pagina sono contenuti negli altri widget. Le modifiche apportate a questi widget verranno applicate a tutte le pagine. Si farà riferimento a questi con il termine "widget di layout".

Nella modifica quotidiana di una pagina in genere viene modificato solo il widget Contenuto che avrà un contenuto diverso per ogni pagina.

## <a name="modify-layout-widget"></a>Modifica dei contenuti di un widget di layout

È possibile modificare il contenuto nel portale per sviluppatori tramite il portale di pubblicazione, accessibile tramite il Portale di Azure. Per accedervi, fare clic su **Publisher portal** (Portale di pubblicazione) nella barra degli strumenti del servizio dell'istanza di Gestione API.

![Portale di pubblicazione][api-management-management-console]

Per modificare i contenuti del widget, fare clic su **Widget** dal menu **Portale per sviluppatori** a sinistra. In questo esempio verranno modificati i contenuti del widget Intestazione. Selezionare il widget **Intestazione** dall'elenco.

![Widget Intestazione][api-management-widgets-header]

I contenuti dell'intestazione sono modificabili dal campo **Corpo** . Modificare il testo come desiderato e quindi fare clic su **Salva** nella parte inferiore della pagina.

A questo punto, la nuova intestazione dovrebbe essere visualizzata in ogni pagina del portale per sviluppatori.

> Per aprire il **portale per sviluppatori** dal portale di pubblicazione, fare clic sull'opzione corrispondente sulla barra superiore.
> 
> 

## <a name="edit-page-contents"></a>Modificare i contenuti di una pagina

Per visualizzare l'elenco di tutte le pagine di contenuto esistenti, nel portale di pubblicazione scegliere **Contenuto** dal menu **Portale per sviluppatori**.

![Gestione del contenuto][api-management-customization-manage-content]

Fare clic nella pagina di **benvenuto** per modificare gli elementi visualizzati nella home page del portale per sviluppatori. Apportare le modifiche desiderate, visualizzarle in anteprima, se necessario, e quindi fare clic su **Pubblica** per renderle visibili a tutti.

> Per la home page viene usato un layout speciale che consente di visualizzare un banner nella parte superiore. Questo banner non è modificabile dalla sezione **Contenuto** . Per modificare il banner, scegliere **Widget** dal menu **Portale per sviluppatori**, selezionare **Home page** dall'elenco a discesa **Current Layer** (Livello corrente) e quindi aprire l'elemento **Banner** nella sezione **Featured section** (In primo piano). I contenuti di questo banner sono modificabili come quelli di ogni altra pagina.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Aggiornare gli stili usati per gli elementi della pagina nel portale per sviluppatori][customize-styles]
* [Modificare i modelli usati per le pagine generate dal portale][portal-templates] (ad esempio documenti API, prodotti, autenticazione utente e così via)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
