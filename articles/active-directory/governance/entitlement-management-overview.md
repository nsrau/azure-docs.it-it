---
title: Che cos'è Azure AD gestione dei diritti? (Anteprima)-Azure Active Directory
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
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12452b4e9cc6caa64d4c81a310fbccb5d1717817
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678149"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Che cos'è Azure AD gestione dei diritti? (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per svolgere il proprio lavoro, i dipendenti delle organizzazioni devono accedere a diversi gruppi, applicazioni e siti. Gestire questo accesso è complesso. Nella maggior parte dei casi, non è disponibile un elenco organizzato di tutte le risorse necessarie per un progetto. Il project manager dispone di una conoscenza approfondita delle risorse necessarie, dei singoli utenti interessati e della durata del progetto. Tuttavia, il project manager in genere non dispone delle autorizzazioni per approvare o concedere l'accesso ad altri utenti. Questo scenario diventa più complicato quando si tenta di lavorare con le società o i singoli utenti esterni.

Azure Active Directory (Azure AD) la gestione dei diritti consente di gestire l'accesso a gruppi, applicazioni e siti di SharePoint Online per gli utenti interni e anche per gli utenti esterni all'organizzazione.

## <a name="why-use-entitlement-management"></a>Perché usare la gestione dei diritti?

Le organizzazioni aziendali spesso affrontano problemi durante la gestione dell'accesso alle risorse, ad esempio:

- È possibile che gli utenti non conoscano l'accesso
- È possibile che gli utenti abbiano difficoltà nell'individuare gli individui giusti o le risorse giuste
- Una volta che gli utenti trovano e ricevono l'accesso a una risorsa, possono mantenere l'accesso più a lungo di quanto richiesto per scopi aziendali

Questi problemi sono composti per gli utenti che necessitano dell'accesso da un'altra directory, ad esempio gli utenti esterni che appartengono a organizzazioni di supply chain o altri partner commerciali. Ad esempio:

- È possibile che le organizzazioni non conoscano tutti gli utenti specifici di altre directory per poterli invitare
- Anche se le organizzazioni erano in grado di invitare questi utenti, le organizzazioni potrebbero non ricordare di gestire tutti gli accessi degli utenti in modo coerente

Azure AD la gestione dei diritti può aiutare a risolvere questi problemi.

## <a name="what-can-i-do-with-entitlement-management"></a>Cosa posso fare con la gestione dei diritti?

Di seguito sono riportate alcune delle funzionalità di gestione dei diritti:

- Creare pacchetti di risorse correlate che gli utenti possono richiedere
- Definire le regole per la richiesta di risorse e la scadenza dell'accesso
- Governare il ciclo di vita dell'accesso per gli utenti interni ed esterni
- Delegare la gestione delle risorse
- Designare i responsabili approvazione per approvare le richieste
- Creare report per tenere traccia della cronologia

Per una panoramica della governance delle identità e della gestione dei diritti, guardare il video seguente dalla conferenza Ignite 2018:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Quali risorse è possibile gestire?

Di seguito sono riportati i tipi di risorse a cui è possibile gestire l'accesso con la gestione dei diritti:

- Gruppi di sicurezza Azure AD
- Gruppi di Office 365
- Azure AD applicazioni aziendali, tra cui applicazioni SaaS e applicazioni personalizzate che supportano la Federazione o il provisioning
- Siti e raccolte di siti di SharePoint Online

È anche possibile controllare l'accesso ad altre risorse che si basano su Azure AD gruppi di sicurezza o gruppi di Office 365.  Ad esempio:

