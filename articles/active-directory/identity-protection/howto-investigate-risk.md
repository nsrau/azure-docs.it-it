---
title: Come analizzare i rischi in Azure Active Directory Identity Protection (aggiornato) | Microsoft Docs
description: Informazioni su come analizzare gli utenti, i rilevamenti e gli accessi rischiosi in Azure Active Directory Identity Protection (aggiornamento).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e9715b63ccfbf1da3bdaedf947ea4cd65109b7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129718"
---
# <a name="how-to-investigate-risk"></a>Procedura: Esaminare il rischio

Usando gli accessi a rischio, gli utenti a rischio e i rilevamenti dei rischi, è possibile esaminare e ottenere informazioni sui rischi nell'ambiente. Grazie alla possibilità di filtrare e ordinare gli accessi a rischio, gli utenti e i rilevamenti, è possibile comprendere meglio le potenziali intrusioni nell'organizzazione. 

## <a name="risky-users-report"></a>Report utenti a rischio

Le informazioni contenute nel report utenti a rischio consentono di rispondere a domande come le seguenti:

- Quali sono gli utenti ad alto rischio?
- Quali utenti presentano lo stato di rischio con correzione?

Il primo punto di approccio a questo report è la sezione **Analisis dei problemi** nella pagina di sicurezza.

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/01.png)

Il report utenti a rischio dispone di una vista predefinita che include:

- Name
- Stato del rischio
- Livello di rischio
- Dettaglio del rischio
- Ultimo aggiornamento rischio
- Type
- Stato

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/03.png)

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/04.png)

La finestra di dialogo a colonne permette di visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

Facendo clic su un elemento nella visualizzazione elenco, è possibile ottenere tutti i dettagli disponibili sull'elemento in una visualizzazione orizzontale.

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/05.png)

La vista Dettagli mostra:

- Info di base
- Accessi a rischio recenti
- Rilevamento del rischio non collegato a un accesso
- Cronologia rischio

È anche possibile:

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/08.png)

- Visualizzare tutti i collegamenti agli accessi per vedere il report degli accessi per tale utente.
- Visualizzare tutti gli accessi a rischio per vedere tutti gli accessi dell'utente che sono stati contrassegnati come rischiosi.
- Reimpostare la password dell'utente se si ritiene che la sua identità sia stata compromessa.
- Ignorare i rischi per gli utenti se si ritiene che i rilevamenti di rischi attivi di un utente siano falsi positivi. Per ulteriori informazioni, vedere l'articolo [fornire feedback sui rilevamenti dei rischi in Azure ad Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-users"></a>Filtrare gli utenti a rischio

Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati sugli utenti a rischio usando i campi predefiniti seguenti:

- Name
- Nome utente
- Stato del rischio
- Livello di rischio
- Type
- Stato

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/06.png)

Il filtro **Nome** consente di specificare il nome o il nome dell'entità utente (UPN) per l'utente interessato.

Il filtro **Stato rischio** consente di selezionare:

- A rischio
- Con correzione
- Ignorato

Il filtro **Livello rischio** consente di selezionare:

- Alto
- Medio
- Basso

Il filtro **Tipo** consente di selezionare:

- Guest
- Member

Il filtro **Stato** consente di selezionare:

- Eliminata
- Attivo

### <a name="download-risky-users-data"></a>Download dei dati sugli utenti a rischio

È possibile scaricare i dati degli utenti rischiosi se si desidera utilizzarli al di fuori della portale di Azure. Facendo clic su Scarica viene creato un file CSV dei record 2.500 più recenti. 

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/07.png)

Per personalizzare la visualizzazione elenco, fare clic su Colonne nella barra degli strumenti.
 
In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.
 
Per altre informazioni su un utente a rischio, fare clic sul pannello dei dettagli per espanderlo

## <a name="risky-sign-ins-report"></a>Report sugli accessi a rischio

Le informazioni contenute nel report accessi a rischio consentono di rispondere a domande come le seguenti:

- Quanti accessi riusciti erano presenti con rilevamento dei rischi degli indirizzi IP anonimi nell'ultima settimana?
- Quali utenti sono stati confermati compromessi nell'ultimo mese?
- Quali utenti hanno effettuato accessi a rischio al portale Office 365?

Il primo punto di approccio a questo report è la sezione **Analisis dei problemi** nella pagina di sicurezza.

![Report sugli accessi a rischio](./media/howto-investigate-risky-users-signins/02.png)

Il report accessi a rischio presenta una vista predefinita che include:

- Date
- Utente
- Applicazione
- Stato accesso
- Stato del rischio
- Livello di rischio (aggregato)
- Livello di rischio (in tempo reale)
- Accesso condizionale
- Autenticazione a più fattori obbligatoria  

![Report sugli accessi a rischio](./media/howto-investigate-risky-users-signins/09.png)

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/11.png)

La finestra di dialogo a colonne permette di visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

Facendo clic su un elemento nella visualizzazione elenco, è possibile ottenere tutti i dettagli disponibili sull'elemento in una visualizzazione orizzontale.

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/12.png)

La vista Dettagli mostra:

- Info di base
- Informazioni dispositivo
- Informazioni di rischio
- Informazioni su MFA
- Accesso condizionale

È anche possibile:

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/13.png)

- Confermare un accesso compromesso 
- Conferma sicuro

