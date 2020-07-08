---
title: Informazioni su Gestione entitlement - Azure AD
description: Panoramica della funzionalità di gestione entitlement di Azure Active Directory, con informazioni su come usarla per gestire l'accesso a gruppi, applicazioni e siti di SharePoint Online per utenti interni ed esterni.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f05fa9f9f31011f04aee0d2bedbcd4c4dad5d39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85338200"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Cos'è Gestione entitlement di Azure AD?

Gestione entitlement di Active Directory (Azure AD) è una funzionalità di [Identity Governance](identity-governance-overview.md) che consente alle organizzazioni di gestire il ciclo di vita di identità e accessi su larga scala, automatizzando i flussi di lavoro delle richieste di accesso, le assegnazioni, le verifiche e la scadenza degli accessi.

Per svolgere il proprio lavoro, i dipendenti delle organizzazioni devono accedere a vari gruppi, applicazioni e siti. La gestione di questo accesso è problematica, in quanto i requisiti cambiano quando ad esempio vengono aggiunte nuove applicazioni o se gli utenti necessitano di diritti di accesso aggiuntivi.  Questo scenario diventa ancora più complicato se si collabora con organizzazioni esterne. Si potrebbe non sapere chi nell'altra organizzazione ha l'esigenza di accedere alle risorse della propria organizzazione, mentre gli utenti dell'altra organizzazione non sapranno quali applicazioni, gruppi o siti sono in uso nella propria.

La funzionalità di gestione entitlement di Azure AD può aiutare a gestire in modo più efficiente l'accesso a gruppi, applicazioni e siti di SharePoint per utenti interni e anche per quelli esterni all'organizzazione che hanno l'esigenza di accedere a tali risorse.

## <a name="why-use-entitlement-management"></a>Perché usare la gestione entitlement?

La gestione dell'accesso dei dipendenti risulta spesso problematica per le organizzazioni aziendali, per questi motivi:

- Gli utenti potrebbero non sapere quali diritti di accesso hanno e, anche se lo sanno, possono avere difficoltà a individuare i singoli diritti per l'approvazione dell'accesso
- Una volta trovato e ricevuto l'accesso a una risorsa, gli utenti potrebbero mantenerlo più a lungo rispetto a quanto sia necessario per scopi aziendali

Questi problemi diventano ancora più complessi per gli utenti che devono accedere a un'altra organizzazione, ad esempio utenti esterni di organizzazioni della supply chain o altri partner aziendali. Ad esempio:

- Nessuno può conoscere tutti gli specifici individui inclusi nelle directory di altre organizzazioni per avere la possibilità di invitarli
- Anche se si riesce a invitare questi utenti, nessuno nell'organizzazione esterna può ricordarsi di gestire in modo coerente tutti gli accessi degli utenti

