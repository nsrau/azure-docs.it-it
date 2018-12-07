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
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d3705267520087e098e3e2bcc55e677935dc6097
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275334"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di accesso nel portale di Azure Active Directory

L'architettura di report in Azure Active Directory (Azure AD) include i componenti seguenti:

- **Attività** 
    - **Accessi**: informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
    - **Log di controllo** - [log di controllo](concept-audit-logs.md) forniscono informazioni sulle attività di sistema relative a gestione di utenti e gruppi, applicazioni gestite e attività di directory.
- **Sicurezza** 
    - **Accessi a rischio**: un [accesso a rischio](concept-risky-sign-ins.md) indica un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.
    - **Utenti contrassegnati per il rischio**: un [utente a rischio](concept-user-at-risk.md) indica un account utente che potrebbe essere stato compromesso.

Questo articolo fornisce una panoramica del report degli accessi.

## <a name="prerequisites"></a>Prerequisiti

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
* Utenti con il ruolo di Amministratore della sicurezza oppure con un ruolo con autorizzazioni di lettura per la sicurezza e per i report
* Amministratori globali
* Qualsiasi utente (non amministratore) può inoltre visualizzare i propri accessi 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?
* Per visualizzare il report completo delle attività di accesso, è necessario che al tenant sia associata una licenza di Azure AD Premium

## <a name="sign-ins-report"></a>Report sugli accessi

Il report relativo agli accessi utente fornisce le risposte alle domande seguenti:

* Qual è il modello di accesso di un utente?
* Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
* Qual è lo stato di questi accessi?

Per accedere al report degli accessi, selezionare **Accessi** nella sezione **Attività** del pannello **Azure Active Directory** nel [portale di Azure](https://portal.azure.com).

![Attività di accesso](./media/concept-sign-ins/61.png "Attività di accesso")

> [!IMPORTANT]
> Il report degli accessi mostra solo gli accessi **interattivi**, ovvero gli accessi eseguiti manualmente da un utente usando nome utente e password. Gli accessi non interattivi, ad esempio l'autenticazione da servizio a servizio, non vengono visualizzati nel report degli accessi. 

Un log di accesso ha una visualizzazione elenco predefinita che include:

- Data di accesso
- Utente correlato
- Applicazione a cui l'utente ha eseguito l'accesso
- Stato dell'accesso
- Stato di rilevamento rischi
- Stato del requisito di autenticazione a più fattori (MFA)

![Attività di accesso](./media/concept-sign-ins/01.png "Attività di accesso")

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Attività di accesso](./media/concept-sign-ins/19.png "Attività di accesso")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Attività di accesso](./media/concept-sign-ins/02.png "Attività di accesso")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Attività di accesso](./media/concept-sign-ins/03.png "Attività di accesso")

