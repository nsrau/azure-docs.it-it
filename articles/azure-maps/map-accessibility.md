---
title: Creazione di un'applicazione accessibile con Mappe di Azure | Microsoft Docs
description: Come compilare un'applicazione accessibile con Mappe di Azure
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 2523287669628b8c58028cae9887743c20b6bc5e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659331"
---
# <a name="building-an-accessible-application"></a>Compilazione di un'applicazione accessibile

Questo articolo illustra come compilare un'applicazione per le mappe che può essere usata da un'utilità per la lettura dello schermo.

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='500' scrolling='no' title='Creare un'applicazione accessibile' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Make an accessible application</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

La mappa viene precompilata con alcune funzionalità di accessibilità. Un utente può spostarsi nella mappa usando la tastiera e, se è in esecuzione un'utilità per la lettura dello schermo, la mappa notificherà all'utente le modifiche dello stato. L'utente, ad esempio, riceverà una notifica relativa alla nuova latitudine, longitudine, zoom e località quando viene fatta una panoramica o lo zoom della mappa. A eventuali informazioni aggiuntive inserite nella mappa di base devono corrispondere informazioni testuali per gli utenti dell'utilità di lettura dello schermo. A questo scopo, è possibile usare [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest). Nell'esempio di ricerca precedente viene aggiunto alla mappa un popup con informazioni testuali per ogni segnaposto inserito nella mappa. Il metodo attach di [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) consente al popup di essere individuato da un'utilità di lettura dello schermo senza visualizzare il popup nella mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla classe Popup e sui metodi usati in questo articolo, vedere:

* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [attach](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [remove](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Per altri scenari di mapping, vedere la [pagina dell'esempio di codice](http://aka.ms/AzureMapsSamples).
