---
title: Cos'è Gestione entitlement di Azure AD? -Azure Active Directory
description: Panoramica della gestione dei diritti Azure Active Directory e del modo in cui è possibile utilizzarla per gestire l'accesso a gruppi, applicazioni e siti di SharePoint Online per utenti interni ed esterni.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd72bba6e8c01644adebaa333c8fe588c35b8c7
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927307"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Cos'è Gestione entitlement di Azure AD?

La gestione dei diritti di Azure Active Directory (Azure AD) è una funzionalità di [governance delle identità](identity-governance-overview.md) che consente alle organizzazioni di gestire il ciclo di vita delle identità e dell'accesso su larga scala, automatizzando i flussi di lavoro delle richieste di accesso, le assegnazioni di accesso, le verifiche

Per svolgere il proprio lavoro, i dipendenti delle organizzazioni devono accedere a diversi gruppi, applicazioni e siti. La gestione di questo accesso è problematica, in quanto i requisiti cambiano: vengono aggiunte nuove applicazioni o gli utenti necessitano di diritti di accesso aggiuntivi.  Questo scenario diventa più complicato quando si collabora con organizzazioni esterne. è possibile che non si conosca chi nell'altra organizzazione debba accedere alle risorse dell'organizzazione e che non conoscano le applicazioni, i gruppi o i siti utilizzati dall'organizzazione.

Azure AD la gestione dei diritti può aiutarti a gestire in modo più efficiente l'accesso a gruppi, applicazioni e siti di SharePoint Online per utenti interni e anche a utenti esterni all'organizzazione che necessitano dell'accesso a tali risorse.

## <a name="why-use-entitlement-management"></a>Perché usare la gestione dei diritti?

Le organizzazioni aziendali spesso affrontano problemi durante la gestione dell'accesso dei dipendenti alle risorse, ad esempio:

- È possibile che gli utenti non conoscano l'accesso che devono avere e, anche in caso affermativo, potrebbero avere difficoltà nell'individuare gli individui giusti per approvare l'accesso
- Una volta che gli utenti trovano e ricevono l'accesso a una risorsa, possono mantenere l'accesso più a lungo di quanto richiesto per scopi aziendali

Questi problemi sono composti per gli utenti che necessitano dell'accesso da un'altra organizzazione, ad esempio gli utenti esterni che appartengono a organizzazioni di supply chain o altri partner commerciali. Ad esempio:

- Non è possibile che una persona conosca tutti gli utenti specifici nelle directory di altre organizzazioni per poterli invitare
- Anche se sono stati in grado di invitare questi utenti, nessuno in tale organizzazione potrebbe ricordare di gestire in modo coerente tutti gli accessi degli utenti

