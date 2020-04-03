---
title: Informazioni su Gestione entitlement - Azure AD
description: Panoramica della gestione dei diritti di Azure Active Directory e come è possibile usarla per gestire l'accesso a gruppi, applicazioni e siti di SharePoint Online per utenti interni ed esterni.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65100e8584eba5c31edc2f9fd8c57ad8bd14c0d1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582536"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Cos'è Gestione entitlement di Azure AD?

La gestione dei diritti di Azure Active Directory (Azure AD) è una funzionalità di governance delle [identità](identity-governance-overview.md) che consente alle organizzazioni di gestire il ciclo di vita di identità e accesso su larga scala, automatizzando i flussi di lavoro delle richieste di accesso, le assegnazioni di accesso, le revisioni e la scadenza.

I dipendenti delle organizzazioni devono accedere a vari gruppi, applicazioni e siti per svolgere il proprio lavoro. La gestione di questo accesso è complessa, in quanto cambiano i requisiti: vengono aggiunte nuove applicazioni o gli utenti necessitano di diritti di accesso aggiuntivi.  Questo scenario diventa più complicato quando si collabora con organizzazioni esterne: è possibile che non si sappia chi nell'altra organizzazione deve accedere alle risorse dell'organizzazione e non sappia quali applicazioni, gruppi o siti dell'organizzazione sta usando.

La gestione dei diritti di Azure AD consente di gestire in modo più efficiente l'accesso a gruppi, applicazioni e siti di SharePoint Online per gli utenti interni e anche per gli utenti esterni all'organizzazione che devono accedere a tali risorse.

## <a name="why-use-entitlement-management"></a>Perché utilizzare la gestione dei diritti?

Le organizzazioni aziendali devono spesso affrontare problemi nella gestione dell'accesso dei dipendenti alle risorse, ad esempio:

- Gli utenti potrebbero non sapere quale accesso dovrebbero avere, e anche se lo fanno, potrebbero avere difficoltà a individuare le persone giuste per approvare il loro accesso
- Una volta che gli utenti trovano e ricevono l'accesso a una risorsa, possono mantenere l'accesso più a lungo di quanto richiesto per scopi aziendali

Questi problemi sono aggravati per gli utenti che necessitano di accesso da un'altra organizzazione, ad esempio utenti esterni provenienti da organizzazioni della supply chain o altri partner commerciali. Ad esempio:

- Nessuno può non conoscere tutti gli individui specifici nelle directory di altre organizzazioni per poterli invitare
- Anche se sono stati in grado di invitare questi utenti, nessuno in tale organizzazione potrebbe ricordare di gestire tutti l'accesso dell'utente in modo coerente

