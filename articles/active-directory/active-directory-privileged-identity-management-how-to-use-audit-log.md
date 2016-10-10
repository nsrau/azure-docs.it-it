<properties
   pageTitle="Come usare il log di controllo | Microsoft Azure"
   description="Informazioni su come usare il log di controllo nell'estensione Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>  

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2016"
   ms.author="kgremban"/>  

# Come usare il log di controllo in Azure AD Privileged Identity Management

È possibile usare il log di controllo di Privileged Identity Management (PIM) per visualizzare tutte le assegnazioni utente e le attivazioni per un periodo di tempo specificato. Se si desidera visualizzare la cronologia di controllo completa dell'attività nel tenant, inclusi amministratore, utente finale e attività di sincronizzazione, è possibile usare i [report di accesso e utilizzo di Azure Active Directory.](active-directory-view-access-usage-reports.md)

## Accedere al log di controllo
Nel dashboard del [portale di Azure](https://portal.azure.com) selezionare l'app **Azure AD Privileged Identity Management**. Da qui è possibile accedere al log di controllo facendo clic su **Gestione dei ruoli con privilegi** > **Cronologia dei controlli** nel dashboard di PIM.

## Grafico del log di controllo
È possibile usare il log di controllo per visualizzare il totale delle attivazioni, il numero massimo di attivazioni per giorno e il numero medio di attivazioni per giorno in un grafico a linee. È anche possibile filtrare i dati per ruolo se sono presenti più ruoli nella cronologia di controlli.

Usare i pulsanti relativi a **ora**, **azione** e **ruolo** per ordinare il log.

## Elenco del log di controllo
Le colonne nell'elenco del log di controllo sono le seguenti:

- **Richiedente**: utente che ha richiesto l'attivazione o la modifica del ruolo. Se il valore è "Azure System", vedere il log di controllo di Azure per altre informazioni.
- **Utente**: utente che esegue l'attivazione o che è assegnato a un ruolo.
- **Ruolo**: ruolo assegnato o attivato dall'utente.
- **Azione**: azioni eseguite dal richiedente. Le azioni possono includere assegnazione, annullamento dell'assegnazione, attivazione o disattivazione.
- **Ora**: quando si è verificata l'azione.
- **Motivo**: eventuale testo immesso nel campo del motivo durante l'attivazione.
- **Scadenza**: rilevante solo per l'attivazione dei ruoli.

## Filtrare il log di controllo

È possibile filtrare le informazioni visualizzate nel log di controllo facendo clic sul pulsante **Filtro**. Verrà visualizzato il pannello **Aggiorna parametri grafico**.

Dopo aver impostato i filtri, fare clic su **Aggiorna** per filtrare i dati del log. Se i dati non vengono visualizzati immediatamente, aggiornare la pagina.


### Modificare l'intervallo di date
Usare i pulsanti **Oggi**, **Ultima settimana**, **Mese precedente** o **Personalizzato** per modificare l'intervallo di tempo del log di controllo.

Se si sceglie il pulsante **Personalizzato**, vengono visualizzati i campi di data **Da** e **A** per specificare un intervallo di date per il log. È possibile immettere le date nel formato MM/GG/AAAA o fare clic sull'icona **calendario** e scegliere la data da un calendario.

### Modificare i ruoli inclusi nel log

Selezionare o deselezionare la casella di controllo **Ruolo** accanto a ogni ruolo per includerlo o escluderlo dal log.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->  
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0928_2016-->