Azure AD la gestione dei diritti può aiutare a risolvere questi problemi.  Per ulteriori informazioni sul modo in cui i clienti hanno utilizzato Azure AD la gestione dei diritti, è possibile leggere il [Case Study Avanade](https://aka.ms/AvanadeELMCase) e il [Case Study incentrato](https://aka.ms/CentricaELMCase).  Questo video offre una panoramica della gestione dei diritti e del relativo valore:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Cosa posso fare con la gestione dei diritti?

Di seguito sono riportate alcune delle funzionalità di gestione dei diritti:

- Delegare agli non amministratori la possibilità di creare pacchetti di accesso. Questi pacchetti di accesso contengono risorse che gli utenti possono richiedere e i gestori di pacchetti di accesso delegato possono definire criteri con regole per le quali gli utenti possono richiedere, che devono approvare l'accesso e quando scade l'accesso.
- Selezionare le organizzazioni connesse i cui utenti possono richiedere l'accesso.  Quando un utente che non è ancora presente nella directory richiede l'accesso e viene approvato, viene automaticamente invitato alla directory e viene assegnato l'accesso.  Al termine dell'accesso, se non hanno altre assegnazioni di pacchetti di accesso, è possibile rimuovere automaticamente il relativo account B2B nella directory.

[Per creare il primo pacchetto di accesso](entitlement-management-access-package-first.md), è possibile iniziare con l'esercitazione. È anche possibile leggere gli [scenari comuni](entitlement-management-scenarios.md)o guardare i video, tra cui

- [Come distribuire Azure AD gestione dei diritti nell'organizzazione](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Come monitorare e ridimensionare l'uso della gestione dei diritti Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Come delegare la gestione dei diritti](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Che cosa sono i pacchetti di accesso e quali risorse è possibile gestire con loro?

La gestione dei diritti introduce la Azure AD il concetto di *pacchetto di accesso*. Un pacchetto di accesso è costituito da un bundle di tutte le risorse con l'accesso che un utente deve usare per un progetto o per eseguire le attività. I pacchetti Access vengono usati per gestire l'accesso per i dipendenti interni e anche per gli utenti esterni all'organizzazione.

 Di seguito sono riportati i tipi di risorse a cui è possibile gestire l'accesso dell'utente con la gestione dei diritti:

- Appartenenza ai gruppi di sicurezza Azure AD
- Appartenenza ai gruppi e ai team di Office 365
- Assegnazione a Azure AD applicazioni aziendali, incluse applicazioni SaaS e applicazioni personalizzate che supportano la Federazione/Single Sign-on e/o il provisioning
- Appartenenza ai siti di SharePoint Online

È anche possibile controllare l'accesso ad altre risorse che si basano su Azure AD gruppi di sicurezza o gruppi di Office 365.  Ad esempio:

- È possibile concedere agli utenti le licenze per Microsoft Office 365 usando un gruppo di sicurezza Azure AD in un pacchetto di accesso e configurando le [licenze basate sui gruppi](../users-groups-roles/licensing-groups-assign.md) per quel gruppo
- È possibile concedere agli utenti l'accesso per gestire le risorse di Azure usando un gruppo di sicurezza Azure AD in un pacchetto di accesso e creando un' [assegnazione di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per quel gruppo

## <a name="how-do-i-control-who-gets-access"></a>Ricerca per categorie controllare chi ottiene l'accesso?

Con un pacchetto di accesso, un amministratore o un gestore di pacchetti di accesso delegato elenca le risorse (gruppi, app e siti) e i ruoli necessari agli utenti per tali risorse.

I pacchetti Access includono inoltre uno o più *criteri*. Un criterio definisce le regole o Guardrails per l'assegnazione al pacchetto di accesso. Ogni criterio può essere usato per garantire che solo gli utenti appropriati siano in grado di richiedere l'accesso, che ci siano responsabili approvazione per la richiesta e che l'accesso a tali risorse sia limitato dal tempo e scadrà se non rinnovato.

![Accedere a pacchetti e criteri](./media/entitlement-management-overview/elm-overview-access-package.png)

All'interno di ogni criterio, un amministratore o un gestore di pacchetti di Access definisce

- Utenti già esistenti (in genere dipendenti o Guest già invitati) o organizzazioni partner di utenti esterni, idonei per richiedere l'accesso
- Processo di approvazione e utenti che possono approvare o negare l'accesso
- Durata dell'assegnazione di accesso di un utente, una volta approvata, prima della scadenza dell'assegnazione

Il diagramma seguente mostra un esempio dei diversi elementi della gestione dei diritti. Mostra un catalogo con due pacchetti di accesso di esempio.

- Il **pacchetto di accesso 1** include un singolo gruppo come risorsa. L'accesso viene definito con un criterio che consente a un set di utenti nella directory di richiedere l'accesso.
- Il **pacchetto di accesso 2** include un gruppo, un'applicazione e un sito di SharePoint Online come risorse. L'accesso viene definito con due criteri diversi. Il primo criterio consente a un set di utenti nella directory di richiedere l'accesso. Il secondo criterio consente agli utenti di una directory esterna di richiedere l'accesso.

![Panoramica sulla gestione dei diritti](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Quando è consigliabile usare I pacchetti di accesso?

I pacchetti di accesso non sostituiscono altri meccanismi per l'assegnazione di accesso.  Sono più appropriati in situazioni come le seguenti:

- I dipendenti necessitano di un accesso limitato al tempo per una determinata attività.  Ad esempio, è possibile usare le licenze basate sui gruppi e un gruppo dinamico per assicurarsi che tutti i dipendenti dispongano di una cassetta postale di Exchange Online, quindi usare i pacchetti di accesso per situazioni in cui i dipendenti necessitano di accesso aggiuntivo, ad esempio per leggere le risorse di reparto da un altro reparto.
- L'accesso deve essere approvato dal responsabile di un dipendente o da altri utenti designati.
- I reparti desiderano gestire i propri criteri di accesso per le risorse senza coinvolgimento IT.  
- Due o più organizzazioni collaborano a un progetto e, di conseguenza, più utenti di un'organizzazione dovranno essere portati via Azure AD B2B per accedere alle risorse di un'altra organizzazione.

## <a name="how-do-i-delegate-access"></a>Ricerca per categorie accesso delegato?

 I pacchetti di accesso sono definiti in contenitori denominati *cataloghi*.  È possibile disporre di un singolo catalogo per tutti i pacchetti di Access oppure è possibile designare singoli utenti per creare e possedere i propri cataloghi. Un amministratore può aggiungere risorse a qualsiasi catalogo, ma un amministratore non può solo aggiungere a un catalogo le risorse di cui sono proprietari. Un proprietario del catalogo può aggiungere altri utenti come comproprietari del catalogo o come gestori di pacchetti di accesso.  Questi scenari sono descritti ulteriormente nell'articolo [delega e ruoli in Azure ad gestione dei diritti](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Riepilogo della terminologia

Per comprendere meglio la gestione dei diritti e la relativa documentazione, è possibile fare riferimento all'elenco di termini seguente.

| Termine | DESCRIZIONE |
| --- | --- |
| pacchetto di accesso | Un bundle di risorse necessarie a un team o a un progetto e viene regolato con i criteri. Un pacchetto di accesso è sempre contenuto in un catalogo. È possibile creare un nuovo pacchetto di accesso per uno scenario in cui gli utenti devono richiedere l'accesso.  |
| richiesta di accesso | Richiesta di accesso alle risorse in un pacchetto di accesso. Una richiesta viene in genere attraversata da un flusso di lavoro di approvazione.  Se approvata, l'utente richiedente riceve un'assegnazione del pacchetto di accesso. |
| assegnazione | Un'assegnazione di un pacchetto di accesso a un utente garantisce che l'utente disponga di tutti i ruoli delle risorse del pacchetto di accesso.  Le assegnazioni dei pacchetti di accesso in genere hanno un limite di tempo prima della scadenza. |
| catalog | Contenitore di risorse correlate e pacchetti di accesso.  I cataloghi vengono usati per la delega, in modo che gli amministratori non possano creare i propri pacchetti di accesso. I proprietari del catalogo possono aggiungere risorse di cui sono proprietari in un catalogo. |
| creatore del catalogo | Raccolta di utenti autorizzati a creare nuovi cataloghi.  Quando un utente non amministratore autorizzato a essere un creatore del catalogo crea un nuovo catalogo, diventa automaticamente il proprietario di tale catalogo. |
| organizzazione connessa | Una directory o un dominio di Azure AD esterno con una relazione. Gli utenti di un'organizzazione connessa possono essere specificati in un criterio come consentito per richiedere l'accesso. |
| policy | Set di regole che definisce il ciclo di vita dell'accesso, ad esempio il modo in cui gli utenti ottengono l'accesso, gli utenti che possono approvare e il tempo di accesso degli utenti attraverso un'assegnazione. Un criterio è collegato a un pacchetto di accesso. Un pacchetto di accesso, ad esempio, può avere due criteri, uno per i dipendenti per richiedere l'accesso e un secondo per consentire agli utenti esterni di richiedere l'accesso. |
| resource | Un asset, ad esempio un gruppo di Office, un gruppo di sicurezza, un'applicazione o un sito di SharePoint Online, con un ruolo a cui un utente può concedere le autorizzazioni. |
| Directory delle risorse | Una directory che dispone di una o più risorse da condividere. |
| ruolo risorsa | Raccolta di autorizzazioni associate a e definite da una risorsa. Un gruppo dispone di due ruoli: membro e proprietario. I siti di SharePoint in genere hanno 3 ruoli, ma possono avere ruoli personalizzati aggiuntivi. Le applicazioni possono disporre di ruoli personalizzati. |


## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Cloud specializzati, ad esempio Azure per enti pubblici, Azure Germania e Azure Cina 21Vianet, non sono attualmente disponibili per l'uso.

### <a name="which-users-must-have-licenses"></a>Quali utenti devono avere le licenze?

Il tenant deve avere almeno il numero di licenze Azure AD Premium P2, perché gli utenti membri sono attivi nella gestione dei diritti. Gli utenti membri attivi nella gestione dei diritti includono:

- Utente che avvia o approva una richiesta di un pacchetto di accesso.
- Utente a cui è stato assegnato un pacchetto di accesso.
- Utente che gestisce i pacchetti di accesso.

Come parte delle licenze per gli utenti membri, è anche possibile consentire a diversi utenti guest di interagire con la gestione dei diritti. Per informazioni su come calcolare il numero di utenti guest che è possibile includere, vedere [Azure Active Directory linee guida sulle licenze di collaborazione B2B](../b2b/licensing-guidance.md).

Per informazioni su come assegnare le licenze agli utenti, vedere [assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md). Si noti che la gestione dei diritti non impone attualmente l'assegnazione delle licenze per gli utenti.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: creare il primo pacchetto di accesso](entitlement-management-access-package-first.md)
- [Scenari comuni](entitlement-management-scenarios.md)
