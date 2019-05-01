---
title: Informazioni su come compilare il modulo delle impostazioni dell'offerta | Azure Marketplace
description: Illustra i vari campi che richiedono valori nel modulo delle impostazioni dell'offerta, per una nuova applicazione di Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pabutler
ms.openlocfilehash: d29b17e1a109b37a51a0e6bd2af2a7bb02b977a9
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934915"
---
<a name="how-to-fill-out-the-offer-settings-form"></a>Come compilare il modulo delle impostazioni dell'offerta
=======================================

Il modulo delle impostazioni dell'offerta è un modulo di base in cui specificare le impostazioni di offerta.
I campi obbligatori sono indicati di seguito.

### <a name="offer-id"></a>Offer ID (ID offerta)

`OfferId` è un identificatore univoco dell'offerta in un profilo di pubblicazione.
Questo ID sarà visibile negli URL dei prodotti. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta.

Ad esempio, se il partner "Contoso" crea un ID offerta denominato "esempio app Web", sarà visualizzato in AppSource come:

&emsp; `https://appsource.microsoft.com/marketplace/apps/contoso.sample-Web App?tab=Overview`


### <a name="publisher-id"></a>Publisher ID (ID editore)

Questo elenco a discesa consente di scegliere il profilo di pubblicazione in cui si desidera pubblicare l'offerta. Questo campo è bloccato dopo la pubblicazione dell'offerta.


### <a name="name"></a>NOME

Si tratta del nome visualizzato per l'app/offerta in Microsoft [AppSource](https://appsource.microsoft.com/). Può contenere massimo 50 caratteri.

> [!NOTE]
> Il nome breve deve essere identico al nome editore specificato nel manifesto dell'applicazione.

Fare clic su **Salva** per salvare le voci immesse. Il passaggio successivo consiste nell'aggiungere informazioni tecniche all'offerta.
