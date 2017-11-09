---
title: Risolvere i problemi relativi alle licenze per un gruppo in Azure Active Directory | Microsoft Docs
description: Come identificare e risolvere problemi di assegnazione delle licenze quando si usano le licenze basate sui gruppi in Azure Active Directory
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
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa16cf14def7c6b4555d6624b25e267ef01d5adb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificare e risolvere i problemi relativi alle licenze per un gruppo in Azure Active Directory

Le licenze basate sui gruppi in Azure Active Directory (Azure AD) introducono il concetto di utenti in stato di errore di licenza. Questo articolo illustra i motivi per cui gli utenti possono trovarsi in questo stato.

Quando si assegnano le licenze direttamente a utenti singoli, senza l'uso di licenze basate sui gruppi, l'operazione di assegnazione può avere esito negativo. Ad esempio, quando si esegue il cmdlet `Set-MsolUserLicense` di PowerShell su un sistema utente, è possibile che il cmdlet non riesca per vari motivi correlati alla logica di business. Il numero di licenze potrebbe ad esempio essere insufficiente o è possibile che siano presenti conflitti tra due piani di servizio che non possono essere assegnati contemporaneamente. Il problema viene segnalato immediatamente all'utente.

Quando si usano le licenze basate sui gruppi, è possibile che si verifichino gli stessi errori, che però rimangono in background mentre il servizio Azure AD assegna le licenze. Per questo motivo gli errori non possono essere comunicati immediatamente all'utente. Vengono invece registrati nell'oggetto utente e segnalati tramite il portale amministrativo. L'intento originale di assegnare una licenza all'utente resta, ma la licenza viene registrata in stato di errore che è possibile analizzare e risolvere in un secondo momento.

