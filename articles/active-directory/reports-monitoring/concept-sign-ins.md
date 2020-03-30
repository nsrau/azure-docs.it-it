---
title: Report delle attività di accesso nel portale di Azure Active Directory | Microsoft Docs
description: Introduzione ai report delle attività di accesso nel portale di Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246518"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di accesso nel portale di Azure Active Directory

L'architettura di report in Azure Active Directory (Azure AD) include i componenti seguenti:

- **Attività** 
    - **Accessi**: informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
    - **Registri di controllo** - [I log](concept-audit-logs.md) di controllo forniscono informazioni sulle attività di sistema relative alla gestione di utenti e gruppi, alle applicazioni gestite e alle attività della directory.
- **Sicurezza** 
    - **Adesempiomenti rischiosi:** un [accesso rischioso](concept-risky-sign-ins.md) è un indicatore di un tentativo di accesso da parte di un utente che non è il legittimo proprietario di un account utente.
    - **Utenti contrassegnati per il rischio:** un [utente rischioso](concept-user-at-risk.md) è un indicatore per un account utente che potrebbe essere stato compromesso.

Questo articolo offre una panoramica del report di accesso.

## <a name="prerequisites"></a>Prerequisiti

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?

* Utenti nei ruoli Amministratore sicurezza, Lettore sicurezza, Lettore globale e Lettore report
* Amministratori globali
* Qualsiasi utente (non amministratore) può visualizzare i propri accessi 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?

- Il report dell'attività di accesso è disponibile in [tutte le edizioni di Azure AD.](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)

- Se si desidera accedere ai dati di accesso usando un'API, il tenant deve disporre di una licenza [di Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) associata.



## <a name="sign-ins-report"></a>Report sugli accessi

Il report relativo agli accessi utente fornisce le risposte alle domande seguenti:

* Qual è il modello di accesso di un utente?
* Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
* Qual è lo stato di questi accessi?

