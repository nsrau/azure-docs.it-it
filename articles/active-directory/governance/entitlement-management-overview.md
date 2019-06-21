---
title: Che cos'è la gestione dei diritti di Azure AD? (Anteprima) - Azure Active Directory
description: Panoramica di gestione dei diritti di Azure Active Directory e come è possibile usarlo per gestire l'accesso a gruppi, applicazioni e siti di SharePoint Online per gli utenti interni ed esterni.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/05/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb4001e9496d31d9c2879721f8cf8e26b74ddf3
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204542"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Che cos'è la gestione dei diritti di Azure AD? (Anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I dipendenti di organizzazioni è necessario accedere a diversi gruppi, applicazioni e siti per svolgere le proprie. Gestire l'accesso è complessa. Nella maggior parte dei casi, non vi è alcun elenco organizzata di tutte le risorse di un utente deve avere per un progetto. Il project manager ha una buona conoscenza delle risorse necessari, le persone coinvolte e per quanto tempo che può durare il progetto. Tuttavia, il project manager in genere non disporre delle autorizzazioni per approvare o concedere l'accesso ad altri utenti. Questo scenario diventa più complicato quando si prova a lavorare con persone esterne o aziende.

Gestione dei diritti di Azure Active Directory (Azure AD) può aiutarti a gestire l'accesso ai gruppi, applicazioni e siti di SharePoint Online per gli utenti interni e anche gli utenti esterni all'organizzazione.

## <a name="why-use-entitlement-management"></a>Perché usare Gestione entitlement?

Organizzazioni aziendali affrontano spesso difficoltà quando gestiscono l'accesso alle risorse, ad esempio:

- Gli utenti potrebbero non sapere quale devono disporre di accesso
- Gli utenti riscontrino difficoltà di individuazione di individui appropriati o delle risorse adeguate
- Dopo che gli utenti individuano e ricevano l'accesso a una risorsa, potrebbe contenere più a lungo per gli scopi aziendali è necessario accedere a

Questi problemi si accumulano per gli utenti che richiedono l'accesso da un'altra directory, ad esempio gli utenti esterni che provengono da organizzazioni di supply chain o altri partner di business. Ad esempio:

- Le organizzazioni potrebbero non sapere tutto dei singoli individui specifici in altre directory sia in grado di inviare un invito
- Anche se le organizzazioni erano in grado di invitare tali utenti, le organizzazioni non venga memorizzato gestire in modo coerente tutte di accesso dell'utente

Gestione dei diritti di Azure AD consente di risolvere queste problematiche.

## <a name="what-can-i-do-with-entitlement-management"></a>Che cosa può fare con la gestione dei diritti?

Ecco alcune delle funzionalità di gestione dei diritti:

- Creazione di pacchetti di risorse correlate in cui gli utenti possono richiedere
- Definire le regole per informazioni su come richiedere le risorse e in cui scade l'accesso
- Gestire il ciclo di vita di accesso per gli utenti interni ed esterni
- Delegare la gestione delle risorse
- Designare gli approvatori per approvare le richieste
- Creazione di report per tenere traccia della cronologia

Per una panoramica di governance delle identità e gestione dei diritti, guardare il video seguente dalla conferenza Ignite 2018:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Le risorse a cui è possibile gestire?

Ecco i tipi di risorse che è possibile gestire l'accesso a con la gestione dei diritti:

- Gruppi di sicurezza di Azure AD
- Gruppi di Office 365
- Azure AD le applicazioni aziendali, tra cui applicazioni SaaS e le applicazioni integrate personalizzate che supportano la federazione o il provisioning
- Siti e le raccolte siti di SharePoint Online

È inoltre possibile controllare l'accesso ad altre risorse che si basano su gruppi di sicurezza di Azure AD o i gruppi di Office 365.  Ad esempio:

- È possibile assegnare licenze agli utenti per Microsoft Office 365 tramite un gruppo di sicurezza di Azure AD in un pacchetto di accesso e configurando [licenze basate su gruppo](../users-groups-roles/licensing-groups-assign.md) per tale gruppo
- È possibile concedere agli utenti l'accesso per gestire le risorse di Azure usando un gruppo di sicurezza di Azure AD in un pacchetto di accesso e la creazione di un' [assegnazione di ruoli](../../role-based-access-control/role-assignments-portal.md) per tale gruppo

## <a name="what-are-access-packages-and-policies"></a>Quali sono i pacchetti di accesso e criteri?

Gestione Entitlement introduce il concetto di un' *pacchetto di accesso*. Un pacchetto di accesso è un bundle di tutte le risorse di che un utente deve lavorare a un progetto o svolgere le proprie. Le risorse includono l'accesso a gruppi, applicazioni o siti. I pacchetti di accesso vengono utilizzati per controllare l'accesso per i dipendenti interni e anche gli utenti esterni all'organizzazione. I pacchetti di accesso sono definiti in contenitori denominati *cataloghi*.

I pacchetti di accesso includono inoltre uno o più *criteri*. Un criterio definisce le regole o i confini di un pacchetto di accesso di accesso. L'abilitazione di un criterio impone che solo agli utenti giusti dispone dell'accesso, le risorse corrette e per la giusta quantità di tempo.

![I criteri e pacchetti di accesso](./media/entitlement-management-overview/elm-overview-access-package.png)

Con un pacchetto di accesso e i relativi criteri, la gestione di pacchetti di accesso definisce:

- Risorse
- Gli utenti i ruoli necessari per le risorse
- Gli utenti interni e gli utenti esterni che sono idonei per richiedere l'accesso
- Processo di approvazione e gli utenti che possono approvare o negare l'accesso
- Durata dell'accesso dell'utente