> [!NOTE]
> I clienti possono ora risolvere i problemi dei criteri di accesso condizionale tramite tutti i report di accesso. Facendo clic sulla scheda di **Accesso condizionale** per un record di accesso, i clienti possono esaminare lo stato di accesso condizionale e approfondire i dettagli dei criteri applicati per l'accesso e il risultato per ogni criterio.
> Per altre informazioni, vedere [Domande frequenti sulle informazioni di CA in tutti gli accessi](reports-faq.md#conditional-access).

![Attività di accesso](./media/concept-sign-ins/ConditionalAccess.png "Attività di accesso")


## <a name="filter-sign-in-activities"></a>Filtrare le attività di accesso

Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati di accesso usando i campi predefiniti seguenti:

- Utente
- Applicazione
- Stato accesso
- Accesso condizionale
- Data

![Attività di accesso](./media/concept-sign-ins/04.png "Attività di accesso")

Il filtro **Utente** permette di specificare il nome o il nome dell'entità utente (UPN) per l'utente richiesto.

Il filtro **Applicazione** permette di specificare il nome dell'applicazione richiesta.

Il filtro **Stato accesso** permette di selezionare:

- Tutti
- Success
- Esito negativo

Il filtro **Accesso condizionale** consente di selezionare lo stato dei criteri di accesso condizionale per l'accesso:

- Tutti
- Non applicato
- Success
- Esito negativo

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


![Attività di accesso](./media/concept-sign-ins/12.png "Attività di accesso")


## <a name="download-sign-in-activities"></a>Scaricare le attività di accesso

È possibile [scaricare i dati relativi agli accessi](quickstart-download-sign-in-report.md) per usarli esternamente al portale di Azure. Facendo clic su **Scarica**, viene creato un file CSV dei 5.000 record più recenti.  Oltre a un pulsante di download, il portale di Azure offre un'opzione per [generare uno script per scaricare i dati](tutorial-signin-logs-download-script.md).  

![Download](./media/concept-sign-ins/71.png "Download")

Se occorre una maggiore flessibilità, è possibile usare la soluzione tramite script. Facendo clic su **Script** si crea uno script di PowerShell che include tutti i filtri impostati. Scaricare ed eseguire lo script in **modalità amministratore** per generare il file CSV. 

> [!IMPORTANT]
> Il numero di record che è possibile scaricare è limitato dai [criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).  

### <a name="running-the-script-on-a-windows-10-machine"></a>Esecuzione dello script in un computer Windows 10

Se si desidera eseguire lo script in un computer **Windows 10**, è necessario eseguire prima alcuni passaggi aggiuntivi. 

1. Installare il [modulo AzureRM](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l).
2. Importare il modulo aprendo un prompt di PowerShell ed eseguendo il comando **Import-Module AzureRM**.
3. Eseguire **Set-ExecutionPolicy unrestricted** e scegliere **Sì a tutti**. 
4. Ora è possibile eseguire lo script di PowerShell scaricato in modalità amministratore per generare il file CSV.

## <a name="sign-ins-data-shortcuts"></a>Tasti di scelta rapida per i dati degli accessi

Oltre ad Azure AD, il portale di Azure fornisce altri punti di ingresso ai dati relativi agli accessi:

- Panoramica Identity Security e Protection
- Utenti
- Gruppi
- Applicazioni aziendali

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dati degli accessi degli utenti in Identity Security e Protection

Il grafico degli accessi utente nella pagina di panoramica **Identity Security e Protection** mostra le aggregazioni settimanali degli accessi per tutti gli utenti in un determinato periodo di tempo. Il periodo di tempo predefinito è di 30 giorni.

![Attività di accesso](./media/concept-sign-ins/06.png "Attività di accesso")

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

![Attività di accesso](./media/concept-sign-ins/08.png "Attività di accesso")

## <a name="usage-of-managed-applications"></a>Utilizzo di applicazioni gestite

Con una visualizzazione dei dati di accesso basata sulle applicazioni, è possibile rispondere a domande come:

* Chi sta usando le applicazioni?
* Quali sono le prime 3 applicazioni nell'organizzazione?
* Di recente è stata implementata un'applicazione. Come sta andando?

Il punto di ingresso a questi dati sono le prime 3 applicazioni nell'organizzazione nel report sugli ultimi 30 giorni della sezione **Panoramica** in **Applicazioni aziendali**.

![Attività di accesso](./media/concept-sign-ins/10.png "Attività di accesso")

Il grafico sull'utilizzo delle app visualizza le aggregazioni settimanali degli accessi per le prime 3 applicazioni in un determinato periodo di tempo. Il periodo di tempo predefinito è di 30 giorni.

![Attività di accesso](./media/concept-sign-ins/47.png "Attività di accesso")

Se si preferisce, è possibile mettere in evidenza un'applicazione specifica.

![Report](./media/concept-sign-ins/single_spp_usage_graph.png "Report")

Quando si fa clic su un giorno nel grafico dell'utilizzo dell'app, si ottiene un elenco dettagliato delle attività di accesso.

L'opzione **Accessi** offre una panoramica completa di tutti gli eventi di accesso nell'applicazione.

![Attività di accesso](./media/concept-sign-ins/11.png "Attività di accesso")

## <a name="next-steps"></a>Passaggi successivi

* [Codici di errore del report delle attività di accesso](reference-sign-ins-error-codes.md)
* [Criteri di conservazione dei report di Azure AD](reference-reports-data-retention.md)
* [Latenze dei report di Azure AD](reference-reports-latencies.md)

