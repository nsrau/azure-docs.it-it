---
title: 'Informazioni di riferimento: ritiro immagini Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni dettagliate sui ritiri che interessano il Data Science Virtual Machine di Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001621"
---
# <a name="reference-retirements-of-dsvm-images"></a>Informazioni di riferimento: ritiro di immagini DSVM

Di seguito viene illustrato il ritiro (deprecazione) delle immagini e i suggerimenti per la gestione dei prossimi ritiri nel Data Science Virtual Machine di Azure.

## <a name="why-we-retire-dsvm-images"></a>Perché ritirare le immagini DSVM

Il Data Science Virtual Machine dispone attualmente di due edizioni:

* Ubuntu
* Windows Server

L'immagine DSVM per queste edizioni è basata su una versione specifica del sistema operativo, ad esempio Ubuntu 18,04 LTS. Nel corso del tempo, la finestra di manutenzione per la _versione_ del sistema operativo terminerà e/o gli strumenti di Data Science non supporteranno più le versioni precedenti del sistema operativo. Per rendere aggiornata l'immagine di DSVM con i sistemi operativi più recenti e data science gli strumenti, viene rilasciata una nuova immagine di DSVM basata su versioni del sistema operativo più recenti.

## <a name="how-we-retire-dsvm-images"></a>Modalità di ritiro delle immagini DSVM

Viene emesso un _annuncio di ritiro_ per cui gli utenti di DSVM esistenti ricevono una notifica (tramite posta elettronica) del ritiro imminente di un'immagine di DSVM. L'annuncio del ritiro indica in dettaglio la _Data di ritiro_ (dopo questa data, l'immagine non è disponibile) e le raccomandazioni relative alla mitigazione, ad esempio l'aggiornamento a un'immagine DSVM più recente.

Nella data di _annuncio_ verrà nascosta l'immagine nel Marketplace in modo che:

1. ai nuovi utenti viene impedito di eseguire inavvertitamente il provisioning di un'immagine DSVM ritirata.
2. Gli utenti esistenti possono usare le distribuzioni ARM fino alla data di ritiro.

L'immagine nascosta riceverà le patch del sistema operativo fino alla _Data di ritiro_ ma __non__ riceverà gli aggiornamenti per gli strumenti e i Framework Data Science. Nella _Data di ritiro_, l'immagine non sarà disponibile per le distribuzioni ARM.

Tutte le immagini DSVM di cui è stato effettuato il provisioning nella sottoscrizione continueranno a funzionare dopo la data di ritiro. Tuttavia, si consiglia di eseguire l'aggiornamento alla versione più recente di DSVM, in modo da avere i sistemi operativi più recenti e gli strumenti di data science.

## <a name="impact"></a>Impatto

Le immagini con provisioning di DSVM esistenti nella sottoscrizione continueranno a funzionare dopo la data di ritiro. Tuttavia, si consiglia agli utenti di aggiornare l'immagine di DSVM alla versione più recente usando il modello di portale di Azure o ARM.

> [!WARNING]
> Le immagini DSVM ritirate con provisioning con i set di scalabilità di macchine virtuali non verranno scalate dopo la data di ritiro.
>
> I modelli ARM che non sono stati aggiornati con i nuovi dettagli dell'immagine DSVM non verranno distribuiti dopo la data di ritiro.

