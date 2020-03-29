---
title: Usare un riquadro markdown personalizzato nei dashboard di Azure
description: Informazioni su come aggiungere un riquadro markdown a un dashboard di Azure per visualizzare il contenuto statico
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310714"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Usare un riquadro markdown nei dashboard di Azure per visualizzare il contenuto personalizzato

È possibile aggiungere un riquadro markdown ai dashboard di Azure per visualizzare il contenuto statico personalizzato. Ad esempio, è possibile visualizzare le istruzioni di base, un'immagine o un set di collegamenti ipertestuali in un riquadro markdown.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Aggiungere un riquadro markdown al dashboard

1. Selezionare **Dashboard** nella barra laterale del portale di Azure.

   ![Schermata che mostra la barra laterale del portale](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Se sono stati creati dashboard personalizzati, nella visualizzazione dashboard usare l'elenco a discesa per selezionare il dashboard in cui deve essere visualizzato il riquadro markdown personalizzato. Selezionare l'icona di modifica per aprire la **Raccolta riquadri**.

   ![Screenshot con la modifica della visualizzazione del dashboard](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Nella **Raccolta riquadri**individuare il riquadro denominato **Markdown** e selezionare **Aggiungi**. Il riquadro viene aggiunto al dashboard e si apre il riquadro **Modifica markdown**.

1. Immettere i valori per **Titolo** e **Sottotitolo**, visualizzati nel riquadro dopo lo spostamento in un altro campo.

   ![Screenshot che mostra i risultati dell'immissione di titolo e sottotitolo](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selezionare una delle opzioni per includere il contenuto di markdown: **Modifica in linea** o Inserisci contenuto **tramite URL**.

   - Selezionare **Modifica in linea** se si desidera immettere direttamente markdown.

      ![Schermata che mostra l'immissione di contenuto in linea](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Selezionare **Inserisci contenuto tramite URL** se si desidera utilizzare contenuto markdown esistente ospitato online.

      ![Schermata che mostra l'URL di immissione](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Per una maggiore sicurezza, è possibile creare un file markdown e archiviarlo in un [BLOB dell'account di archiviazione](../storage/common/storage-service-encryption.md)di Azure in cui è abilitata la crittografia, quindi scegliere il file usando l'opzione URL. Il contenuto markdown viene crittografato tramite le opzioni di crittografia dell'account di archiviazione. Solo gli utenti con autorizzazioni per il file possono visualizzare il contenuto di markdown nel dashboard.

1. Selezionare **Fine** per chiudere il riquadro **Modifica markdown**. Il contenuto viene visualizzato nel riquadro Markdown, che è possibile ridimensionare trascinando la maniglia nell'angolo inferiore destro.

   ![Screenshot con il riquadro markdown personalizzato](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Limitazioni e funzionalità del contenuto markdown

È possibile usare qualsiasi combinazione di testo normale, sintassi Markdown e contenuto HTML nel riquadro markdown. Il portale di Azure usa una libreria open source denominata _marked_ per trasformare il contenuto nel codice HTML visualizzato nel riquadro. Il codice HTML generato da _marked_ viene preelaborato dal portale prima del rendering. Questo passaggio consente di assicurarsi che la personalizzazione non influisca sulla sicurezza o sul layout del portale. Durante la preelaborazione, viene rimossa qualsiasi parte del codice HTML che rappresenta una potenziale minaccia. I tipi di contenuto seguenti non sono consentiti dal portale:

* JavaScript: i tag `<script>` e le valutazioni JavaScript inline verranno rimossi.
* iframes: i tag `<iframe>` verranno rimossi.
* Stile: i tag `<style>` verranno rimossi. Gli attributi di stile in linea negli elementi HTML non sono ufficialmente supportati. Alcuni elementi di stile in linea possono essere accettabili, ma se interferiscono con il layout del portale, potrebbero smettere di funzionare in qualsiasi momento. Il riquadro markdown è destinato al contenuto statico di base che usa gli stili predefiniti del portale.

## <a name="next-steps"></a>Passaggi successivi

* Per creare un dashboard personalizzato, vedere [Creare e condividere dashboard nel portale di Azure](../azure-portal/azure-portal-dashboards.md)
