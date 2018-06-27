---
title: Report delle attività di accesso nel portale di Azure Active Directory | Microsoft Docs
description: Introduzione ai report delle attività di accesso nel portale di Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/17/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d9c2f210204122947c24eb42f643450537f3b9a8
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231990"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di accesso nel portale di Azure Active Directory

I report di Azure Active Directory (Azure AD) nel [portale di Azure](https://portal.azure.com) offrono tutte le informazioni necessarie per determinare lo stato dell'ambiente.

L'architettura di reporting in Azure Active Directory include i componenti seguenti:

- **Attività** 
    - **Attività di accesso** : informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso utente
    - **Log di controllo**: informazioni relative alle attività di sistema sulla gestione di utenti e gruppi, sulle applicazioni gestite e sulle attività di directory.
- **Sicurezza** 
    - **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. Per informazioni dettagliate, vedere Accessi a rischio.
    - **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. Per informazioni dettagliate, vedere Utenti contrassegnati per il rischio.

In questo argomento viene offerta una panoramica delle attività di accesso.

## <a name="prerequisites"></a>prerequisiti

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
* Gli utenti con ruolo Amministratore della sicurezza, Ruolo con autorizzazioni di lettura per la sicurezza o Lettore report
* Gli amministratori globali
* Qualsiasi utente (non amministratore) può visualizzare i propri accessi 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?
* Per visualizzare il report completo delle attività di accesso, è necessario che al tenant sia associata una licenza di Azure AD Premium


## <a name="sign-in-activities"></a>Attività di accesso

Le informazioni contenute nel report relativo all'accesso utente consentono di rispondere a domande come le seguenti:

* Qual è il modello di accesso di un utente?
* Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
* Qual è lo stato di questi accessi?

Il primo punto di ingresso a tutte le attività di accesso è **Accessi** nella sezione **Attività** di Azure Active


![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/61.png "Attività di accesso")


Un log di accesso ha una visualizzazione elenco predefinita che include:

- Data di accesso
- Utente correlato
- Applicazione a cui l'utente ha eseguito l'accesso
- Stato dell'accesso
- Stato di rilevamento rischi
- Stato del requisito di autenticazione a più fattori (MFA) 

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/01.png "Attività di accesso")

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/19.png "Attività di accesso")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/02.png "Attività di accesso")

Facendo clic su un elemento nella visualizzazione elenco, è possibile ottenere tutti i dettagli disponibili sull'elemento in una visualizzazione orizzontale.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/03.png "Attività di accesso")


## <a name="filter-sign-in-activities"></a>Filtrare le attività di accesso

Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati di accesso usando i campi predefiniti seguenti:

- Utente
- Applicazione
- Stato accesso
- Status of the risk detection (Stato di rilevamento rischi)
- Data


![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/04.png "Attività di accesso")

Il filtro **Utente** permette di specificare il nome o il nome dell'entità utente (UPN) per l'utente richiesto. 

Il filtro **Applicazione** permette di specificare il nome dell'applicazione richiesta.  

Il filtro **Stato accesso** permette di selezionare:

- Tutti 
- Success
- Esito negativo

Il filtro **Risk Detected** (Rischio rilevato) permette di selezionare:

- Tutti
- Sì
- No  


Il filtro **Date** (Data) permette di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 1 mese
- 7 giorni
- 24 ore
- Intervallo di tempo personalizzato

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare un'ora di inizio e un'ora di fine.

Se si aggiungono altri campi alla visualizzazione degli accessi, questi campi verranno aggiunti automaticamente all'elenco dei filtri. Ad esempio, se si aggiunge il campo **App client** all'elenco, si otterrà un'altra opzione di filtro che consente di impostare i filtri seguenti:

- Browser      
- Exchange ActiveSync (supportato)               
- Exchange ActiveSync (non supportato)
- Altri client               
    - IMAP
    - MAPI
    - Client Office precedenti
    - POP
    - SMTP


![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/12.png "Attività di accesso")


> [!TIP] 
> Oltre ai filtri predefiniti, tutti gli altri campi che si aggiungono alla visualizzazione degli accessi diventano campi di filtro.


