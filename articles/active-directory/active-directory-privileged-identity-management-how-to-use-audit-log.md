<properties
   pageTitle="Azure Privileged Identity Management: Come usare il log di controllo"
   description="Informazioni su come usare il log di controllo nell'estensione Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management: Come usare il log di controllo

## Uso del log di controllo
È possibile usare il log di controllo di Privileged Identity Management per visualizzare tutte le assegnazioni utente e le attivazioni per un periodo di tempo specificato.

## Accesso al log di controllo
È possibile accedere al log di controllo facendo clic su Cronologia controlli nel dashboard di PIM.

## Grafico del log di controllo
È possibile usare il log di controllo per visualizzare il totale delle attivazioni, il numero massimo di attivazioni per giorno e il numero medio di attivazioni per giorno in un grafico a linee. È anche possibile filtrare i dati per ruolo se sono presenti più ruoli nella cronologia di controlli.

È possibile eseguire l'ordinamento per **ora**, **azione** o **ruolo** usando i pulsanti corrispondenti.

## Elenco del log di controllo
Le colonne nell'elenco del log di controllo sono le seguenti:

- **Richiedente**: persona che ha richiesto l'attivazione del ruolo.
- **Utente**: utente dell'attivazione del ruolo.
- **Ruolo**: ruolo assegnato all'utente.
- **Azione**: azioni intraprese con il ruolo/utente.
- **Ora**: quando si è verificata l'azione.
- **Motivo**: eventuale testo immesso nel campo del motivo durante l'attivazione.
- **Scadenza**: se l'anno di scadenza è 9999, il ruolo è assegnato in modo permanente all'utente.

## Applicazione di un filtro nel log di controllo

È anche possibile filtrare le informazioni visualizzate nel log di controllo facendo clic sul pulsante **Filtro**. Verrà visualizzato il pannello **Aggiorna parametri grafico**.

### Modificare l'intervallo di date
Modificare l'intervallo di date del log di controllo scegliendo uno dei pulsanti **Oggi**, **Settimana precedente**, **Mese precedente** o **Personalizzato**. Se si sceglie il pulsante **Personalizzato**, verranno visualizzati i campi di data **Da** e **A** per specificare l'intervallo di date desiderato per il log. È possibile immettere le date nel formato MM/GG/AAAA o fare clic sull'icona **calendario** e scegliere la data da un calendario.

### Modificare i ruoli inclusi nel log

Selezionare o deselezionare la casella di controllo **Ruolo** accanto a ogni ruolo che si desidera includere o escludere dal log.

Dopo aver impostato tutti i filtri desiderati per il log di controllo, fare clic sul pulsante di aggiornamento per filtrare i dati nel log. Se i dati non vengono visualizzati immediatamente, fare clic sul pulsante **Aggiorna**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->