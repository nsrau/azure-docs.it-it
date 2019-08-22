---
title: Risolvere i problemi di pagamento di Azure
description: Risoluzione di un problema durante l'aggiornamento dell'account di informazioni di pagamento nel portale di Microsoft Azure o nel centro account.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657086"
---
# <a name="troubleshoot-azure-payment-issues"></a>Risolvere i problemi di pagamento di Azure

È possibile che si verifichi un problema quando si tenta di aggiornare l'account delle informazioni di pagamento nel portale di Microsoft Azure o nel centro account di Azure. Prima di risolvere il problema, considerare le linee guida seguenti:

- Assicurarsi che le informazioni fornite per il profilo dell'account di Azure (inclusi indirizzo di posta elettronica di contatto, indirizzo via e numero di telefono) siano corrette.
- Assicurarsi che le informazioni della carta di credito siano corrette.
- Assicurarsi che non sia già presente un account Microsoft con le stesse informazioni.

## <a name="issues"></a>Problemi

Per risolvere eventuali errori, selezionare il problema che si verifica quando si tenta di eseguire l'iscrizione ad Azure.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Non è possibile rimuovere una carta di credito da un metodo di pagamento per la fatturazione salvato

Per impostazione predefinita, non è possibile rimuovere una carta di credito dalla sottoscrizione attiva.

Se è necessario eliminare una scheda esistente, è necessario aggiungere una nuova scheda alla sottoscrizione in modo che lo strumento di pagamento precedente possa essere eliminato correttamente oppure è necessario annullare la sottoscrizione. In questo modo la sottoscrizione viene eliminata definitivamente e la scheda viene rimossa.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Non è possibile eliminare un metodo di pagamento precedente dopo l'aggiunta di un nuovo metodo di pagamento

Il nuovo strumento di pagamento potrebbe non essere associato alla sottoscrizione. Per associare lo strumento di pagamento alla sottoscrizione, vedere [aggiungere, aggiornare o rimuovere una carta di credito o debito per Azure](billing-how-to-change-credit-card.md).

Per risolvere i problemi relativi a una scheda rifiutata, vedere come risolvere i problemi relativi a [una scheda rifiutata all'iscrizione ad Azure](billing-troubleshoot-declined-card.md).

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Non è possibile eliminare un metodo di pagamento perché *non è possibile eliminare* l'errore del metodo di pagamento

Questo problema si verifica a causa di un saldo in attesa. Cancellare eventuali saldi in attesa prima di eliminare il metodo di pagamento.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Non è possibile visualizzare le sottoscrizioni dell'account per aggiornare il metodo di pagamento

È possibile che si stia usando un ID di posta elettronica diverso da quello usato per le sottoscrizioni.

Per risolvere questo problema, vedere [non è stato trovato alcun errore di accesso alle sottoscrizioni per portale di Azure o il centro account di Azure](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Non è possibile effettuare il pagamento per una sottoscrizione

Se viene visualizzato il messaggio di errore: *Il pagamento è scaduto. Si è verificato un problema con il metodo* di *pagamento oppure non è possibile salvare le informazioni. Chiudere il browser e riprovare.* , quindi è presente un pagamento in sospeso sulla scheda perché la scheda è stata negata dall'istituto finanziario.

Verificare che la carta di credito disponga di un saldo sufficiente per effettuare un pagamento. In caso contrario, usare un'altra scheda per effettuare il pagamento o rivolgersi all'istituto finanziario per risolvere il problema.

Contattare la banca per individuare i seguenti problemi:

- Le transazioni internazionali non sono abilitate.
- La scheda ha un limite di credito e il saldo deve essere stabilito.
- Sulla scheda è abilitato un pagamento ricorrente.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Non è possibile modificare il metodo di pagamento a causa di problemi del browser (il browser non risponde, non carica e così via)

Disconnettersi da tutte le sessioni di Azure attive e quindi seguire la procedura descritta nell' [articolo esplorare InPrivate in Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) per avviare una sessione InPrivate in Microsoft Edge o Internet Explorer.

Nella sessione privata, attenersi alla procedura descritta in [come modificare una carta di credito](billing-how-to-change-credit-card.md) per aggiornare o modificare le informazioni sulla carta di credito.

È anche possibile provare a eseguire le operazioni seguenti:

- Aggiornare il browser
- Usare un altro browser
- Elimina cookie memorizzati nella cache

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>La sottoscrizione è ancora disabilitata dopo l'aggiornamento del metodo di pagamento.

Questo problema si verifica a causa di un saldo in attesa. Cancellare eventuali saldi in attesa prima di eliminare il metodo di pagamento.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Impossibile modificare il metodo di pagamento a causa di una pagina di risposta di errore XML

Questo messaggio viene visualizzato se si usa [il portale di Azure](https://portal.azure.com/) per aggiungere una nuova carta di credito.

Per aggiungere i dettagli della scheda, accedere al portale degli account di Azure usando l'indirizzo di posta elettronica dell'amministratore account.

## <a name="additional-help-resources"></a>Risorse della guida aggiuntive

Altri articoli sulla risoluzione dei problemi per la fatturazione e le sottoscrizioni di Azure

- [Carta rifiutata](billing-troubleshoot-declined-card.md)
- [Problemi di accesso alla sottoscrizione](billing-troubleshoot-sign-in-issue.md)
- [Non sono state trovate sottoscrizioni](billing-no-subscriptions-found.md)
- [Vista dei costi Enterprise disabilitata](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contattaci per assistenza

In caso di domande o per assistenza, [creare una richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione sulla fatturazione di Azure](index.md)
