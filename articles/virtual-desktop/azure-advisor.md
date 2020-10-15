---
title: Integra desktop virtuale di Windows con Azure Advisor-Azure
description: Come usare Azure Advisor con la distribuzione di desktop virtuali Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147535"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Usare Azure Advisor con desktop virtuale di Windows

Azure Advisor possono aiutare gli utenti a risolvere i problemi più comuni senza dover archiviare i casi di supporto. Le raccomandazioni riducono la necessità di inviare richieste di assistenza, risparmiando tempo e costi.

Questo articolo illustra come configurare Azure Advisor nella distribuzione di desktop virtuali Windows per aiutare gli utenti.

## <a name="what-is-azure-advisor"></a>Cos'è Azure Advisor?

Azure Advisor analizza le configurazioni e i dati di telemetria per offrire consigli personalizzati per risolvere i problemi comuni. Con questi consigli puoi ottimizzare le tue risorse di Azure per affidabilità, sicurezza, eccellenza operativa, prestazioni e costi. Ulteriori informazioni sono disponibili nel [sito web Azure Advisor](https://azure.microsoft.com/services/advisor/).

## <a name="how-to-start-using-azure-advisor"></a>Come iniziare a usare Azure Advisor

Per iniziare, è sufficiente un account Azure nella portale di Azure. Prima di tutto, aprire il portale di Azure in <https://portal.azure.com/#home> , quindi selezionare **Advisor** in **servizi di Azure**, come illustrato nella figura seguente. È anche possibile immettere "Azure Advisor" nella barra di ricerca nel portale di Azure.

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure. L'utente sta posizionando il cursore del mouse sul collegamento Azure Advisor, causando la visualizzazione di un menu a discesa.](media/azure-advisor.png)

Quando si apre Azure Advisor, verranno visualizzate cinque categorie:

- Cost
- Security
- Affidabilità
- Eccellenza operativa
- Prestazioni

> [!div class="mx-imgBorder"]
> ![Screenshot del Azure Advisor che mostra i cinque menu per ogni categoria. I cinque elementi visualizzati nelle rispettive caselle sono costo, sicurezza, affidabilità, eccellenza operativa e prestazioni.](media/advisor-categories.png)

Quando si seleziona una categoria, si passerà alla relativa pagina di raccomandazioni attive. In questa pagina è possibile visualizzare le raccomandazioni Azure Advisor, come illustrato nella figura seguente.

> [!div class="mx-imgBorder"]
> ![Screenshot dell'elenco di raccomandazioni attive per l'eccellenza operativa. L'elenco Mostra sette raccomandazioni con diversi livelli di priorità.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Suggerimenti aggiuntivi per Azure Advisor

- Assicurarsi di controllare spesso le raccomandazioni, almeno più di una volta alla settimana. Azure Advisor aggiorna le raccomandazioni attive più volte al giorno. Il controllo di nuove raccomandazioni può impedire problemi più grandi grazie alla possibilità di individuare e risolvere i problemi più piccoli.

- Provare sempre a risolvere i problemi con il livello di priorità più elevato in Azure Advisor. I problemi con priorità alta sono contrassegnati con il rosso. Lasciare le raccomandazioni con priorità alta non risolte può causare problemi alla riga.

- Se una raccomandazione sembra meno importante, è possibile ignorarla o posticiparla. Per ignorare o posticipare una raccomandazione, passare alla colonna **azione** e modificare lo stato dell'elemento.

- Non ignorare le raccomandazioni fino a quando non si conosce il motivo per cui vengono visualizzate e si è certi che l'utente o gli utenti non avranno un impatto negativo. Selezionare sempre **altre informazioni** per verificare il problema. Se si risolve un problema seguendo le istruzioni riportate in Azure Advisor, questo verrà automaticamente rimosso dall'elenco. È preferibile risolvere i problemi piuttosto che Posticiparli ripetutamente.

- Ogni volta che si verifica un problema nel desktop virtuale di Windows, controllare sempre Azure Advisor prima di tutto. Azure Advisor fornirà indicazioni su come risolvere il problema o almeno puntare a una risorsa che può essere utile.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come risolvere le raccomandazioni, vedere [How to resolve Azure Advisor raccomandazioni](azure-advisor-recommendations.md).

Per suggerimenti relativi a nuove raccomandazioni, pubblicare un post nel [forum Azure Advisor User Voice](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations).
