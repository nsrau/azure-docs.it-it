---
title: 'Servizio di sincronizzazione Azure AD Connect: impedire eliminazioni accidentali | Documentazione Microsoft'
description: "Questo argomento descrive la funzionalità per impedire le eliminazioni accidentali in Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 48531d69fcefed27785e0e1ae667274fa48ea1d2
ms.lasthandoff: 03/24/2017


---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Servizio di sincronizzazione Azure AD Connect: Impedire eliminazioni accidentali
Questo argomento descrive la funzionalità per impedire le eliminazioni accidentali in Azure AD Connect.

Quando si installa Azure AD Connect, la funzionalità per impedire le eliminazioni accidentali viene abilitata per impostazione predefinita e configurata in modo da non consentire un'esportazione con più di 500 eliminazioni. Questa funzionalità è progettata per la protezione da modifiche accidentali della configurazione e della directory locale che possono interessare un numero elevato di utenti e altri oggetti.

## <a name="what-is-prevent-accidental-deletes"></a>Informazioni sulla prevenzione di eliminazioni accidentali
Gli scenari comuni in cui si verificano molte eliminazioni includono:

* Modifiche ai [filtri](active-directory-aadconnectsync-configure-filtering.md) in cui è deselezionata un'intera [unità organizzativa](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) o un [dominio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering).
* Vengono eliminati tutti gli oggetti in un'unità organizzativa.
* Un'unità organizzativa viene rinominata in modo che tutti gli oggetti in essa contenuti vengano considerati al di fuori dell'ambito di sincronizzazione.

Il valore predefinito di 500 oggetti può essere modificato con PowerShell utilizzando `Enable-ADSyncExportDeletionThreshold`. È consigliabile configurare questo valore in base alle dimensioni dell'organizzazione. Poiché l'utilità di pianificazione della sincronizzazione viene eseguita ogni 30 minuti, il valore è il numero di eliminazioni visualizzate in 30 minuti.

Se il numero di eliminazioni da esportare in Azure AD è troppo elevato, l'esportazione verrà arrestata e si riceverà un messaggio di posta elettronica simile al seguente:

![Messaggio di posta elettronica per evitare eliminazioni accidentali](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Gentile (contatto tecnico), Sincronizzazione delle identità: il giorno (data) è stato rilevato che il numero di eliminazioni ha superato la soglia di eliminazione per (nome dell'organizzazione). È stato inviato un totale di (numero) oggetti per l'eliminazione in questa esecuzione di sincronizzazione delle identità. È stato quindi raggiunto o superato il valore della soglia di eliminazione configurato di (numero) oggetti. Prima di continuare, è necessario confermare di voler procedere con l'elaborazione di queste eliminazioni. Per altre informazioni sull'errore indicato in questo messaggio di posta elettronica, vedere l'articolo che illustra come evitare eliminazioni accidentali.*
>
> 

È anche possibile visualizzare lo stato `stopped-deletion-threshold-exceeded` nell'interfaccia utente di **Synchronization Service Manager** per il profilo di esportazione.
![Interfaccia utente di Synchronization Service Manager per evitare eliminazioni accidentali](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se si tratta di un messaggio inatteso, ricercare la causa e intraprendere eventuali azioni correttive. Per visualizzare gli oggetti che devono essere eliminati, procedere come segue:

1. Avviare **Servizio di sincronizzazione** dal Menu start.
2. Passare alla pagina **Connettori**.
3. Selezionare il connettore con tipo **Azure Active Directory**.
4. In **Azioni** a destra selezionare **Spazio connettore di ricerca**.
5. Nella casella popup in **Scope** (Ambito) selezionare **Disconnected Since** (Disconnesso dal) e selezionare una data/ora trascorsa. Fare clic su **Search**(Cerca). Questa pagina offre la visualizzazione di tutti gli oggetti che verranno eliminati. Facendo clic su ogni elemento è possibile ottenere altre informazioni sull'oggetto. È anche possibile fare clic su **Column Settings** (Impostazioni colonna) per aggiungere altri attributi da visualizzare nella griglia.

![Spazio connettore di ricerca](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Se si desidera tutte le eliminazioni, eseguire le operazioni seguenti:

1. Per recuperare la soglia di eliminazione corrente, eseguire il cmdlet di PowerShell `Get-ADSyncExportDeletionThreshold`. Indicare un account di amministratore globale di Azure AD e una password. Il valore predefinito è 500.
2. Per disabilitare temporaneamente la protezione e consentire l'esportazione di queste eliminazioni, eseguire il cmdlet PowerShell: `Disable-ADSyncExportDeletionThreshold`. Indicare un account di amministratore globale di Azure AD e una password.
   ![Credenziali](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
3. Con il connettore Azure Active Directory ancora selezionato, selezionare l'azione **Esegui** e selezionare **Esporta**.
4. Per riabilitare la protezione, eseguire il cmdlet PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Sostituire 500 con il valore osservato quando si recupera la soglia di eliminazione corrente. Indicare un account di amministratore globale di Azure AD e una password.

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

