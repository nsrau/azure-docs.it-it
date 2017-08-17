---
title: Utilizzare un'applicazione gestita di Azure in Marketplace| Microsoft Docs
description: Descrive come creare un'applicazione gestita di Azure disponibile tramite Marketplace.
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="consume-azure-managed-applications-in-the-marketplace"></a>Utilizzare un'applicazione gestita di Azure in Marketplace

Come illustrato nell'articolo [Panoramica di Applicazione gestita di Azure](managed-application-overview.md), gli scenari nell'esperienza end-to-end sono due. Nel primo l'editore o il fornitore vuole creare un'applicazione gestita che verrà usata dai clienti. Il secondo è relativo al cliente finale o all'utente dell'applicazione gestita. Questo articolo riguarda il secondo scenario. Descrive come è possibile distribuire un'applicazione gestita da Microsoft Azure Marketplace.

## <a name="create-from-the-marketplace"></a>Creare da Marketplace

La distribuzione di un'applicazione gestita da Marketplace è simile alla distribuzione di qualsiasi tipo di risorse da Marketplace. 

Nel portale selezionare **+ Nuovo** e cercare il tipo di soluzione da distribuire. Selezionare l'opzione appropriata tra quelle disponibili.

![cercare le soluzioni](./media/managed-application-consume-marketplace/search-apps.png)

Esaminare il riepilogo dell'applicazione e selezionare **Crea**.

![creare l'applicazione gestita](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

Come per qualsiasi altra soluzione, vengono visualizzati i campi per cui fornire i valori. Questi campi variano a seconda del tipo di applicazione gestita creata. 

## <a name="view-support-information"></a>Visualizzare le informazioni di supporto

Dopo avere distribuito l'applicazione gestita, visualizzare le informazioni di supporto per l'applicazione. Nel pannello dell'applicazione gestita sono elencate le informazioni di supporto.

![support](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>Visualizzare le autorizzazioni dell'editore

Al fornitore che gestisce l'applicazione viene concesso l'accesso alle risorse. Per esaminare queste autorizzazioni, selezionare **Autorizzazioni**.

![autorizzazioni](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla pubblicazione di un'applicazione gestita in Marketplace, vedere [Applicazioni gestite di Azure Marketplace](managed-application-author-marketplace.md).
* Per pubblicare le applicazioni gestite disponibili solo per gli utenti dell'organizzazione, vedere [Creare e pubblicare un'applicazione gestita del catalogo di servizi](managed-application-publishing.md).
* Per usare le applicazioni gestite disponibili solo per gli utenti dell'organizzazione, vedere [Utilizzare un'applicazione gestita del catalogo di servizi](managed-application-consumption.md).