## <a name="how-to-find-license-assignment-errors"></a>Come individuare gli errori di assegnazione licenze
**Per individuare gli errori di assegnazione licenze**

   1. Per trovare gli utenti con stato di errore in un gruppo specifico, aprire il riquadro del gruppo. In **Licenze** compare una notifica se sono presenti utenti con stato di errore.

   ![Gruppo, notifica di errore](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

   2. Selezionare la notifica per aprire un elenco di tutti gli utenti interessati. È possibile selezionare ogni singolo utente per visualizzare altri dettagli.

   ![Gruppo, elenco degli utenti in stato di errore](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

   3. Per trovare tutti i gruppi che contengono almeno un errore, nel pannello **Azure Active Directory** selezionare **Licenze** e quindi selezionare **Panoramica**. Se ci sono gruppi che richiedono attenzione, viene visualizzata una casella di informazioni.

   ![Panoramica, informazioni sui gruppi in stato di errore](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

   4. Selezionare la casella per visualizzare un elenco di tutti i gruppi con errori. È possibile selezionare ciascun gruppo per visualizzare altri dettagli.

   ![Panoramica, elenco di gruppi con errori](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


Le sezioni seguenti riportano una descrizione di ogni potenziale problema con la relativa soluzione.

## <a name="not-enough-licenses"></a>Le licenze non sono sufficienti

**Problema:** le licenze disponibili per uno dei prodotti specificati nel gruppo non sono sufficienti. È necessario acquistare altre licenze per il prodotto o liberare le licenze inutilizzate da altri utenti o gruppi.

Per vedere il numero di licenze disponibili, passare ad **Azure Active Directory** > **Licenze** > **Tutti i prodotti**.

Per vedere quali utenti e gruppi utilizzano le licenze, selezionare un prodotto. In **Utenti con licenza** compare un elenco di tutti gli utenti a cui sono state assegnate licenze direttamente o tramite uno o più gruppi. In **Gruppi con licenza** compaiono tutti i gruppi a cui sono assegnati prodotti.

**PowerShell:** i cmdlet di PowerShell segnalano questo errore come _CountViolation_.

## <a name="conflicting-service-plans"></a>Piani di servizio in conflitto

**Problema:** uno dei prodotti specificati nel gruppo contiene un piano di servizio in conflitto con un altro piano di servizio già assegnato all'utente tramite un prodotto diverso. Alcuni piani di servizio vengono configurati in modo che non sia possibile assegnarli allo stesso utente come un altro piano di servizio correlato.

Si consideri l'esempio seguente. A un utente viene assegnata direttamente una licenza per Office 365 Enterprise *E1*, con tutti i piani abilitati. L'utente è stato aggiunto a un gruppo a cui è assegnato il prodotto Office 365 Enterprise *E3*. Il prodotto E3 contiene piani di servizio che non possono sovrapporsi con i piani inclusi in E1, quindi l'assegnazione della licenza di gruppo non riesce, generando il messaggio di errore "Piani di servizio in conflitto". In questo esempio, i piani di servizio in conflitto sono:

-   SharePoint Online (piano 2) in conflitto con SharePoint Online (piano 1).
-   Exchange Online (piano 2) in conflitto con Exchange Online (piano 1).

Per risolvere questo conflitto è necessario disabilitare due dei piani. È possibile disattivare la licenza E1 che è stata direttamente assegnata all'utente. oppure occorre modificare l'assegnazione di licenze per l'intero gruppo e disabilitare i piani nella licenza E3. In alternativa, è possibile decidere di rimuovere la licenza E1 dall'utente, se è ridondante nel contesto della licenza E3.

La decisione sulla risoluzione dei problemi relativi a licenze dei prodotti in conflitto spetta sempre all'amministratore. Azure AD non risolve automaticamente i conflitti di licenza.

**PowerShell:** i cmdlet di PowerShell segnalano questo errore come _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Altri prodotti dipendono da questa licenza

**Problema:** uno dei prodotti specificati nel gruppo contiene un piano di servizio che per funzionare deve essere abilitato per un altro piano di servizio, in un altro prodotto. Questo errore si verifica quando Azure AD prova a rimuovere il piano di servizio sottostante. Ad esempio, può verificarsi quando si rimuove l'utente dal gruppo.

Per risolvere questo problema, è necessario assicurarsi che il piano richiesto sia ancora assegnato agli utenti tramite un altro metodo oppure che i servizi dipendenti siano disabilitati per tali utenti. Al termine di questa operazione, è possibile rimuovere correttamente la licenza di gruppo da questi utenti.

**PowerShell:** i cmdlet di PowerShell segnalano questo errore come _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>La località di utilizzo non è consentita

**Problema:** alcuni servizi Microsoft non sono disponibili in tutte le località a causa di leggi e regolamenti locali. Prima di assegnare una licenza a un utente, è necessario specificare la proprietà **Località di utilizzo** per l'utente. È possibile specificare la posizione nella sezione **Utente** > **Profilo** > **Impostazioni** del portale di Azure.

Quando Azure AD tenta di assegnare una licenza di gruppo a un utente la cui località di utilizzo non è supportata, l'operazione non riesce e viene registrato un errore per l'utente.

Per risolvere questo problema, rimuovere gli utenti di località non supportate dal gruppo con licenza. In alternativa, se i valori correnti relativi alla località di utilizzo non rappresentano la località effettiva degli utenti, è possibile modificarli in modo che la volta successiva le licenze vengano assegnate correttamente (se la nuova località è supportata).

**PowerShell:** i cmdlet di PowerShell segnalano questo errore come _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Quando Azure AD assegna licenze di gruppo, gli utenti per cui non è specificata una località di utilizzo ereditano la località della directory. È consigliabile che gli amministratori impostino valori corretti per la località di utilizzo degli utenti prima di usare licenze basate sul gruppo, in modo da rispettare le normative e le leggi locali.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Cosa accade quando un gruppo include più di una licenza del prodotto

È possibile assegnare più di una licenza del prodotto a un gruppo. È ad esempio possibile assegnare Office 365 Enterprise E3 ed Enterprise Mobility + Security a un gruppo per abilitare facilmente tutti i servizi inclusi per gli utenti.

Azure AD prova ad assegnare a ogni utente tutte le licenze specificate nel gruppo. Se Azure AD non riesce ad assegnare uno dei prodotti a causa di problemi di logica di business, non assegnerà neppure le altre licenze nel gruppo. Un esempio è se non sono disponibili licenze sufficienti per tutti o se sono presenti conflitti con altri servizi che sono abilitati per l'utente.

È possibile visualizzare gli utenti per i quali l'assegnazione non è riuscita e i relativi prodotti interessati.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Come si gestiscono le licenze per prodotti con prerequisiti?

Alcuni prodotti Microsoft Online sono *componenti aggiuntivi*. I componenti aggiuntivi richiedono un piano di servizio dei prerequisiti per essere abilitati per un utente o un gruppo, prima che sia possibile assegnarvi una licenza. Con le licenze basate su gruppo, il sistema prevede che i piani di servizio dei prerequisiti e dei componenti aggiuntivi si trovino nello stesso gruppo. Questa operazione viene eseguita per garantire che tutti gli utenti aggiunti al gruppo possano ricevere il prodotto completamente funzionante. Si consideri l'esempio seguente:

Microsoft Workplace Analytics è un componente aggiuntivo. Contiene un unico piano di servizio con lo stesso nome. È possibile assegnare questo piano di servizio a un utente o a gruppo solo quando viene assegnato anche uno dei seguenti prerequisiti:
- Exchange Online (piano 1) 
- Exchange Online (piano 2)

Se si tenta di assegnare questo prodotto da solo a un gruppo, il portale restituisce un errore. Selezionando la notifica di errore vengono visualizzati i dettagli seguenti:

![Prerequisito del gruppo mancante](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

Se si selezionano i dettagli, viene visualizzato il messaggio di errore seguente:

>L'operazione relativa alla licenza non è riuscita. Assicurarsi che il gruppo abbia i servizi necessari prima di aggiungere o rimuovere un servizio dipendente. **Il servizio Microsoft Workplace Analytics richiede che sia anche abilitato il servizio Exchange Online (piano 2).**

Per assegnare questa licenza del componente aggiuntivo a un gruppo, è necessario assicurarsi che anche il gruppo contenga il piano di servizio del prerequisito. Ad esempio, è possibile aggiornare un gruppo esistente che contiene già il prodotto completo Office 365 E3 e quindi aggiungervi il componente aggiuntivo.

È anche possibile creare un gruppo autonomo che contenga solo i prodotti minimi richiesti per far funzionare il componente aggiuntivo. Può essere usato per concedere la licenza del componente aggiuntivo solo agli utenti selezionati. In questo esempio i prodotti seguenti sono stati assegnati allo stesso gruppo:
- Office 365 Enterprise E3 solo con il piano di servizio Exchange Online (piano 2) abilitato
- Microsoft Workplace Analytics

![Prerequisito del gruppo incluso](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

Da questo momento in poi gli utenti aggiunti a questo gruppo useranno una licenza del prodotto E3 e una licenza del prodotto Workplace Analytics. Allo stesso tempo gli utenti possono essere membri di un altro gruppo che concede loro il prodotto E3 completo e continueranno a usare solo una licenza per il prodotto in questione.

> [!TIP]
> È possibile creare più gruppi, per ogni piano di servizio dei prerequisiti. Ad esempio, se si usano Office 365 Enterprise E1 e Office 365 Enterprise E3 per gli utenti, è possibile creare due gruppi per la licenza di Microsoft Workplace Analytics; uno che usa E1 come prerequisito e l'altro che usa E3. Ciò consente di distribuire il componente aggiuntivo agli utenti di E1 ed E3 senza usare altre licenze.

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>L'assegnazione delle licenze non riesce in modo silenzioso per un utente a causa di indirizzi proxy duplicati in Exchange Online

Se si usa Exchange Online, alcuni utenti nel tenant potrebbero non essere configurati correttamente con lo stesso valore di indirizzo proxy. Quando il meccanismo delle licenze basate sui gruppi tenta di assegnare una licenza a questo utente, il tentativo non riesce e non viene registrato un errore. La mancata registrazione dell'errore in questo caso è una limitazione della versione di anteprima di questa funzionalità che sarà risolta prima della *disponibilità generale*.

> [!TIP]
> Se si nota che alcuni utenti non hanno ricevuto una licenza e non ci sono errori registrati per tali utenti, per prima cosa controllare se hanno un indirizzo proxy duplicato.
> Per verificare se esiste un indirizzo proxy duplicato, eseguire il seguente cmdlet di PowerShell con Exchange Online:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> Per altre informazioni su questo problema, vedere [Messaggio di errore "indirizzo proxy < indirizzo > è già in uso" in Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). L'articolo include anche informazioni su [come connettersi a Exchange Online usando PowerShell remoto](https://technet.microsoft.com/library/jj984289.aspx).

Dopo aver risolto i problemi di indirizzo proxy per gli utenti interessati, forzare l'elaborazione delle licenze nel gruppo per assicurarsi che ora sia possibile applicarle.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Come forzare l'elaborazione delle licenze in un gruppo per risolvere gli errori

A seconda delle azioni intraprese per risolvere gli errori, potrebbe essere necessario attivare manualmente l'elaborazione di un gruppo per aggiornare lo stato dell'utente.

Ad esempio, se sono state liberate alcune licenze rimuovendo le assegnazioni di licenza dirette dagli utenti, sarà necessario attivare l'elaborazione dei gruppi che in precedenza è riuscita per poter assegnare licenze complete a tutti i membri utente. Per rielaborare un gruppo, passare al riquadro del gruppo, **Licenze**, quindi selezionare il pulsante **Rielabora** sulla barra degli strumenti.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari per la gestione delle licenze tramite i gruppi, vedere:

* [Assegnazione di licenze a un gruppo in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-advanced.md)