Per ulteriori informazioni, vedere l'articolo [fornire feedback sui rilevamenti dei rischi in Azure ad Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-sign-ins"></a>Filtrare gli accessi a rischio

Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati sugli utenti a rischio usando i campi predefiniti seguenti:

- Utente
- Applicazione
- Stato accesso
- Stato del rischio
- Livello di rischio (aggregato)
- Livello di rischio (in tempo reale)
- Accesso condizionale
- Date
- Tipo di livello di rischio

![Report sugli accessi a rischio](./media/howto-investigate-risky-users-signins/14.png)

Il filtro **Nome** consente di specificare il nome o il nome dell'entità utente (UPN) per l'utente interessato.

Il filtro **Applicazione** consente di specificare l'app cloud a cui l’utente ha provato ad accedere.

Il filtro **Stato accesso** permette di selezionare:

- Tutti
- Riuscito
- Errore

Il filtro **Stato rischio** consente di selezionare:

- A rischio
- Confermato compromesso
- Confermato sicuro
- Ignorato
- Con correzione

Il filtro **Livello di rischio (aggregato)** consente di selezionare:

- Alto
- Medio
- Basso

Il filtro **Livello di rischio (in tempo reale)** consente di selezionare:

- Alto
- Medio
- Basso

Il filtro di **accesso condizionale** consente di selezionare:

- Tutti
- Non applicato
- Riuscito
- Errore

Il filtro **Date** (Data) permette di definire un intervallo di tempo per i dati restituiti.
I valori possibili sono:

- Ultimo mese
- Ultimi 7 giorni
- Ultime 24 ore
- Intervallo di tempo personalizzato

### <a name="download-risky-sign-ins-data"></a>Download dei dati sugli accessi a rischio

È possibile scaricare i dati degli accessi a rischio se si desidera utilizzarli al di fuori della portale di Azure. Facendo clic su Scarica viene creato un file CSV dei record 2.500 più recenti. 

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/15.png)


## <a name="risk-detections-report"></a>Report rilevamento rischi

Il rapporto sui rilevamenti dei rischi fornisce informazioni dettagliate su ogni rilevamento dei rischi di Identity Protection nel tenant.


Il report rilevamento rischi include una visualizzazione predefinita che mostra:

- Date

- Utente

- Indirizzo IP

- Location

- Tipo di rilevamento

- Stato del rischio

- Livello di rischio

- ID richiesta
 

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti. La finestra di dialogo a colonne permette di visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

Facendo clic su un elemento nella visualizzazione elenco, è possibile ottenere tutti i dettagli disponibili sull'elemento in una visualizzazione orizzontale.


La visualizzazione dettagli Mostra informazioni aggiuntive sul rilevamento dei rischi.

La visualizzazione dettagli include anche collegamenti a

- Visualizzare il report di rischio dell'utente
- Visualizzare gli accessi dell'utente
- Visualizzare gli accessi a rischio dell'utente
- Visualizzare l'accesso rischioso collegato
- Visualizzare i rilevamenti dei rischi dell'utente


### <a name="filter-risk-detections"></a>Filtra rilevamento rischi

Per limitare i dati segnalati a un livello che funziona automaticamente, è possibile filtrare i dati di rilevamento dei rischi usando i campi predefiniti seguenti:

- Tempo di rilevamento
- Tipo di rilevamento
- Stato del rischio
- Livello di rischio
- Utente
- Nome utente
- Indirizzo IP
- Location
- Temporizzazione rilevamento
- Attività 
- Source
- Tipo di emittente del token
- ID richiesta
- ID correlazione


Il filtro **tempo di rilevamento** consente di definire un intervallo di tempo per i dati restituiti. Questo filtro consente di selezionare:
- Ultimi 90 giorni
- Ultimi 30 giorni
- Ultimi 7 giorni
- Ultime 24 ore
- Intervallo di tempo personalizzato

Il filtro del **tipo di rilevamento** consente di specificare il tipo di rilevamento dei rischi, ad esempio le proprietà di accesso non note.

Il filtro **Stato rischio** consente di selezionare:

- A rischio
- Confermato compromesso
- Confermato sicuro
- Ignorato
- Con correzione

Il filtro **Livello rischio** consente di selezionare:

- Alto
- Medio
- Basso

Il filtro **utente** consente di specificare il nome o l'ID oggetto dell'utente a cui si è interessati.

Il filtro del **nome** utente consente di specificare il nome dell'entità utente (UPN) dell'utente a cui si è interessati.

Il filtro **intervallo di rilevamento** consente di specificare se il rilevamento era in tempo reale o offline. Nota: È possibile sfidare gli accessi con i rilevamenti in tempo reale usando i criteri di rischio di accesso. 

Il filtro **attività** consente di specificare se il rilevamento è stato collegato a un accesso, ad esempio un indirizzo IP anonimo, o a un utente, ad esempio le credenziali perse.

Il filtro di **origine** consente di specificare l'origine del rilevamento dei rischi, ad esempio Azure AD o Microsoft cloud app Security. 

Il filtro del **tipo di emittente del token** consente di specificare la posizione in cui è stato emesso il token, ad esempio Azure ad o ad Federation Services.


### <a name="download-risk-detections-data"></a>Scarica i dati di rilevamento dei rischi

È possibile scaricare i dati di rilevamento dei rischi se si desidera utilizzarli al di fuori della portale di Azure. Facendo clic su Scarica viene creato un file CSV dei record 5.000 più recenti. 

## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview-v2.md).
