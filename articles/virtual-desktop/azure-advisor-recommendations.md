---
title: Procedura dettagliata Azure Advisor desktop virtuale di Windows-Azure
description: Come risolvere Azure Advisor consigli per desktop virtuale di Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b14dd4aa44620f2b13891a18fc473acbcb0f3245
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086515"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Come risolvere Azure Advisor consigli

Questo articolo descrive come è possibile risolvere le raccomandazioni visualizzate in Azure Advisor per desktop virtuale di Windows.

## <a name="no-validation-environment-enabled"></a>"Nessun ambiente di convalida abilitato"

>[!div class="mx-imgBorder"]
>![Screenshot della pagina Azure Advisor excellent Operational. La raccomandazione "nessun ambiente di convalida abilitato" è evidenziata in rosso.](media/no-validation-environment.png)

Questa raccomandazione viene visualizzata sotto eccellenza operativa. La raccomandazione dovrebbe inoltre visualizzare un messaggio di avviso simile al seguente:

"Non è disponibile un ambiente di convalida abilitato in questa sottoscrizione. Quando sono stati creati i pool host, nella scheda proprietà è stato selezionato **No** per "ambiente di convalida". Per garantire la continuità aziendale tramite le distribuzioni di servizi desktop virtuali Windows, assicurarsi di avere almeno un pool host con un ambiente di convalida in cui è possibile verificare la presenza di potenziali problemi ".

È possibile far sparire questo messaggio di avviso abilitando un ambiente di convalida in uno dei pool host.

Per abilitare un ambiente di convalida:

1. Passare al home page portale di Azure e selezionare il pool host che si desidera modificare.

2. Selezionare quindi il pool host che si desidera modificare da un ambiente di produzione a un ambiente di convalida.

3. Nel pool host selezionare la scheda **Proprietà** nella colonna sul lato destro dello schermo. Scorrere quindi verso il basso fino a visualizzare "ambiente di convalida". Selezionare **Sì**, quindi selezionare **applica**.

>[!div class="mx-imgBorder"]
>![Screenshot del menu proprietà. "Ambiente di convalida" è evidenziato in rosso e la bolla "Sì" è selezionata.](media/validation-yes.png)

Queste modifiche non rilasceranno l'avviso immediatamente, ma scompariranno alla fine. Azure Advisor aggiornamenti due volte al giorno. Fino ad allora, è possibile posticipare o ignorare manualmente la raccomandazione. È consigliabile lasciare che la raccomandazione venga eliminata autonomamente. In questo modo, Azure Advisor possibile indicare se si verificano problemi in caso di modifica delle impostazioni.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"Gli ambienti di produzione (non di convalida) sono abilitati"

Questa raccomandazione viene visualizzata sotto eccellenza operativa.

Per questa raccomandazione, viene visualizzato il messaggio di avviso per uno dei seguenti motivi:

- Sono presenti troppi pool host nell'ambiente di convalida.
- Non sono presenti pool host di produzione.

È consigliabile che gli utenti dispongano di meno della metà dei pool host in un ambiente di convalida.

Per risolvere il problema:

1. Passare al home page portale di Azure.

2. Selezionare i pool host che si desidera modificare dalla convalida alla produzione.

3. Nel pool host selezionare la scheda **Proprietà** nella colonna sul lato destro dello schermo. Scorrere quindi verso il basso fino a visualizzare "ambiente di convalida". Selezionare **No**, quindi selezionare **applica**.

>[!div class="mx-imgBorder"]
>![Screenshot del menu proprietà. "Ambiente di convalida" è evidenziato in rosso e la bolla "No" è selezionata.](media/validation-no.png)

Queste modifiche non rilasceranno l'avviso immediatamente, ma scompariranno alla fine. Azure Advisor aggiornamenti due volte al giorno. Fino ad allora, è possibile posticipare o ignorare manualmente la raccomandazione. È consigliabile lasciare che la raccomandazione venga eliminata autonomamente. In questo modo, Azure Advisor possibile indicare se si verificano problemi in caso di modifica delle impostazioni.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"I collegamenti insufficienti sono sbloccati per implementare correttamente la VM"

Questa raccomandazione viene visualizzata sotto eccellenza operativa.

È necessario sbloccare URL specifici per assicurarsi che la macchina virtuale (VM) funzioni correttamente. È possibile visualizzare l'elenco in [elenco indirizzi attendibili](safe-url-list.md). Se gli URL non sono sbloccati, la macchina virtuale non funzionerà correttamente.

Per risolvere il problema, assicurarsi di sbloccare tutti gli URL nell' [elenco degli URL sicuri](safe-url-list.md). È possibile utilizzare tag di servizio o tag FQDN anche per sbloccare URL.

## <a name="propose-new-recommendations"></a>Suggerisci nuove raccomandazioni

È possibile contribuire a migliorare Azure Advisor inviando idee per le raccomandazioni. La raccomandazione potrebbe aiutare un altro utente a trovare un posto difficile. Per inviare un suggerimento, visitare il [Forum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) e compilare il modulo di invio. Quando si compila il modulo, assicurarsi di fornire il maggior dettaglio possibile.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni più dettagliate su come risolvere i problemi comuni, vedere [Panoramica della risoluzione dei problemi, commenti e suggerimenti e supporto per desktop virtuale di Windows](troubleshoot-set-up-overview.md).