Il diagramma seguente illustra un esempio dei diversi elementi in Gestione dei diritti. Mostra due pacchetti di accesso di esempio.

- **Pacchetto di accesso 1** include un singolo gruppo come una risorsa. L'accesso viene definito con un criterio che consente a un set di utenti nella directory per richiedere l'accesso.
- **Pacchetto di accesso 2** include un gruppo, un'applicazione e un sito di SharePoint Online come risorse. L'accesso è definito con due criteri diversi. Il primo criterio consente a un set di utenti nella directory per richiedere l'accesso. Il secondo set di criteri consente agli utenti in una directory esterna di richiedere l'accesso.

![Panoramica della gestione dei diritti](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Utenti esterni

Quando si usa la [Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) invitare esperienza, conosci già gli indirizzi di posta elettronica degli utenti guest esterni si desidera rendere la directory delle risorse e usare. Questo approccio è ottimale quando si lavora su un progetto più piccole o a breve termine e si conoscono già tutti i partecipanti, ma si tratta più difficili da gestire se sono presenti molti utenti che si desidera usare oppure se i membri cambiano nel tempo.  Ad esempio, è possibile lavorare con un'altra organizzazione e disporre di un punto di contatto con l'organizzazione, ma nel corso del tempo altri utenti di tale organizzazione saranno inoltre necessario accedere.

Con la gestione dei diritti, è possibile definire un criterio che consente agli utenti di organizzazioni si specifica, che sono anche usando Azure AD, per poter richiedere un pacchetto di accesso. È possibile specificare se è necessaria l'approvazione e una data di scadenza per l'accesso. Se è necessaria l'approvazione, è anche possibile designare come responsabile approvazione uno o più utenti dell'organizzazione esterna si precedentemente invitati - poiché è probabile che si sa quali utenti esterni all'organizzazione devono accedere. Dopo aver configurato il pacchetto di accesso, è possibile inviare un collegamento al pacchetto di accesso del contatto presso l'organizzazione esterna. Tale contatto può condividere con altri utenti dell'organizzazione esterna e si può usare questo collegamento per richiedere il pacchetto di accesso.  Gli utenti che sono già stati invitati alla directory dell'organizzazione possono anche utilizzare il collegamento.

Quando una richiesta viene approvata, gestione dei diritti dei effettuerà il provisioning utente con l'accesso necessario, che può includere l'invito all'utente se non sono già nella directory. Azure AD creerà automaticamente un account B2B per loro.  Si noti che un amministratore può avere precedentemente limitata che consente alle organizzazioni sono consentite per la collaborazione, impostando un [B2B consente o Nega elenco](../b2b/allow-deny-list.md) per consentire o bloccare invita ad altre organizzazioni.  Se l'utente non è consentito da elenco elementi consentiti o bloccati, quindi sono non essere invitati.

Poiché si desidera l'accesso dell'utente esterno duri all'infinito, specificare una data di scadenza nel criterio, ad esempio 180 giorni. Dopo 180 giorni, se l'accesso non viene rinnovata, la gestione dei diritti rimuoverà tutti gli accessi associati a tale pacchetto di accesso.  Se l'utente che è stato invitato mediante la gestione dei diritti non dispone di alcun altra assegnazioni di accesso pacchetto, quindi quando si perdono l'ultima assegnazione, il proprio account B2B verrà impedito di accesso per 30 giorni e successivamente rimossa.  Questo impedisce la proliferazione di account non necessari.  

## <a name="terminology"></a>Terminologia

Per comprendere meglio la gestione dei diritti e la relativa documentazione, è necessario esaminare i termini seguenti.

| Termine o concetto | Descrizione |
| --- | --- |
| gestione dei diritti | Un servizio che viene assegnato, revoca e Amministra i pacchetti di accesso. |
| pacchetto di accesso | Raccolta di autorizzazioni e i criteri per le risorse che gli utenti possono richiedere. Un pacchetto di accesso è sempre contenuto in un catalogo. |
| richiesta di accesso | Richiesta di accedere a un pacchetto di accesso. Una richiesta in genere passa attraverso un flusso di lavoro. |
| criterio | Un set di regole che definisce il ciclo di vita di accesso, ad esempio di come ottengono l'accesso agli utenti autorizzati ad approvare e quanti utenti hanno accesso. Criteri di esempio includono accesso dei dipendenti e l'accesso esterno. |
| catalog | Un contenitore di risorse correlate e i pacchetti di accesso. |
| Catalogo generale | Catalogo predefinito che è sempre disponibile. Per aggiungere risorse al catalogo generale, richiede determinate autorizzazioni. |
| resource | Un asset o un servizio (ad esempio, un gruppo, applicazione o sito) che un utente può essere concesse le autorizzazioni per. |
| Tipo di risorsa | Il tipo di risorsa, che include i gruppi, applicazioni e siti di SharePoint Online. |
| ruolo per le risorse | Raccolta di autorizzazioni associate a una risorsa. |
| directory delle risorse | Una directory che ha uno o più risorse per condividerle. |
| utenti assegnati | Un'assegnazione di un pacchetto di accesso per un utente o gruppo. |
| enable | Il processo di rendere disponibili agli utenti di richiedere un pacchetto di accesso. |

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Cloud specializzato, ad esempio Azure per enti pubblici, Azure Germania e Azure Cina 21Vianet, non sono attualmente disponibili per l'uso in questa versione di anteprima.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare il pacchetto prima dell'accesso](entitlement-management-access-package-first.md)
- [Scenari comuni](entitlement-management-scenarios.md)
