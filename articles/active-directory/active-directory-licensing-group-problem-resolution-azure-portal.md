---

title: Risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory | Documentazione Microsoft
description: Come identificare e risolvere problemi di assegnazione delle licenze con le licenze basate sui gruppi in Azure Active Directory
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 5d1c24f0423feffcbde0387ebf3f23a32fda50ce
ms.lasthandoff: 03/08/2017


---

# <a name="identifying-and-resolving-license-assignment-problems-when-using-groups-in-azure-active-directory"></a>Identificazione e risoluzione dei problemi relativi all'assegnazione di licenze quando si usano i gruppi in Azure Active Directory


Le licenze basate sui gruppi in Azure Active Directory (Azure AD) introducono il concetto di utenti in stato di errore di licenza. Questo articolo illustra il motivo per cui gli utenti possono trovarsi in questo stato. Quando le licenze vengono assegnate direttamente a utenti singoli, senza l'uso di licenze basate sui gruppi, l'operazione di assegnazione può avere esito negativo. Ad esempio, quando l'amministratore esegue il cmdlet `Set-MsolUserLicense` di PowerShell su un utente, il cmdlet potrebbe non riuscire per diversi motivi correlati alla logica di business, come un numero insufficiente di licenze o un conflitto tra due piani di servizio che non è possibile assegnare contemporaneamente. Il problema viene segnalato immediatamente all'utente che esegue il comando.

Quando si usano licenze basate sui gruppi, possono verificarsi gli stessi errori, ma questo avviene in background durante l'assegnazione delle licenze da parte del servizio Azure AD. Per questo motivo non è possibile comunicare immediatamente le licenze all'amministratore. Vengono invece registrate nell'oggetto utente e segnalate tramite il portale amministrativo. L'intento originale di assegnare una licenza all'utente resta, ma la licenza viene registrata in stato di errore per l'analisi e la risoluzione in un secondo momento.

Per trovare gli utenti in stato di errore per ogni gruppo, aprire il pannello del gruppo specifico. In caso di utenti in stato di errore, verrà visualizzata una notifica in **Licenze**. Selezionare la notifica per aprire un elenco di tutti gli utenti interessati, visualizzabili singolarmente allo scopo di comprendere il problema sottostante. Questo articolo illustra tutti i potenziali problemi e come risolverli.

## <a name="not-enough-licenses"></a>Le licenze non sono sufficienti

Le licenze disponibili per uno dei prodotti specificati nel gruppo non sono sufficienti. È necessario acquistare altre licenze per il prodotto o liberare le licenze inutilizzate da altri utenti o gruppi.

Per vedere il numero di licenze disponibili, passare a **Azure Active Directory &gt; Licenze &gt; Tutti i prodotti**.

Per vedere quali utenti e gruppi utilizzano le licenze, fare clic su un prodotto. In **Utenti con licenza** vengono visualizzati tutti gli utenti a cui sono state assegnate licenze direttamente o tramite uno o più gruppi. In **Gruppi con licenza** vengono visualizzati tutti i gruppi a cui è assegnato tale prodotto.

## <a name="conflicting-service-plans"></a>Piani di servizio in conflitto

Uno dei prodotti specificati nel gruppo contiene un piano di servizio in conflitto con un altro piano di servizio già assegnato all'utente tramite un prodotto diverso. Alcuni piani di servizio vengono configurati in modo che non sia possibile assegnarli allo stesso utente come un altro piano di servizio correlato.

Si consideri l'esempio seguente: a un utente viene assegnata direttamente una licenza per Office 365 Enterprise **E1**, con tutti i piani abilitati. L'utente è stato aggiunto a un gruppo a cui è assegnato il prodotto Office 365 Enterprise **E3**. Questo prodotto contiene piani di servizio che non possono sovrapporsi con i piani inclusi in E1, quindi l'assegnazione della licenza di gruppo non riuscirà, generando il messaggio di errore "Piani di servizio in conflitto". In questo esempio, i piani di servizio in conflitto sono:

-   SharePoint Online (piano 2) in conflitto con SharePoint Online (piano 1)

-   Exchange Online (piano 2) in conflitto con Exchange Online (piano 1)