La gestione dei diritti di Azure AD consente di affrontare queste sfide.  Per altre informazioni su come i clienti hanno usato la gestione dei diritti di Azure AD, è possibile leggere il [case study di Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) e il [case study Centrica.](https://customers.microsoft.com/story/757467-centrica-energy-azure)  Questo video offre una panoramica della gestione dei diritti e del relativo valore:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Cosa posso fare con la gestione dei diritti?

Ecco alcune delle funzionalità di gestione dei diritti:

- Delegare agli utenti non amministratori la possibilità di creare pacchetti di accesso. Questi pacchetti di accesso contengono risorse che gli utenti possono richiedere e i gestori di pacchetti di accesso delegati possono definire criteri con regole per le quali gli utenti possono richiedere, che devono approvare l'accesso e quando l'accesso scade.
- Selezionare le organizzazioni connesse i cui utenti possono richiedere l'accesso.  Quando un utente che non è ancora presente nella directory richiede l'accesso e viene approvato, viene automaticamente invitato alla directory e l'accesso assegnato.  Quando il loro accesso scade, se non hanno altre assegnazioni del pacchetto di accesso, il loro account B2B nella directory può essere rimosso automaticamente.

Puoi iniziare con il nostro [tutorial per creare il tuo primo pacchetto](entitlement-management-access-package-first.md)di accesso . È inoltre possibile leggere gli [scenari comuni](entitlement-management-scenarios.md)o guardare video,

- [Come distribuire la gestione dei diritti di Azure AD nell'organizzazioneHow to deploy Azure AD entitlement management in your organization](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Come monitorare e ridimensionare l'uso della gestione dei diritti di Azure ADHow to monitor and scale your use of Azure AD entitlement management](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Come delegare nella gestione dei diritti](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Che cosa sono i pacchetti di accesso e quali risorse posso gestire con loro?

La gestione dei diritti introduce in Azure AD il concetto di pacchetto di *accesso.* Un pacchetto di accesso è un pacchetto di tutte le risorse con l'accesso di cui un utente ha bisogno per lavorare su un progetto o eseguire l'attività. I pacchetti di accesso vengono usati per controllare l'accesso per i dipendenti interni e anche per gli utenti esterni all'organizzazione.

 Di seguito sono riportati i tipi di risorse a cui è possibile gestire l'accesso degli utenti con la gestione dei diritti:

- Appartenenza ai gruppi di sicurezza di Azure ADMembership of Azure AD security groups
- Appartenenza a gruppi e team di Office 365
- Assegnazione alle applicazioni aziendali di Azure AD, incluse le applicazioni SaaS e le applicazioni personalizzate che supportano la federazione/single sign-on e/o il provisioning
- Appartenenza ai siti di SharePoint Online

È anche possibile controllare l'accesso ad altre risorse che si basano su gruppi di sicurezza di Azure AD o gruppi di Office 365.You can also control access to other resources that rely on Azure AD security groups or Office 365 Groups.  Ad esempio:

- È possibile concedere licenze agli utenti per Microsoft Office 365 usando un gruppo di sicurezza di Azure AD in un pacchetto di accesso e configurando le licenze basate su gruppo per tale gruppoYou can give users licenses for Microsoft Office 365 by using an Azure AD security group in an access package and configuring [group-based licensing](../users-groups-roles/licensing-groups-assign.md) for that group
- È possibile concedere agli utenti l'accesso per gestire le risorse di Azure usando un gruppo di sicurezza di Azure AD in un pacchetto di accesso e creando [un'assegnazione](../../role-based-access-control/role-assignments-portal.md) di ruolo di Azure per tale gruppoYou can give users access to manage Azure resources by using an Azure AD security group in an access package and creating an Azure role assignment for that group

## <a name="how-do-i-control-who-gets-access"></a>Come posso controllare chi ottiene l'accesso?

Con un pacchetto di accesso, un amministratore o un gestore di pacchetti di accesso delegato elenca le risorse (gruppi, app e siti) e i ruoli necessari agli utenti per tali risorse.

I pacchetti di accesso includono anche uno o più *criteri.* Un criterio definisce le regole o i guardrail per l'assegnazione al pacchetto di accesso. Ogni criterio può essere utilizzato per garantire che solo gli utenti appropriati siano in grado di richiedere l'accesso, che vi siano responsabili approvazione per la loro richiesta e che il loro accesso a tali risorse sia limitato nel tempo e scada se non rinnovato.

![Pacchetto e criteri di accesso](./media/entitlement-management-overview/elm-overview-access-package.png)

All'interno di ogni criterio, un amministratore o un gestore di pacchetti di accesso definisce

- Gli utenti già esistenti (in genere dipendenti o guest già invitati) o le organizzazioni partner di utenti esterni, idonei a richiedere l'accesso
- Il processo di approvazione e gli utenti che possono approvare o negare l'accesso
- La durata dell'assegnazione di accesso di un utente, una volta approvata, prima della scadenza dell'assegnazione

Il diagramma seguente mostra un esempio dei diversi elementi nella gestione dei diritti. Mostra un catalogo con due pacchetti di accesso di esempio.

- **Il pacchetto di accesso 1** include un singolo gruppo come risorsa. L'accesso viene definito con un criterio che consente a un insieme di utenti nella directory di richiedere l'accesso.
- **Il pacchetto di access 2** include un gruppo, un'applicazione e un sito di SharePoint Online come risorse. L'accesso viene definito con due criteri diversi. Il primo criterio consente a un set di utenti nella directory di richiedere l'accesso. Il secondo criterio consente agli utenti in una directory esterna di richiedere l'accesso.

![Panoramica della gestione dei diritti](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Quando devo usare i pacchetti di accesso?

I pacchetti di access non sostituiscono altri meccanismi per l'assegnazione dell'accesso.  Sono più appropriati in situazioni come le seguenti:

- I dipendenti hanno bisogno di un accesso limitato nel tempo per una particolare attività.  Ad esempio, è possibile utilizzare licenze basate su gruppo e un gruppo dinamico per garantire che tutti i dipendenti dispongano di una cassetta postale di Exchange Online e quindi utilizzare i pacchetti di accesso per le situazioni in cui i dipendenti necessitano di ulteriore accesso, ad esempio per leggere le risorse dipartimentali da un altro reparto.
- L'accesso deve essere approvato dal responsabile di un dipendente o da altre persone designate.
- I reparti desiderano gestire i propri criteri di accesso per le proprie risorse senza il coinvolgimento dell'IT.  
- Due o più organizzazioni collaborano a un progetto e, di conseguenza, più utenti di un'organizzazione dovranno essere introdotti tramite Azure AD B2B per accedere alle risorse di un'altra organizzazione.

## <a name="how-do-i-delegate-access"></a>Come si delega l'accesso?

 I pacchetti di accesso sono definiti in contenitori denominati *cataloghi*.  È possibile avere un unico catalogo per tutti i pacchetti di accesso oppure è possibile designare individui per creare e possedere i propri cataloghi. Un amministratore può aggiungere risorse a qualsiasi catalogo, ma un non amministratore può aggiungere solo a un catalogo le risorse di cui è proprietario. Il proprietario di un catalogo può aggiungere altri utenti come comproprietari del catalogo o come gestori di pacchetti di accesso.  Questi scenari sono descritti più avanti nell'articolo delega e ruoli nella gestione dei diritti di [Azure AD.](entitlement-management-delegate.md)

## <a name="summary-of-terminology"></a>Riepilogo della terminologia

Per comprendere meglio la gestione dei diritti e la relativa documentazione, è possibile tornare al seguente elenco di termini.

| Termine | Descrizione |
| --- | --- |
| pacchetto di accesso | Un insieme di risorse di cui un team o un progetto ha bisogno ed è regolato da criteri. Un pacchetto di accesso è sempre contenuto in un catalogo. È necessario creare un nuovo pacchetto di accesso per uno scenario in cui gli utenti devono richiedere l'accesso.  |
| richiesta di accesso | Richiesta di accesso alle risorse in un pacchetto di accesso. Una richiesta viene in genere sottoposta a un flusso di lavoro di approvazione.  Se approvato, l'utente richiedente riceve un'assegnazione del pacchetto di accesso. |
| assegnazione | L'assegnazione di un pacchetto di accesso a un utente garantisce che l'utente disponga di tutti i ruoli delle risorse di tale pacchetto di accesso.  Le assegnazioni dei pacchetti di Access hanno in genere un limite di tempo prima della scadenza. |
| catalogo | Contenitore di risorse correlate e pacchetti di accesso.  I cataloghi vengono utilizzati per la delega, in modo che gli utenti non amministratori possano creare i propri pacchetti di accesso. I proprietari dei cataloghi possono aggiungere le risorse di cui sono proprietari a un catalogo. |
| creatore del catalogo | Raccolta di utenti autorizzati a creare nuovi cataloghi.  Quando un utente non amministratore autorizzato a essere un creatore del catalogo crea un nuovo catalogo, diventa automaticamente il proprietario di tale catalogo. |
| organizzazione connessa | Una directory o un dominio di Azure AD esterno con cui si ha una relazione. Gli utenti di un'organizzazione connessa possono essere specificati in un criterio come autorizzato a richiedere l'accesso. |
| policy | Set di regole che definisce il ciclo di vita dell'accesso, ad esempio il modo in cui gli utenti ottengono l'accesso, chi può approvare e per quanto tempo gli utenti hanno accesso tramite un'assegnazione. Un criterio è collegato a un pacchetto di accesso. Ad esempio, un pacchetto di accesso potrebbe avere due criteri: uno per i dipendenti richiedere l'accesso e un secondo per gli utenti esterni richiedere l'accesso. |
| resource | Risorsa, ad esempio un gruppo di Office, un gruppo di sicurezza, un'applicazione o un sito di SharePoint Online, con un ruolo a cui a un utente possono essere concesse le autorizzazioni. |
| directory delle risorse | Directory con una o più risorse da condividere. |
| ruolo delle risorse | Raccolta di autorizzazioni associate e definite da una risorsa. Un gruppo ha due ruoli: membro e proprietario. I siti di SharePoint hanno in genere 3 ruoli, ma possono avere ruoli personalizzati aggiuntivi. Le applicazioni possono avere ruoli personalizzati. |


## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

I cloud specializzati, ad esempio Azure Germania e Azure China 21Vianet, non sono attualmente disponibili per l'uso.

### <a name="how-many-licenses-must-you-have"></a>Quante licenze è necessario disporre?

Verificare che la directory disponga di almeno le licenze di Azure AD Premium P2 di cui si dispone:Ensure that your directory has at least as many Azure AD Premium P2 licenses as you have:

- Utenti membri che **possono** richiedere un pacchetto di accesso.
- Utenti membri e guest che richiedono un pacchetto di accesso.
- Utenti membri e guest che approvano le richieste per un pacchetto di accesso.
- Utenti membri e guest che dispongono di un'assegnazione diretta a un pacchetto di accesso.

Le licenze di Azure AD Premium P2 non sono necessarie per le attività seguenti:Azure AD Premium P2 licenses are **not** required for the following tasks:

- Non sono necessarie licenze per gli utenti con il ruolo di amministratore globale che configurano i cataloghi iniziali, i pacchetti di accesso e i criteri e delegano le attività amministrative ad altri utenti.
- Non sono necessarie licenze per gli utenti a cui sono state delegate attività amministrative, ad esempio creatore del catalogo, proprietario del catalogo e gestore di pacchetti di accesso.
- Non sono necessarie licenze per gli ospiti che **possono** richiedere pacchetti di accesso, ma **non** richiedono un pacchetto di accesso.

Per ogni licenza di Azure AD Premium P2 a pagamento acquistata per gli utenti membri (dipendenti), è possibile usare Azure AD B2B per invitare fino a 5 utenti guest. Questi utenti guest possono anche usare le funzionalità di Azure AD Premium P2. Per altre informazioni, vedere Indicazioni sulle licenze per la [collaborazione B2B di Azure AD.For more information, see Azure AD B2B collaboration licensing guidance](../b2b/licensing-guidance.md).

Per altre informazioni sulle licenze, vedere [Assegnare o rimuovere licenze tramite il portale](../fundamentals/license-users-groups.md)di Azure Active Directory.For more information about licenses, see Assign or remove licenses using the Azure Active Directory portal.

### <a name="example-license-scenarios"></a>Scenari di licenza di esempio

Di seguito sono riportati alcuni scenari di licenza di esempio che consentono di determinare il numero di licenze necessarie.

| Scenario | Calcolo | Numero di licenze |
| --- | --- | --- |
| Un amministratore globale presso Woodgrove Bank crea cataloghi iniziali e delega le attività amministrative ad altri 6 utenti. Uno dei criteri specifica che **tutti i dipendenti** (2.000 dipendenti) possono richiedere un set specifico di pacchetti di accesso. 150 dipendenti richiedono i pacchetti di accesso. | 2.000 dipendenti che **possono** richiedere i pacchetti di accesso | 2.000 |
| Un amministratore globale presso Woodgrove Bank crea cataloghi iniziali e delega le attività amministrative ad altri 6 utenti. Uno dei criteri specifica che **tutti i dipendenti** (2.000 dipendenti) possono richiedere un set specifico di pacchetti di accesso. Un altro criterio specifica che alcuni utenti **di Users del partner Contoso** (ospiti) possono richiedere gli stessi pacchetti di accesso soggetti ad approvazione. Contoso dispone di 30.000 utenti. 150 dipendenti richiedono i pacchetti di accesso e 10.500 utenti da Contoso richiedono l'accesso. | 2.000 dipendenti , 500 utenti guest di Contoso che superano il rapporto 1:5 (10.500 - 2.000 x 5)) | 2.500 |

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare il primo pacchetto di accessoTutorial: Create your first access package](entitlement-management-access-package-first.md)
- [Scenari comuni](entitlement-management-scenarios.md)
