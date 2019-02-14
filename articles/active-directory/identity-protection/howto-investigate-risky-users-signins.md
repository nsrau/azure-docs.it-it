---
title: Come analizzare utenti e accessi a rischio in Azure Active Directory Identity Protection (procedura aggiornata) | Microsoft Docs
description: Informazioni su come analizzare utenti e accessi a rischio in Azure Active Directory Identity Protection (procedura aggiornata).
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.author: markvi
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 546d9f6771ea75c2601630850f4e9ef082fd5623
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210986"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Procedura: Analizzare utenti e accessi a rischio 


Usando i report sugli accessi e gli utenti a rischio è possibile analizzare e ottenere informazioni approfondite sui potenziali rischi nell'ambiente. Con la possibilità di filtrare e ordinare gli accessi e gli utenti a rischio, si possono comprendere meglio le potenziali intrusioni nella propria organizzazione. 


## <a name="risky-users-report"></a>Report utenti a rischio

Le informazioni contenute nel report utenti a rischio consentono di rispondere a domande come le seguenti:

- Quali sono gli utenti ad alto rischio?
- Quali utenti presentano lo stato di rischio con correzione?



Il primo punto di approccio a questo report è la sezione **Analisis dei problemi** nella pagina di sicurezza.

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/01.png)


Il report utenti a rischio dispone di una vista predefinita che include:

- NOME

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

- Eventi di rischio non collegati a un accesso

- Cronologia rischio



È anche possibile:

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/08.png)

- Visualizzare tutti i collegamenti agli accessi per vedere il report degli accessi per tale utente.

- Visualizzare tutti gli accessi a rischio per vedere tutti gli accessi dell'utente che sono stati contrassegnati come rischiosi.

- Reimpostare la password dell'utente se si ritiene che la sua identità sia stata compromessa.

- Ignorare il rischio utente se si ritiene che gli eventi di rischio attivi di un utente siano falsi positivi. Per altre informazioni, vedere [How to improve the detection accuracy](howto-improve-detection-accuracy.md)(Come migliorare la precisione del rilevamento).



### <a name="filter-risky-users"></a>Filtrare gli utenti a rischio

Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati sugli utenti a rischio usando i campi predefiniti seguenti:

- NOME

- Username

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
- Media
- Basso


Il filtro **Tipo** consente di selezionare:

- Guest
- Membro

Il filtro **Stato** consente di selezionare:

- Deleted
- Attivo


### <a name="download-risky-users-data"></a>Download dei dati sugli utenti a rischio

È possibile scaricare i dati degli utenti a rischio per usarli esternamente al portale di Azure. Facendo clic su Scarica, viene creato un file CSV dei 5.000 record più recenti. 

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/07.png)


Per personalizzare la visualizzazione elenco, fare clic su Colonne nella barra degli strumenti.
 
In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.
 
Per altre informazioni su un utente a rischio, fare clic sul pannello dei dettagli per espanderlo

 



## <a name="risky-sign-ins-report"></a>Report sugli accessi a rischio

Le informazioni contenute nel report accessi a rischio consentono di rispondere a domande come le seguenti:

- Quanti accessi per i quali sono stati rilevati eventi di rischio con indirizzo IP anonimo sono stati effettuati nell'ultima settimana?

- Quali utenti sono stati confermati compromessi nell'ultimo mese?

- Quali utenti hanno effettuato accessi a rischio al portale Office 365?




Il primo punto di approccio a questo report è la sezione **Analisis dei problemi** nella pagina di sicurezza.

![Report sugli accessi a rischio](./media/howto-investigate-risky-users-signins/02.png)

Il report accessi a rischio presenta una vista predefinita che include:

- Data

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

- Confermare un accesso sicuro

Per altre informazioni, vedere [How to improve the detection accuracy](howto-improve-detection-accuracy.md)(Come migliorare la precisione del rilevamento).




### <a name="filter-risky-sign-ins"></a>Filtrare gli accessi a rischio

Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati sugli utenti a rischio usando i campi predefiniti seguenti:

- Utente
- Applicazione
- Stato accesso
- Stato del rischio
- Livello di rischio (aggregato)
- Livello di rischio (in tempo reale)
- Accesso condizionale
- Data
- Tipo di livello di rischio

![Report sugli accessi a rischio](./media/howto-investigate-risky-users-signins/14.png)



Il filtro **Nome** consente di specificare il nome o il nome dell'entità utente (UPN) per l'utente interessato.

Il filtro **Applicazione** consente di specificare l'app cloud a cui l’utente ha provato ad accedere.

Il filtro **Stato accesso** permette di selezionare:

- Tutti
- Success
- Esito negativo


Il filtro **Stato rischio** consente di selezionare:

- A rischio
- Confermato compromesso
- Confermato sicuro
- Ignorato
- Con correzione


Il filtro **Livello di rischio (aggregato)** consente di selezionare:

- Alto
- Media
- Basso

Il filtro **Livello di rischio (in tempo reale)** consente di selezionare:

- Alto
- Media
- Basso


Il filtro **Accesso condizionale** consente di selezionare:

- Tutti
- Non applicato
- Success
- Esito negativo


Il filtro **Date** (Data) permette di definire un intervallo di tempo per i dati restituiti.
I valori possibili sono:

- Ultimo mese
- Ultimi 7 giorni
- Ultime 24 ore
- Intervallo di tempo personalizzato





### <a name="download-risky-sign-ins-data"></a>Download dei dati sugli accessi a rischio

È possibile scaricare i dati relativi agli accessi a rischio per usarli esternamente al portale di Azure. Facendo clic su Scarica, viene creato un file CSV dei 5.000 record più recenti. 

![Report utenti a rischio](./media/howto-investigate-risky-users-signins/15.png)



## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview-v2.md).
