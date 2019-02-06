---
title: Usare un riquadro markdown personalizzato nei dashboard di Azure
description: Informazioni su come aggiungere un riquadro markdown a un dashboard di Azure per visualizzare il contenuto statico
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 29c6dc0f7e71508e7b4ecb4e0f1d5c849df7a68c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54905014"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Usare un riquadro markdown nei dashboard di Azure per visualizzare il contenuto personalizzato

È possibile aggiungere un riquadro markdown ai dashboard di Azure per visualizzare il contenuto statico personalizzato. È ad esempio possibile visualizzare istruzioni di base, un'immagine o un set di collegamenti ipertestuali con un riquadro markdown.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Aggiungere un riquadro markdown al dashboard

1. Selezionare **Dashboard** nella barra laterale del portale di Azure. Se sono stati creati dashboard personalizzati, nella visualizzazione dashboard usare l'elenco a discesa per selezionare il dashboard in cui deve essere visualizzato il riquadro markdown personalizzato. Selezionare l'icona di modifica per aprire la **Raccolta riquadri**.

  ![Screenshot con la modifica della visualizzazione del dashboard](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. Nella **Raccolta riquadri** individuare il riquadro denominato **Markdown** e fare clic su **Aggiungi**. Il riquadro viene aggiunto al dashboard e si apre il riquadro **Modifica markdown**.

1. Modificare i campi **Titolo**, **Sottotitolo** e **Contenuto** per personalizzare il riquadro. Nell'esempio illustrato di seguito il riquadro markdown è stato modificato per visualizzare le informazioni personalizzate sull'help desk.

  ![Screenshot con la modifica della visualizzazione del riquadro markdown](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Selezionare **Fine** per chiudere il riquadro **Modifica markdown**. Il contenuto verrà visualizzato nel riquadro Markdown, che può quindi essere ridimensionato trascinando il punto di controllo nell'angolo inferiore destro.

  ![Screenshot con il riquadro markdown personalizzato](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Limitazioni e funzionalità del contenuto markdown

È possibile usare qualsiasi combinazione di testo normale, sintassi Markdown e contenuto HTML nel riquadro markdown. Il portale di Azure usa una libreria open source denominata _marked_ per trasformare il contenuto nel codice HTML visualizzato nel riquadro. Il codice HTML generato da _marked_ viene preelaborato dal portale prima del rendering. Questo passaggio consente di assicurarsi che la personalizzazione non influisca sulla sicurezza o sul layout del portale. Durante la preelaborazione, viene rimossa qualsiasi parte del codice HTML che rappresenta una potenziale minaccia. I tipi di contenuto seguenti non sono consentiti dal portale:

* JavaScript: i tag `<script>` e le valutazioni JavaScript inline verranno rimossi.
* iframes: i tag `<iframe>` verranno rimossi.
* Stile: i tag `<style>` verranno rimossi. Gli attributi di stile in linea negli elementi HTML non sono ufficialmente supportati. Alcuni elementi di stile in linea possono essere accettabili, ma se interferiscono con il layout del portale, potrebbero smettere di funzionare in qualsiasi momento. Il riquadro markdown è destinato al contenuto statico di base che usa gli stili predefiniti del portale.

## <a name="next-steps"></a>Passaggi successivi

* Per creare un dashboard personalizzato, vedere [Creare e condividere dashboard nel portale di Azure](../azure-portal/azure-portal-dashboards.md)
