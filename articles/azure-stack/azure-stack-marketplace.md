---
title: Pubblicare un elemento personalizzato del marketplace in Azure Stack (operatore cloud) | Microsoft Docs
description: Un operatore di Azure Stack, informazioni su come pubblicare un elemento personalizzato del marketplace in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: df1d263b321361c00ceb37c84858dd2c62033228
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245350"
---
# <a name="azure-stack-marketplace-overview"></a>Panoramica di Azure Stack Marketplace

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Il Marketplace di Azure Stack è una raccolta di servizi, applicazioni e risorse personalizzate per Azure Stack. Le risorse includono reti, le macchine virtuali, archiviazione e altro ancora. Usa il Marketplace per creare nuove risorse e distribuire nuove applicazioni; oppure individuare e selezionare gli elementi da usare. Per usare un elemento del Marketplace, gli utenti devono sottoscrivere un'offerta che consente l'accesso all'elemento.

Un operatore di Azure Stack, si decide quali elementi da aggiungere (pubblica) nel Marketplace. È possibile pubblicare gli elementi, ad esempio database, servizi App e così via. Pubblicazione li rende visibile a tutti gli utenti. È possibile pubblicare gli elementi personalizzati creati, o è possibile pubblicare elementi da un aumento delle dimensioni [elenco di elementi di Azure Marketplace](azure-stack-marketplace-azure-items.md). Quando si pubblica un elemento nel Marketplace, gli utenti possono visualizzarlo entro cinque minuti.

> [!CAUTION]  
> Tutti gli artefatti elemento della raccolta, tra cui immagini e i file JSON, sono accessibili senza autenticazione dopo rendendoli disponibili in Azure Stack Marketplace. Per altre considerazioni quando si pubblicano elementi personalizzato del marketplace, vedere [creare e pubblicare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Per aprire il Marketplace, nel portale selezionare amministratore **+ crea una risorsa**.

![Marketplace](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Elementi del Marketplace

Un elemento del Marketplace Azure Stack è un servizio, applicazione o risorse che gli utenti possono scaricare e usare. Tutti gli elementi di Marketplace Azure Stack sono visibili a tutti gli utenti, inclusi gli elementi di amministrazione, ad esempio i piani e offerte. Questi elementi non richiedono una sottoscrizione di visualizzazione, ma non funzionali per gli utenti sono.

Ogni un elemento del Marketplace include:

* Un modello di Azure Resource Manager per il provisioning delle risorse.
* Metadati, ad esempio stringhe, icone e altro materiale di marketing.
* Informazioni di formattazione per visualizzare l'elemento nel portale.

Ogni elemento pubblicato nel Marketplace Usa il formato Azure Gallery Package (con estensione azpkg). Aggiungere le risorse di runtime o di distribuzione (code, file zip con software o immagini di macchine virtuali) in Azure Stack separatamente, non come parte dell'elemento del Marketplace.

Con la versione 1803 e versioni successive, Azure Stack converte le immagini per i file sparse al download di Azure o quando si caricano immagini personalizzate. Questo processo aggiunge tempo quando si aggiunge un'immagine, ma consente di risparmiare spazio e velocizzare la distribuzione di queste immagini. La conversione si applica solo alle nuove immagini. Immagini esistenti non vengono modificate.

## <a name="next-steps"></a>Passaggi successivi

* [Scaricare elementi di Marketplace](azure-stack-download-azure-marketplace-item.md)  
* [Creare e pubblicare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md)
