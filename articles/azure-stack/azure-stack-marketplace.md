---
title: Pubblica un elemento del marketplace personalizzato nello Stack di Azure (operatore cloud) | Documenti Microsoft
description: Come operatore Azure Stack, informazioni su come pubblicare un elemento del marketplace personalizzato nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: c791708e11b7e9e8bbe046f06233d948d4632c90
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="the-azure-stack-marketplace-overview"></a>La panoramica di Azure Marketplace di Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Marketplace è una raccolta di servizi, applicazioni e risorse personalizzate per Azure Stack, ad esempio reti, le macchine virtuali, archiviazione e così via. Gli utenti sono qui per creare nuove risorse e distribuire le nuove applicazioni. Può essere considerato come un catalogo di acquisto in cui gli utenti possono individuare e selezionare gli elementi che si desidera utilizzare. Per utilizzare un elemento del Marketplace, gli utenti è necessario sottoscrivere un'offerta che concede l'accesso all'elemento.

Come operatore di Stack di Azure, si decide che gli elementi da aggiungere (pubblica) per il Marketplace. È possibile pubblicare elementi quali database, servizi di App e così via. Questo li rende visibile a tutti gli utenti. È possibile pubblicare gli elementi personalizzati creati. È anche possibile pubblicare elementi da un aumento delle dimensioni [elenco di elementi di Azure Marketplace](azure-stack-marketplace-azure-items.md). Quando si pubblica un elemento nel Marketplace, gli utenti possono visualizzarlo entro cinque minuti.

Per aprire il Marketplace, fare clic su **Nuovo**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Elementi del Marketplace
Un elemento dello Stack di Azure Marketplace è un servizio, applicazione o risorse che gli utenti possono scaricare e utilizzare. Tutti gli elementi di Azure Marketplace Stack sono visibili a tutti gli utenti.

Ogni un elemento del Marketplace include:

* Modello di Gestione risorse di Azure per il provisioning delle risorse
* Metadati, ad esempio stringhe, icone e altro materiale aggiuntivo di marketing
* Informazioni di formattazione per visualizzare l'elemento nel portale

Ogni elemento pubblicato nel Marketplace usa un formato di pacchetto della raccolta di Azure, o Azure Gallery Package (con estensione azpkg). Aggiungere risorse di runtime o di distribuzione (ad esempio di codice, i file zip con software o di immagini di macchine virtuali) allo Stack Azure separatamente, non come parte dell'elemento del Marketplace. 

## <a name="next-steps"></a>Passaggi successivi
[Creare e pubblicare un elemento del marketplace](azure-stack-create-and-publish-marketplace-item.md)