- È possibile concedere agli utenti le licenze per Microsoft Office 365 usando un gruppo di sicurezza Azure AD in un pacchetto di accesso e configurando le [licenze basate sui gruppi](../users-groups-roles/licensing-groups-assign.md) per quel gruppo
- È possibile concedere agli utenti l'accesso per gestire le risorse di Azure usando un gruppo di sicurezza Azure AD in un pacchetto di accesso e creando un' [assegnazione di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per quel gruppo

## <a name="what-are-access-packages-and-policies"></a>Che cosa sono i pacchetti e i criteri di accesso?

La gestione dei diritti introduce il concetto di *pacchetto di accesso*. Un pacchetto di accesso è costituito da un bundle di tutte le risorse necessarie a un utente per lavorare a un progetto o per eseguire il proprio lavoro. Le risorse includono l'accesso a gruppi, applicazioni o siti. I pacchetti Access vengono usati per gestire l'accesso per i dipendenti interni e anche per gli utenti esterni all'organizzazione. I pacchetti di accesso sono definiti in contenitori denominati *cataloghi*.

I pacchetti Access includono inoltre uno o più *criteri*. Un criterio definisce le regole o Guardrails per accedere a un pacchetto di accesso. L'abilitazione di un criterio impone che solo agli utenti giusti venga concesso l'accesso, alle risorse corrette e per il periodo di tempo corretto.

![Accedere a pacchetti e criteri](./media/entitlement-management-overview/elm-overview-access-package.png)

Con un pacchetto di accesso e i relativi criteri, gestione pacchetti di Access definisce:

- Risorse
- Ruoli necessari agli utenti per le risorse
- Utenti interni e organizzazioni partner di utenti esterni idonei per richiedere l'accesso
- Processo di approvazione e utenti che possono approvare o negare l'accesso
- Durata dell'accesso dell'utente

Il diagramma seguente mostra un esempio dei diversi elementi della gestione dei diritti. Mostra due pacchetti di accesso di esempio.

- Il **pacchetto di accesso 1** include un singolo gruppo come risorsa. L'accesso viene definito con un criterio che consente a un set di utenti nella directory di richiedere l'accesso.
- Il **pacchetto di accesso 2** include un gruppo, un'applicazione e un sito di SharePoint Online come risorse. L'accesso viene definito con due criteri diversi. Il primo criterio consente a un set di utenti nella directory di richiedere l'accesso. Il secondo criterio consente agli utenti di una directory esterna di richiedere l'accesso.

![Panoramica sulla gestione dei diritti](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Utenti esterni

Quando si usa l'esperienza di invito [Business-to-business (B2B) di Azure ad](../b2b/what-is-b2b.md) , è necessario conoscere già gli indirizzi di posta elettronica degli utenti Guest esterni da inserire nella directory delle risorse e usare. Si tratta di un'ottima soluzione quando si lavora su un progetto più piccolo o a breve termine e si conoscono già tutti i partecipanti, ma ciò è più difficile da gestire se si dispone di molti utenti con cui si desidera lavorare o se i partecipanti cambiano nel tempo.  È possibile, ad esempio, che si stia lavorando a un'altra organizzazione e che sia presente un punto di contatto con l'organizzazione, ma nel tempo saranno necessari anche altri utenti dell'organizzazione.

Con la gestione dei diritti, è possibile definire un criterio che consenta agli utenti delle organizzazioni specificate, che usano anche Azure AD, di poter richiedere un pacchetto di accesso. È possibile specificare se l'approvazione è obbligatoria e una data di scadenza per l'accesso. Se è richiesta l'approvazione, è anche possibile designare come responsabile approvazione uno o più utenti dell'organizzazione esterna invitati in precedenza, poiché è probabile che gli utenti esterni dell'organizzazione debbano accedere. Dopo aver configurato il pacchetto di accesso, è possibile inviare un collegamento al pacchetto di accesso al contatto dell'organizzazione esterna. Tale contatto può essere condiviso con altri utenti nell'organizzazione esterna e può utilizzare questo collegamento per richiedere il pacchetto di accesso.  Anche gli utenti dell'organizzazione che sono già stati invitati alla directory possono usare tale collegamento.

Quando una richiesta viene approvata, la gestione dei diritti effettuerà il provisioning dell'utente con l'accesso necessario, che può includere l'invito dell'utente se non è già presente nella directory. Azure AD creerà automaticamente un account B2B.  Si noti che un amministratore potrebbe avere limitato in precedenza le organizzazioni a cui è consentita la collaborazione, impostando un [elenco Consenti o nega B2B](../b2b/allow-deny-list.md) per consentire o bloccare gli inviti ad altre organizzazioni.  Se l'utente non è consentito dall'elenco Consenti o blocca, non verrà invitato.

Poiché non si desidera che l'accesso dell'utente esterno venga utilizzato per l'ultima volta, è necessario specificare una data di scadenza nel criterio, ad esempio 180 giorni. Dopo 180 giorni, se l'accesso non viene rinnovato, la gestione dei diritti eliminerà tutti gli accessi associati al pacchetto di accesso.  Se l'utente invitato attraverso la gestione dei diritti non ha altre assegnazioni di pacchetti di accesso, quando perderà l'ultima assegnazione, l'account B2B verrà bloccato dall'accesso per 30 giorni e successivamente rimosso.  In questo modo si impedisce la proliferazione di account non necessari.  

## <a name="terminology"></a>Terminologia

Per comprendere meglio la gestione dei diritti e la relativa documentazione, è necessario esaminare i termini seguenti.

| Termine o concetto | Descrizione |
| --- | --- |
| gestione dei diritti | Servizio che assegna, revoca e amministra i pacchetti di accesso. |
| pacchetto di accesso | Raccolta di autorizzazioni e criteri per le risorse che gli utenti possono richiedere. Un pacchetto di accesso è sempre contenuto in un catalogo. |
| richiesta di accesso | Richiesta di accesso a un pacchetto di accesso. Una richiesta viene in genere attraversata da un flusso di lavoro. |
| policy | Set di regole che definisce il ciclo di vita dell'accesso, ad esempio il modo in cui gli utenti ottengono l'accesso, gli utenti che possono approvare e il tempo di accesso degli utenti. I criteri di esempio includono l'accesso dei dipendenti e l'accesso esterno. |
| catalogo | Contenitore di risorse correlate e pacchetti di accesso. |
| Catalogo generale | Catalogo incorporato che è sempre disponibile. Per aggiungere risorse al catalogo generale, è necessario disporre di determinate autorizzazioni. |
| resource | Un asset o un servizio, ad esempio un gruppo di Office, un gruppo di sicurezza, un'applicazione o un sito di SharePoint Online, a cui un utente può concedere le autorizzazioni. |
| tipo di risorsa | Tipo di risorsa, che include i gruppi, le applicazioni e i siti di SharePoint Online. |
| ruolo risorsa | Raccolta di autorizzazioni associate a una risorsa. |
| Directory delle risorse | Una directory che dispone di una o più risorse da condividere. |
| utenti assegnati | Assegnazione di un pacchetto di accesso a un utente, in modo che l'utente disponga di tutti i ruoli delle risorse del pacchetto di accesso. |
| enable | Il processo di creazione di un pacchetto di accesso disponibile per gli utenti da richiedere. |

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

I cloud specializzati, ad esempio Azure per enti pubblici, Azure Germania e Azure Cina 21Vianet, non sono attualmente disponibili per l'uso in questa versione di anteprima.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare il primo pacchetto di accesso](entitlement-management-access-package-first.md)
- [Scenari comuni](entitlement-management-scenarios.md)