Nel menu del portale di [Azure](https://portal.azure.com) selezionare **Azure Active Directory**oppure cercare e selezionare Azure Active **Directory** da qualsiasi pagina.

![Selezionare Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

In **Monitoraggio**selezionare **Invii** per aprire il [report Invii](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Attività di accesso](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Attività di accesso")

Potrebbero essere necessari fino a due ore prima che alcuni record di accesso vengano visualizzati nel portale.

> [!IMPORTANT]
> Il report degli accessi mostra solo gli accessi **interattivi**, ovvero gli accessi eseguiti manualmente da un utente usando nome utente e password. Gli accessi non interattivi, ad esempio l'autenticazione da servizio a servizio, non vengono visualizzati nel report degli accessi. 

Un log di accesso ha una visualizzazione elenco predefinita che include:

- Data di accesso
- Utente correlato
- L'applicazione a cui l'utente ha effettuato l'accesso
- Stato dell'accesso
- Stato di rilevamento rischi
- Stato del requisito di autenticazione a più fattori (MFA)

![Attività di accesso](./media/concept-sign-ins/sign-in-activity.png "Attività di accesso")

È possibile personalizzare la visualizzazione elenco facendo clic su **Colonne** nella barra degli strumenti.

![Attività di accesso](./media/concept-sign-ins/19.png "Attività di accesso")

La finestra di dialogo **Colonne** consente di accedere agli attributi selezionabili. In un report di accesso non è possibile avere campi con più di un valore per una determinata richiesta di accesso come colonna. Questo è, ad esempio, vero per i dettagli di autenticazione, i dati di accesso condizionale e il percorso di rete.   

![Attività di accesso](./media/concept-sign-ins/columns.png "Attività di accesso")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Attività di accesso](./media/concept-sign-ins/basic-sign-in.png "Attività di accesso")

> [!NOTE]
> I clienti possono ora risolvere i problemi relativi ai criteri di accesso condizionale tramite tutti i report di accesso. Facendo clic sulla scheda **Accesso condizionale** per un record di accesso, i clienti possono esaminare lo stato di accesso condizionale e approfondire i dettagli dei criteri applicati all'accesso e il risultato per ogni criterio.
> Per altre informazioni, vedere [Domande frequenti sulle informazioni di CA in tutti gli accessi](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrare le attività di accesso

In primo luogo, restringere i dati segnalati a un livello che funziona per voi. In secondo luogo, filtrare i dati di accesso utilizzando il campo data come filtro predefinito. Azure AD offre un'ampia gamma di filtri aggiuntivi che è possibile impostare:Azure AD provides you with a broad range of additional filters you can set:

![Attività di accesso](./media/concept-sign-ins/04.png "Attività di accesso")

**ID richiesta:** l'ID della richiesta a cui tieni.

**Utente:** il nome o il nome dell'entità utente (UPN) dell'utente a cui si è interessati.

**Applicazione:** nome dell'applicazione di destinazione.
 
**Stato** - Lo stato di accesso che ti interessa:

- Operazione completata

- Operazioni non riuscite

- Interrotto


**Indirizzo IP** : l'indirizzo IP del dispositivo utilizzato per connettersi al tenant.

La **posizione** - La posizione da cui è stata avviata la connessione:

- city

- Stato / Provincia

- Paese/Area geografica


**Risorsa:** nome del servizio utilizzato per l'accesso.


**ID risorsa:** ID del servizio utilizzato per l'accesso.


**App client:** il tipo di app client usata per connettersi al tenant:

![Filtro app client](./media/concept-sign-ins/client-app-filter.png)


|Nome|Autenticazione moderna|Descrizione|
|---|:-:|---|
|SMTP autenticato| |Utilizzato dai client POP e IMAP per inviare messaggi di posta elettronica.|
|Autodiscover| |Utilizzato dai client Outlook e EAS per trovare e connettersi alle cassette postali in Exchange Online.|
|Exchange ActiveSync| |Questo filtro mostra tutti i tentativi di accesso in cui è stato tentato il protocollo EAS.|
|Browser|![Controllo](./media/concept-sign-ins/check.png)|Mostra tutti i tentativi di accesso degli utenti che utilizzano i browser Web|
|Exchange ActiveSync| | Mostra tutti i tentativi di accesso da parte di utenti con applicazioni client che utilizzano Exchange ActiceSync per connettersi a Exchange Online|
|PowerShell per Exchange Online| |Utilizzato per connettersi a Exchange Online con PowerShell remoto. Se si blocca l'autenticazione di base per Exchange Online PowerShell, è necessario utilizzare il modulo PowerShell di Exchange Online per connettersi. Per istruzioni, vedere [Connettersi a Exchange Online PowerShell utilizzando l'autenticazione a più fattori](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Servizi Web Exchange| |Interfaccia di programmazione utilizzata da Outlook, Outlook per Mac e app di terze parti.|
|IMAP4| |Un client di posta legacy che utilizza IMAP per recuperare la posta elettronica.|
|MAPI su HTTP| |Utilizzato da Outlook 2010 e versioni successive.|
|App per dispositivi mobili e client desktop|![Controllo](./media/concept-sign-ins/check.png)|Mostra tutti i tentativi di accesso degli utenti che utilizzano app per dispositivi mobili e client desktop.|
|Rubrica fuori rete| |Copia delle raccolte di elenchi di indirizzi scaricate e utilizzate da Outlook.|
|Outlook via Internet (RPC su HTTP)| |Utilizzato da Outlook 2016 e versioni precedenti.|
|Servizio Outlook| |Utilizzato dall'app Posta e Calendario per Windows 10.|
|POP3| |Un client di posta legacy che utilizza POP3 per recuperare la posta elettronica.|
|Creazione di servizi Web di report| |Utilizzato per recuperare i dati del report in Exchange Online.|
|Altri client| |Mostra tutti i tentativi di accesso da parte degli utenti in cui l'app client non è inclusa o sconosciuta.|



**Sistema operativo:** il sistema operativo in esecuzione nel dispositivo ha usato l'accesso al tenant. 


**Browser dispositivo:** se la connessione è stata avviata da un browser, questo campo consente di filtrare in base al nome del browser.


**ID correlazione:** l'ID di correlazione dell'attività.




**Accesso condizionale** - Lo stato delle regole di accesso condizionale applicate

- **Non applicato:** nessun criterio applicato all'utente e all'applicazione durante l'accesso.

- **Operazione riuscita:** uno o più criteri di accesso condizionale applicati all'utente e all'applicazione (ma non necessariamente alle altre condizioni) durante l'accesso. 

- **Errore:** uno o più criteri di accesso condizionale applicati e non sono stati soddisfatti durante l'accesso.









## <a name="download-sign-in-activities"></a>Scaricare le attività di accesso

Fare clic sull'opzione **Download** per creare un file CSV o JSON dei 250.000 record più recenti. Iniziare con scaricare i dati di accesso se si vuole usare all'esterno del portale di Azure.Start with download the [sign-ins data](quickstart-download-sign-in-report.md) if you want to work it outside the Azure portal.  

![Scarica](./media/concept-sign-ins/71.png "Download")

> [!IMPORTANT]
> Il numero di record che è possibile scaricare è limitato dai [criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Tasti di scelta rapida per i dati degli accessi

Azure AD e il portale di Azure forniscono entrambi punti di ingresso aggiuntivi ai dati di accesso:Azure AD and the Azure portal both provide you with additional entry points to sign-ins data:

- Panoramica Identity Security e Protection
- Utenti
- Gruppi
- Applicazioni aziendali

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dati degli accessi degli utenti in Identity Security e Protection

Il grafico di accesso utente nella pagina Panoramica della **sicurezza delle identità** mostra le aggregazioni settimanali degli indirizzi. Il valore predefinito per il periodo di tempo è 30 giorni.

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
- Location
- Indirizzo IP
- Data
- Autenticazione a più fattori obbligatoria
- Stato accesso

> [!NOTE]
> gli indirizzi IP vengono rilasciati in modo che non esista una connessione certa tra un IP e la posizione in cui si trova fisicamente il computer con tale indirizzo. Il mapping degli indirizzi IP è complicato dal fatto che provider di telefonia mobile e VPN possono rilasciare indirizzi IP da pool centrali spesso molto distanti dal luogo in cui viene effettivamente usato il dispositivo client. Attualmente nei report di Azure AD la conversione di un indirizzo IP in una posizione fisica è un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni.

Nella pagina **Utenti** è possibile accedere a una panoramica completa di tutti i accessi degli utenti facendo clic su **Accessi** nella sezione **Attività**.

![Attività di accesso](./media/concept-sign-ins/08.png "Attività di accesso")

## <a name="usage-of-managed-applications"></a>Utilizzo di applicazioni gestite

Con una visualizzazione dei dati di accesso basata sulle applicazioni, è possibile rispondere a domande come:

* Chi sta usando le applicazioni?
* Quali sono le prime tre applicazioni dell'organizzazione?
* Come sta la mia nuova applicazione?

Il punto di ingresso a questi dati è le prime tre applicazioni dell'organizzazione. I dati sono contenuti nell'ultimo report degli ultimi 30 giorni nella sezione **Panoramica** in **Applicazioni aziendali**.

![Attività di accesso](./media/concept-sign-ins/10.png "Attività di accesso")

L'utilizzo delle app rappresenta le aggregazioni settimanali degli invii per le prime tre applicazioni in un determinato periodo di tempo. Il periodo di tempo predefinito è di 30 giorni.

![Attività di accesso](./media/concept-sign-ins/graph-chart.png "Attività di accesso")

Se si preferisce, è possibile mettere in evidenza un'applicazione specifica.

![Reporting](./media/concept-sign-ins/single-app-usage-graph.png "Report")

Quando si fa clic su un giorno nel grafico dell'utilizzo dell'app, si ottiene un elenco dettagliato delle attività di accesso.

L'opzione **Accessi** offre una panoramica completa di tutti gli eventi di accesso nell'applicazione.

## <a name="office-365-activity-logs"></a>Log attività di Office 365

È possibile visualizzare i log attività di Office 365 dall'interfaccia di amministrazione di [Microsoft 365.](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center) Si consideri il punto che, attività di Office 365 e log attività di Azure AD condividono un numero significativo di risorse di directory. Solo l'interfaccia di amministrazione di Microsoft 365 offre una visualizzazione completa dei log attività di Office 365. 

È inoltre possibile accedere ai log attività di Office 365 a livello di programmazione utilizzando le API di gestione di [Office 365.](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)

## <a name="next-steps"></a>Passaggi successivi

* [Codici di errore del report delle attività di accesso](reference-sign-ins-error-codes.md)
* [Criteri di conservazione dei report di Azure AD](reference-reports-data-retention.md)
* [Latenze dei report di Azure AD](reference-reports-latencies.md)