## <a name="download-sign-in-activities"></a>Scaricare le attività di accesso

Se occorre usare i dati delle attività di accesso all'esterno del portale di Azure, è possibile scaricarli. Oltre a un pulsante di download, il portale di Azure offre un'opzione per la generazione di uno script per scaricare i dati.  

![Download](./media/active-directory-reporting-activity-sign-ins/71.png "Download")

Facendo clic su **Scarica**, viene creato un file CSV dei 5.000 record più recenti. Se occorre una maggiore flessibilità, è possibile usare la soluzione tramite script. Facendo clic su **Script** si crea uno script di PowerShell che include tutti i filtri impostati. Scaricare ed eseguire lo script in **modalità amministratore** per generare il file CSV. Oltre che dall'implementazione tecnica, il numero di record che è possibile scaricare è limitato anche dai [criteri di conservazione dei report di Azure Active Directory](active-directory-reporting-retention.md).  



## <a name="sign-in-activities-shortcuts"></a>Collegamenti alle attività di accesso

Oltre ad Azure Active Directory, il portale di Azure offre due ulteriori punti di ingresso ai dati sulle attività di accesso:

- Panoramica di protezione e sicurezza delle identità
- Utenti
- Gruppi
- Applicazioni aziendali


### <a name="users-sign-ins-activities"></a>Attività di accesso degli utenti

Le informazioni contenute nel report relativo all'accesso utente consentono di rispondere a domande come le seguenti:

- Qual è il modello di accesso di un utente?
- Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
- Qual è lo stato di questi accessi?



Il punto di ingresso a questi dati è il grafico relativo agli accessi utente nella pagina della panoramica di **protezione e sicurezza delle identità**. Il grafico degli accessi utente visualizza le aggregazioni settimanali degli accessi per tutti gli utenti in un determinato periodo di tempo. Il periodo di tempo predefinito è di 30 giorni.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/06.png "Attività di accesso")

Quando si fa clic su un giorno nel grafico degli accessi, si ottiene una panoramica delle attività di accesso per tale giorno.


Ogni riga dell'elenco delle attività di accesso mostra:

* Chi ha effettuato l'accesso?
* Qual era l'applicazione di destinazione dell'accesso?
* Qual è lo stato dell'accesso?
* Qual è lo stato MFA dell'accesso?

Facendo clic su un elemento, si ottengono altri dettagli sull'operazione di accesso:

- ID utente
- Utente
- Username
- ID applicazione
- Applicazione
- Client
- Località
- Indirizzo IP
- Data
- Autenticazione a più fattori obbligatoria
- Stato accesso

 
Nella pagina **Utenti** è possibile accedere a una panoramica completa di tutti i accessi degli utenti facendo clic su **Accessi** nella sezione **Attività**.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/08.png "Attività di accesso")




## <a name="usage-of-managed-applications"></a>Utilizzo di applicazioni gestite

Con una visualizzazione dei dati di accesso basata sulle applicazioni, è possibile rispondere a domande come:

* Chi sta usando le applicazioni?
* Quali sono le prime 3 applicazioni nell'organizzazione?
* Di recente è stata implementata un'applicazione. Come sta andando?

Il punto di ingresso a questi dati è il report sulle *prime 3 applicazioni nell'organizzazione negli ultimi 30 giorni* nella sezione **Panoramica** della pagina **Applicazioni aziendali**.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/10.png "Attività di accesso")

Il grafico sull'utilizzo delle app visualizza le aggregazioni settimanali degli accessi per le prime 3 applicazioni in un determinato periodo di tempo. Il periodo di tempo predefinito è di 30 giorni.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/47.png "Attività di accesso")

Se si preferisce, è possibile mettere in evidenza un'applicazione specifica.


![Report](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Report")

Quando si fa clic su un giorno nel grafico dell'utilizzo dell'app, si ottiene un elenco dettagliato delle attività di accesso.




L'opzione **Accessi** offre una panoramica completa di tutti gli eventi di accesso nell'applicazione.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/11.png "Attività di accesso")



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui codici di errore dell'attività di accesso, vedere [Codici di errore del report delle attività di accesso nel portale di Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).

