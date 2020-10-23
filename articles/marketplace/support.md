---
title: Ottenere supporto per il portale del Marketplace commerciale nel centro per i partner
description: Informazioni sulle opzioni di supporto in Partner Center, incluso come archiviare una richiesta di supporto.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/18/2020
ms.openlocfilehash: 983f7e400a33cb36f30505e5a8a28811d9d4cdfb
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284252"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Supporto per il programma Commercial Marketplace nel centro per i partner

Microsoft fornisce il supporto per un'ampia gamma di prodotti e servizi. Trovare il team di supporto giusto è importante per garantire una risposta appropriata e tempestiva. Considerare gli scenari seguenti, che possono essere utili per indirizzare la propria richiesta al team appropriato:

- Se si è un editore e si ha una domanda da parte di un cliente, chiedere al cliente di richiedere supporto tramite i collegamenti di supporto nella [portale di Azure](https://portal.azure.com/).
- Se si è un editore e si ha una domanda relativa all'app o al servizio, esaminare le opzioni di supporto seguenti.

## <a name="support-options-for-publishers"></a>Opzioni di supporto per gli editori

1. Accedere al [programma Commercial Marketplace nel centro per i partner](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) con l'account aziendale. se non è ancora stato fatto, sarà necessario [creare un account del centro](partner-center-portal/create-account.md)per i partner.

2. Nel menu superiore sul lato destro della pagina selezionare l'icona **supporto** .

3. Il riquadro **Guida e supporto** verrà visualizzato dal lato destro della pagina.

   ![Menu a discesa supporto](./media/support/commercial-marketplace-support-pane.png)

    In alternativa, passare al riquadro **Home page** e selezionare **Guida e supporto tecnico**.

   ![Guida e supporto tecnico dalla Home page](./media/support/homepage-help-support.png)

4. Selezionare la **[documentazione](../index.yml)** per esaminare le risposte complete a domande e risorse.

5. Per rispondere alle domande, selezionare **[Forum della community del partner Marketplace](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/bd-p/2222)** per rispondere alle proprie domande sfruttando la conoscenza di altri editori Microsoft.

6. Selezionare la **[Guida aggiuntiva](https://aka.ms/marketplacepublishersupport)** per aprire un nuovo ticket di **richiesta di supporto** .  

## <a name="how-to-open-a-support-ticket"></a>Come aprire un ticket di supporto

A questo punto si è pronti per aprire un ticket di supporto nella schermata **Guida e supporto tecnico** .

![Guida e supporto](./media/support/help-and-support.png)

>[!Note]
>Se si è connessi al centro per i partner, si riceverà un'esperienza migliore con il supporto tecnico.

**Opzione 1:** Immettere parole chiave quali Marketplace, app di Azure, offerta SaaS, gestione degli account, gestione dei lead, problema di distribuzione, pagamento e così via.

**Opzione 2:** Sfogliare gli argomenti-> selezionare **categoria** = Commercial marketplace-> selezionare l' **argomento** appropriato, quindi eseguire un **subargomento**.

Dopo aver trovato l'argomento desiderato, selezionare **verifica soluzioni**.

![Passaggio successivo](./media/support/next-step.png)

Verranno rese disponibili le opzioni seguenti:

- Per selezionare un argomento diverso, selezionare un altro collegamento all'argomento in **problema selezionato**.
- Esaminare la descrizione del problema, se disponibile.  È il testo visualizzato sopra i **passaggi consigliati**.
- Esaminare le **procedure consigliate**, se disponibili.
- Esaminare i **documenti consigliati**, se disponibili.

![Soluzioni consigliate](./media/support/recommended-solutions.png)

Se non è possibile trovare la risposta nelle **soluzioni consigliate**, selezionare **Fornisci i dettagli del problema**. Completare tutti i campi obbligatori per velocizzare il processo di risoluzione, quindi selezionare **Invia**.

>[!Note]
>Se non è stato effettuato l'accesso al centro per i partner e l'argomento richiede l'autenticazione, verrà richiesto di effettuare l'accesso prima di procedere.  Per gli argomenti pubblici, l'autenticazione non è obbligatoria.

## <a name="track-your-existing-support-requests"></a>Tenere traccia delle richieste di supporto esistenti

Per esaminare tutti i ticket aperti e chiusi, passare a **Marketplace commerciale** sulla barra di spostamento a sinistra e quindi selezionare **supporto**.

## <a name="record-issue-details-with-a-har-file"></a>Registrare i dettagli del problema con un file HAR

Per consentire agli agenti di supportare la risoluzione del problema, prendere in considerazione la possibilità di allegare un file di formato di archivio HTTP (HAR) al ticket di supporto. I file HAR sono log delle richieste di rete in un Web browser.

> [!WARNING]
> I file HAR possono registrare dati sensibili sull'account del centro per i partner.

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge e Google Chrome

Per generare un file HAR usando **Microsoft Edge** o **Google Chrome**:

1. Passare alla pagina Web in cui si è verificato il problema.
2. Nell'angolo superiore destro della finestra fare clic sull'icona con i puntini di sospensione, quindi su strumenti di sviluppo per **altri strumenti**  >  **Developer tools**. È possibile premere F12 come collegamento.
3. Nel riquadro strumenti di sviluppo selezionare la scheda **rete** .
4. Selezionare **Interrompi registrazione log di rete** e **Cancella** per rimuovere i log esistenti. L'icona del record diventerà grigio.

    ![Come rimuovere i log esistenti in Microsoft Edge o Google Chrome](media/support/chromium-stop-clear-session.png)

5. Selezionare **registra log di rete** per avviare la registrazione. Quando si avvia la registrazione, l'icona del record diventerà rossa.

    ![Come avviare la registrazione in Microsoft Edge o Google Chrome](media/support/chromium-start-session.png)

6. Riprodurre il problema che si desidera risolvere.
7. Dopo aver riprodotto il problema, selezionare **Interrompi registrazione log di rete**.
8. Selezionare **Esporta har**, contrassegnato con un'icona con la freccia rivolta verso il basso e salvare il file.

    ![Come esportare un file HAR in Microsoft Edge o Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Per generare un file HAR usando **Mozilla Firefox**:

1. Passare alla pagina Web in cui si è verificato il problema.
1. Nell'angolo in alto a destra della finestra fare clic sull'icona con i puntini di sospensione e quindi su strumenti per lo **sviluppo Web**  >  **Toggle Tools**. È possibile premere F12 come collegamento.
1. Selezionare la scheda **rete** , quindi **deselezionare** per rimuovere i log esistenti.

    ![Come rimuovere i log esistenti in Mozilla Firefox](media/support/firefox-clear-session.png)

1. Riprodurre il problema che si desidera risolvere.
1. Dopo aver riprodotto il problema, selezionare **har Esporta/Importa**  >  **Salva tutto come har**.

    ![Come esportare un file HAR in Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Per generare un file HAR usando **Safari**:

1. Abilitare gli strumenti di sviluppo in Safari: **Safari**selezionare  >  **Preferenze**Safari. Passare alla scheda **Avanzate** , quindi selezionare **Mostra menu sviluppa nella barra dei menu**.
1. Passare alla pagina Web in cui si è verificato il problema.
1. Selezionare **Develop** (Sviluppo), quindi selezionare **Show Web Inspector** (Mostra Web Inspector).
1. Selezionare la scheda **rete** , quindi selezionare **Cancella elementi di rete** per rimuovere i log esistenti.

    ![Come rimuovere i log esistenti in Safari](media/support/safari-clear-session.png)

1. Riprodurre il problema che si desidera risolvere.
1. Dopo aver riprodotto il problema, selezionare **Esporta** e salvare il file.

    ![Come esportare un file HAR in Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](partner-center-portal/update-existing-offer.md)