Per risolvere questo conflitto, è necessario disabilitare i due piani nella licenza E1 assegnata direttamente all'utente o modificare l'intera assegnazione della licenza di gruppo e disabilitare i piani nella licenza E3. In alternativa, è possibile decidere di rimuovere la licenza E1 dall'utente, se è ridondante nel contesto della licenza E3.

La decisione sulla risoluzione dei problemi relativi a licenze dei prodotti in conflitto spetta sempre all'amministratore. Azure AD non risolve automaticamente i conflitti di licenza.

## <a name="other-products-depend-on-this-license"></a>Altri prodotti dipendono da questa licenza

Uno dei prodotti specificati nel gruppo contiene un piano di servizio che per funzionare deve essere abilitato per un altro piano di servizio, in un altro prodotto. Questo errore si verifica quando Azure AD prova a rimuovere il piano di servizio sottostante, ad esempio in seguito alla rimozione dell'utente dal gruppo.

Per risolvere questo problema, è necessario assicurarsi che il piano richiesto sia ancora assegnato agli utenti tramite un altro metodo oppure che i servizi dipendenti siano disabilitati per tali utenti. Solo così sarà possibile rimuovere la licenza di gruppo da tali utenti.

## <a name="usage-location-not-allowed"></a>La località di utilizzo non è consentita

Alcuni servizi Microsoft non sono disponibili in tutte le località a causa di leggi e regolamenti locali. Per poter assegnare una licenza a un utente, l'amministratore deve prima specificare la proprietà "Località di utilizzo" per l'utente. A tale scopo è possibile usare la sezione **Utente &gt; Profilo &gt; Impostazioni** nel portale di Azure.

Quando Azure AD tenta di assegnare una licenza di gruppo a un utente la cui località di utilizzo non è supportata, l'operazione non riesce e per l'utente viene registrato un errore.

Per risolvere questo problema, rimuovere gli utenti di località non supportate dal gruppo con licenza. In alternativa, se i valori correnti relativi alla località di utilizzo non rappresentano la località effettiva degli utenti, è possibile modificarli in modo che la volta successiva le licenze vengano assegnate correttamente (se la nuova località è supportata).

> [!NOTE]
> Quando Azure AD assegna licenze di gruppo, gli utenti per cui non è specificata una località di utilizzo erediteranno la località della directory. Si consiglia agli amministratori di impostare valori corretti per la località di utilizzo degli utenti prima di usare licenze basate sul gruppo, in modo da rispettare le normative e le leggi locali. 

## <a name="what-happens-when-there-is-more-than-1-product-license-on-a-group"></a>Cosa accade quando un gruppo include più di una licenza del prodotto

È possibile assegnare più di una licenza del prodotto a un gruppo. Ad esempio, è possibile assegnare Office 365 Enterprise E3 ed Enterprise Mobility + Security a un gruppo per abilitare facilmente tutti i servizi inclusi per gli utenti.

Azure AD tenterà di assegnare a ogni utente tutte le licenze specificate nel gruppo. Se non è possibile assegnare uno dei prodotti a causa di problemi di logica di business, ad esempio in caso di licenze insufficienti tutte in conflitto con altri servizi abilitati per l'utente, non vengono assegnate neanche le altre licenze nel gruppo.

È possibile visualizzare gli utenti per i quali l'assegnazione non è riuscita e i relativi prodotti interessati.

## <a name="how-to-force-processing-of-licenses-in-a-group-to-resolve-errors"></a>Come forzare l'elaborazione delle licenze in un gruppo per risolvere gli errori

A seconda delle azioni intraprese per risolvere gli errori, potrebbe essere necessario attivare manualmente l'elaborazione di un gruppo per aggiornare lo stato dell'utente.

Ad esempio, se sono state liberate alcune licenze rimuovendo le assegnazioni di licenza dirette dagli utenti, sarà necessario attivare l'elaborazione dei gruppi che in precedenza hanno dato esito negativo per poter assegnare licenze complete a tutti i membri utente. A tale scopo, nel pannello del gruppo aprire **Licenze** e fare clic sul pulsante **Rielabora** nella barra degli strumenti.


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari per la gestione delle licenze tramite i gruppi, vedere:

* [Assegnazione di licenze a un gruppo in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-advanced.md)

