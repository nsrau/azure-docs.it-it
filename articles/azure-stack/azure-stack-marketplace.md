---
title: Pubblica un elemento del marketplace personalizzato nello Stack di Azure (operatore cloud) | Documenti Microsoft
description: Come operatore Azure Stack, informazioni su come pubblicare un elemento del marketplace personalizzato nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 4ea23ed01e6432f24024d7e8cc07c2dfe42ac639
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605570"
---
# <a name="the-azure-stack-marketplace-overview"></a>La panoramica di Azure Marketplace di Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Marketplace è una raccolta di servizi, applicazioni e risorse personalizzate per lo Stack di Azure. Le risorse includono reti, le macchine virtuali, archiviazione e così via. Gli utenti sono qui per creare nuove risorse e distribuire le nuove applicazioni. Può essere considerato come un catalogo di acquisto in cui gli utenti possono individuare e selezionare gli elementi che si desidera utilizzare. Per utilizzare un elemento del Marketplace, gli utenti è necessario sottoscrivere un'offerta che concede l'accesso all'elemento.

Come operatore di Stack di Azure, si decide che gli elementi da aggiungere (pubblica) per il Marketplace. È possibile pubblicare elementi quali database, servizi di App e così via. Pubblicazione li rende visibile a tutti gli utenti. È possibile pubblicare gli elementi personalizzati creati. È anche possibile pubblicare elementi da un aumento delle dimensioni [elenco di elementi di Azure Marketplace](azure-stack-marketplace-azure-items.md). Quando si pubblica un elemento nel Marketplace, gli utenti possono visualizzarlo entro cinque minuti.

> [!Caution]  
> Tutti gli elementi di elemento della raccolta noti come immagini e i file json sono accessibili senza autenticazione dopo rendendoli disponibili in marketplace dello Stack di Azure. Per altre considerazioni quando si pubblicano elementi del marketplace personalizzati, vedere [creare e pubblicare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Per aprire il Marketplace, nella console di amministrazione selezionare **New**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Elementi del Marketplace
Un elemento dello Stack di Azure Marketplace è un servizio, applicazione o risorse che gli utenti possono scaricare e utilizzare. Tutti gli elementi di Azure Marketplace Stack sono visibili a tutti gli utenti, inclusi gli elementi di amministrazione, ad esempio i piani e alle offerte. Questi elementi non richiedono una sottoscrizione di visualizzazione, ma sono funzionali agli utenti.

Ogni un elemento del Marketplace include:

* Modello di Gestione risorse di Azure per il provisioning delle risorse
* Metadati, ad esempio stringhe, icone e altro materiale aggiuntivo di marketing
* Informazioni di formattazione per visualizzare l'elemento nel portale

Tutti gli elementi pubblicati nel Marketplace Usa il formato di pacchetto della raccolta di Azure (.azpkg). Aggiungere le risorse di runtime o di distribuzione (ad esempio di codice, i file zip con software o di immagini di macchine virtuali) allo Stack di Azure separatamente, non come parte dell'elemento del Marketplace. 

Con la versione 1803 e versioni successive, Azure Stack converte le immagini per i file sparse al download di Azure, quando si caricano immagini personalizzate. Questo processo aggiunge tempo quando si aggiunge un'immagine, ma consente di risparmiare spazio e velocizzare la distribuzione di tali immagini. Conversione si applica solo alle nuove immagini.  Immagini esistenti non vengono modificate. 

## <a name="next-steps"></a>Passaggi successivi
[Scaricare elementi del Marketplace](azure-stack-download-azure-marketplace-item.md)  
[Creare e pubblicare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md)