La gestione entitlement di Azure AD può risultare utile per affrontare queste sfide.  Per altre informazioni su come viene usata la gestione entitlement di Azure AD da altri clienti, vedere i case study di [Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) e [Centrica](https://customers.microsoft.com/story/757467-centrica-energy-azure).  Questo video offre una panoramica della gestione entitlement e dei relativi vantaggi:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Che cosa si può fare con la gestione entitlement?

Ecco alcune funzionalità della gestione entitlement:

- Delegare a non amministratori la possibilità di creare pacchetti di accesso. Questi pacchetti di accesso contengono risorse che gli utenti possono richiedere e i responsabili dei pacchetti di accesso con delega possono definire criteri con regole per gli utenti che possono effettuare richieste e per quelli che devono approvarne l'accesso, oltre a stabilirne la scadenza.
- Selezionare le organizzazioni connesse i cui utenti possono richiedere l'accesso.  Quando un utente che non è ancora presente nella directory richiede l'accesso e riceve l'approvazione, viene automaticamente invitato nella directory e gli viene assegnato l'accesso.  Alla scadenza dell'accesso, se non avrà altre assegnazioni del pacchetto di accesso, il suo account B2B nella directory potrà essere rimosso automaticamente.

Per iniziare, seguire l'[esercitazione sulla creazione del primo pacchetto di accesso](entitlement-management-access-package-first.md). È anche possibile leggere gli [scenari comuni](entitlement-management-scenarios.md) o guardare i video, tra cui

- [Come distribuire la gestione entitlement di Azure AD nell'organizzazione](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Come monitorare e dimensionare l'uso della gestione entitlement di Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Come eseguire deleghe nella gestione entitlement](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Che cosa sono i pacchetti di accesso e quali risorse consentono di gestire?

La gestione entitlement introduce in Azure AD il concetto di *pacchetto di accesso*. Un pacchetto di accesso è un'aggregazione di tutte le risorse a cui un utente deve poter accedere per lavorare su un progetto o svolgere le proprie attività. I pacchetti di accesso vengono usati per regolamentare l'accesso per i dipendenti interni e anche per gli utenti esterni all'organizzazione.

 Ecco i tipi di risorse per cui è possibile gestire l'accesso degli utenti con la gestione entitlement:

- Appartenenza a gruppi di sicurezza di Azure AD
- Appartenenza di gruppi di Microsoft 365 e team
- Assegnazione ad applicazioni aziendali di Azure AD, tra cui applicazioni SaaS e con integrazioni personalizzate che supportano la federazione/Single Sign-On e/o il provisioning
- Appartenenza a siti di SharePoint Online

È anche possibile controllare l'accesso ad altre risorse che si basano su Azure AD gruppi di sicurezza o gruppi di Microsoft 365.  Ad esempio:

- È possibile concedere agli utenti le licenze per Microsoft 365 usando un gruppo di sicurezza Azure AD in un pacchetto di accesso e configurando le [licenze basate sui gruppi](../users-groups-roles/licensing-groups-assign.md) per quel gruppo
- È possibile concedere agli utenti l'accesso per gestire le risorse di Azure usando un gruppo di sicurezza di Azure AD in un pacchetto di e creando un'[assegnazione di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per tale gruppo

## <a name="how-do-i-control-who-gets-access"></a>Come si controlla chi ottiene l'accesso?

Un amministratore o un responsabile dei pacchetti di accesso con delega elenca le risorse (gruppi, app e siti) e i ruoli corrispondenti necessari per gli utenti.

I pacchetti di accesso possono anche includere uno o più *criteri*. Un criterio definisce le regole o i vincoli per l'assegnazione a un pacchetto di accesso. Ogni criterio può essere usato per garantire che solo gli utenti appropriati siano in grado di richiedere l'accesso, che siano presenti responsabili approvazione per la richiesta e che l'accesso a tali risorse sia limitato nel tempo e scada se non rinnovato.

![Pacchetti di accesso e criteri](./media/entitlement-management-overview/elm-overview-access-package.png)

All'interno di ogni criterio un amministratore o un responsabile di pacchetti di accesso definisce

- Gli utenti già esistenti, in genere dipendenti o utenti guest già invitati, oppure le organizzazioni partner di utenti esterni idonei a richiedere l'accesso
- Il processo di approvazione e gli utenti che possono approvare o negare l'accesso
- La durata dell'assegnazione dell'accesso di un utente, una volta approvata, prima che scada

Il diagramma seguente mostra un esempio dei diversi elementi della gestione entitlement. Visualizza un catalogo con due pacchetti di accesso di esempio.

- Il **pacchetto di accesso 1** include un singolo gruppo come risorsa. L'accesso viene definito con un criterio che consente a un set di utenti della directory di richiederlo.
- Il **pacchetto di accesso 2** include un gruppo, un'applicazione e un sito di SharePoint Online come risorse. L'accesso viene definito con due criteri diversi. Il primo criterio consente a un set di utenti della directory di richiedere l'accesso. Il secondo criterio consente agli utenti di una directory esterna di richiedere l'accesso.

![Panoramica della gestione entitlement](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Quando è consigliabile usare i pacchetti di accesso?

I pacchetti di accesso non sostituiscono altri meccanismi per l'assegnazione dell'accesso.  Sono più appropriati in situazioni come le seguenti:

- I dipendenti necessitano di un accesso a tempo limitato per una determinata attività.  Ad esempio, è possibile usare licenze basate su gruppi e un gruppo dinamico per assicurarsi che tutti i dipendenti abbiano una cassetta postale di Exchange e quindi usare i pacchetti di accesso per le situazioni in cui i dipendenti abbiano bisogno di un accesso aggiuntivo, ad esempio per leggere risorse di un altro reparto.
- L'accesso deve essere approvato dal responsabile di un dipendente o da altre persone designate.
- I reparti desiderano gestire i propri criteri di accesso per le loro risorse senza il coinvolgimento dell'IT.  
- Due o più organizzazioni collaborano a un progetto e, di conseguenza, diversi utenti di un'organizzazione dovranno essere inseriti tramite Azure AD B2B per accedere alle risorse di un'altra organizzazione.

## <a name="how-do-i-delegate-access"></a>Come si delega l'accesso?

 I pacchetti di accesso sono definiti in contenitori denominati *cataloghi*.  È possibile avere un singolo catalogo per tutti i pacchetti di accesso oppure designare persone per la creazione di specifici cataloghi di loro proprietà. Un amministratore può aggiungere risorse a qualsiasi catalogo, mentre un non amministratore può aggiungere a un catalogo solo le risorse di sua proprietà. Un proprietario di catalogo può aggiungere altri utenti come comproprietari oppure come responsabili del pacchetto di accesso.  Questi scenari sono descritti ulteriormente nell'articolo [Delega e ruoli nella gestione entitlement di Azure AD](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Riepilogo della terminologia

Per comprendere meglio la gestione entitlement e la relativa documentazione, è possibile fare riferimento all'elenco di termini seguente.

| Termine | Descrizione |
| --- | --- |
| Pacchetto di accesso | Un'aggregazione di risorse necessarie per un team o un progetto regolamentate con criteri. Un pacchetto di accesso è sempre contenuto in un catalogo. È possibile creare un nuovo pacchetto di accesso per uno scenario in cui gli utenti hanno l'esigenza di richiedere l'accesso.  |
| Richiesta di accesso | Una richiesta di accedere alle risorse di un pacchetto di accesso. Una richiesta attraversa in genere un flusso di lavoro di approvazione.  Se approvata, il richiedente riceve un'assegnazione del pacchetto di accesso. |
| Assegnazione | Un'assegnazione di un pacchetto di accesso assicura all'utente tutti i ruoli per le risorse contenute al suo interno.  Le assegnazioni dei pacchetti di accesso in genere hanno un limite di tempo prima della scadenza. |
| Catalogo | Un contenitore di risorse e pacchetti di accesso correlati.  I cataloghi vengono usati per la delega, in modo che i non amministratori possano creare i propri pacchetti di accesso. I proprietari del catalogo possono aggiungervi le risorse di loro proprietà. |
| Autore di cataloghi | Un insieme di utenti autorizzati a creare nuovi cataloghi.  Quando un utente non amministratore autorizzato come autore di cataloghi crea un nuovo catalogo ne diventa automaticamente il proprietario. |
| Organizzazione connessa | Una directory o un dominio di Azure AD esterno con cui la propria organizzazione ha una relazione. Gli utenti di un'organizzazione connessa possono essere specificati in un criterio come autorizzati a richiedere l'accesso. |
| Criterio | Un set di regole che definisce il ciclo di vita dell'accesso, ad esempio il modo in cui gli utenti ottengono l'accesso, chi può approvarlo e la durata tramite un'assegnazione. Un criterio è collegato a un pacchetto di accesso. Un pacchetto di accesso, ad esempio, può avere due criteri, uno per autorizzare le richieste di accesso dei dipendenti e l'altro per autorizzare quelle degli utenti esterni. |
| Risorsa | Un asset, ad esempio un gruppo di Office, un gruppo di sicurezza, un'applicazione o un sito di SharePoint Online, con un ruolo per cui è possibile concedere autorizzazioni a un utente. |
| Directory di risorse | Una directory che contiene una o più risorse da condividere. |
| Ruolo delle risorse | Una raccolta di autorizzazioni associate e definite da una risorsa. Un gruppo ha due ruoli, membro e proprietario. I siti di SharePoint hanno in genere tre ruoli, ma possono averne altri personalizzati. Le applicazioni possono avere ruoli personalizzati. |


## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

I cloud specializzati, ad esempio Azure Germania e Azure Cina (21Vianet), non sono attualmente disponibili per l'uso.

### <a name="how-many-licenses-must-you-have"></a>Quante licenze è necessario avere?

Assicurarsi che la directory abbia un numero di licenze di Azure AD Premium P2 almeno uguale al numero di:

- Utenti membro che **possono** richiedere un pacchetto di accesso.
- Utenti membro e guest che richiedono un pacchetto di accesso.
- Utenti membro e guest che approvano le richieste per un pacchetto di accesso.
- Utenti membro e guest che hanno un'assegnazione diretta a un pacchetto di accesso.

Le licenze di Azure AD Premium P2 **non** sono necessarie per le attività seguenti:

- Non sono necessarie licenze per gli utenti con il ruolo di amministratore globale che configura inizialmente i cataloghi, i pacchetti di accesso e i criteri e che delega le attività amministrative ad altri utenti.
- Non sono necessarie licenze per gli utenti a cui sono state delegate attività amministrative, ad esempio autore del catalogo, proprietario del catalogo e responsabile dei pacchetti di accesso.
- Non sono necessarie licenze per gli utenti guest che **possono** richiedere pacchetti di accesso ma **non** li richiedono.

Per ogni licenza di Azure AD Premium P2 a pagamento acquistata per gli utenti membro (dipendenti), è possibile usare Azure AD B2B per invitare fino a 5 utenti guest. Questi utenti guest possono anche usare le funzionalità di Azure AD Premium P2. Per altre informazioni, vedere [Linee guida sulle licenze per la collaborazione di Azure AD B2B](../b2b/licensing-guidance.md).

Per altre informazioni sulle licenze, vedere [Assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Scenari di licenze di esempio

Ecco alcuni esempi di scenari che consentono di determinare il numero di licenze necessarie.

| Scenario | Calcolo | Numero di licenze |
| --- | --- | --- |
| Un amministratore globale di Woodgrove Bank crea i cataloghi iniziali e delega le attività amministrative ad altri 6 utenti. Uno dei criteri specifica che **tutti i dipendenti** (2.000 persone) possono richiedere un set specifico di pacchetti di accesso. 150 dipendenti richiedono i pacchetti di accesso. | 2\.000 che **possono** richiedere i pacchetti di accesso | 2\.000 |
| Un amministratore globale di Woodgrove Bank crea i cataloghi iniziali e delega le attività amministrative ad altri 6 utenti. Uno dei criteri specifica che **tutti i dipendenti** (2.000 persone) possono richiedere un set specifico di pacchetti di accesso. Un altro criterio specifica che alcuni **utenti del partner Contoso** (guest) possono richiedere gli stessi pacchetti di accesso soggetti ad approvazione. Contoso ha 30.000 utenti. 150 dipendenti richiedono i pacchetti di accesso e gli utenti 10.500 utenti di Contoso richiedono l'accesso. | 2\.000 dipendenti + 500 utenti guest di Contoso che superano il rapporto 1:5 (10.500 - (2.000 * 5)) | 2\.500 |

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare il primo pacchetto di accesso](entitlement-management-access-package-first.md)
- [Scenari comuni](entitlement-management-scenarios.md)
