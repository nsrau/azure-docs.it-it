---
title: 'Azure Active Directory Domain Services: domini sospesi | Microsoft Docs'
description: Sospensione ed eliminazione di domini gestiti
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: ergreenl
ms.openlocfilehash: 6448e592db8024d911831b0b3634dabb4ed3b101
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063116"
---
# <a name="suspended-domains"></a>Domini sospesi
Quando Azure Active Directory Domain Services non è in grado di amministrare un dominio gestito per un lungo periodo di tempo, lo stato del dominio diventa sospeso. Questo articolo descrive il motivo per cui i domini gestiti vengono sospesi e il modo in cui risolvere il problema.


## <a name="states-your-managed-domain-can-be-in"></a>Stati possibili del dominio gestito

![Sequenza temporale dei domini sospesi](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

La figura precedente illustra gli stati possibili in cui può trovarsi un dominio gestito di Azure Active Directory Domain Services.

### <a name="running-state"></a>Stato "In esecuzione"
Un dominio gestito configurato in modo corretto e funzionante regolarmente si trova nello stato **In esecuzione**.

**Cosa aspettarsi?**
* Microsoft può monitorare regolarmente l'integrità del dominio gestito.
* Per i controller del dominio gestito vengono installati aggiornamenti e applicate patch con frequenza regolare.
* Le modifiche da Azure Active Directory vengono sincronizzate regolarmente con il dominio gestito.
* Vengono eseguiti backup regolari del dominio gestito.


### <a name="needs-attention-state"></a>Stato "Richiede attenzione"
Un dominio gestito si trova nello stato **Richiede attenzione** se uno o più problemi richiedono l'intervento di un amministratore. La pagina di integrità del dominio gestito elenca uno o più avvisi correlati a questo stato. 

Se ad esempio è stato configurato un gruppo di sicurezza di rete restrittivo per la rete virtuale, è possibile che Microsoft non sia in grado di aggiornare e monitorare il dominio gestito. Questa configurazione non valida attiva un avviso e lo stato del dominio gestito viene impostato su "Richiede attenzione".

A ogni avviso è associato a un set di passaggi per la risoluzione. Alcuni avvisi sono temporanei e vengono risolti automaticamente dal servizio. Per risolvere altri avvisi, seguire le istruzioni nella procedura di risoluzione corrispondente. Per risolvere alcuni avvisi critici, è necessario contattare il supporto tecnico Microsoft.

Per altre informazioni, vedere [How to troubleshoot alerts on a managed domain](active-directory-ds-troubleshoot-alerts.md) (Come risolvere gli avvisi correlati a un dominio gestito).

**Cosa aspettarsi?**

In alcuni casi, ad esempio se è presente una configurazione di rete non valida, è possibile che i controller del dominio gestito non siano raggiungibili. Quando il dominio gestito è in stato "Richiede attenzione", Microsoft non garantisce il monitoraggio, l'applicazione di patch, l'aggiornamento o il backup con frequenza regolare.

* Il dominio gestito è in uno stato non integro e il monitoraggio costante dell'integrità può interrompersi fino a quando il problema che ha generato l'avviso non viene risolto.
* È possibile che l'applicazione di patch o gli aggiornamenti non vengano eseguiti per i controller del dominio gestito.
* Le modifiche da Azure Active Directory possono non essere sincronizzate con il dominio gestito.
* Se possibile, vengono eseguiti backup per il dominio gestito.
* Se si risolvono gli avvisi che interessano il dominio gestito, lo stato di quest'ultimo può essere ripristinato su "In esecuzione".
* Se si verificano problemi di configurazione per cui Microsoft non riesce a raggiungere i controller di dominio, vengono generati avvisi critici. Se tali avvisi non vengono risolti entro 15 giorni, lo stato del dominio gestito viene impostato su "Sospeso".


### <a name="the-suspended-state"></a>Stato "Sospeso"
Lo stato di un dominio gestito viene impostato su **Sospeso** per i motivi seguenti:

* Uno o più avvisi critici non sono stati risolti in 15 giorni. Gli avvisi critici possono essere provocati da una configurazione non corretta che blocca l'accesso alle risorse necessarie da parte di Azure Active Directory Domain Services.
    * Ad esempio, l'avviso [AADDS104: errore di rete](active-directory-ds-troubleshoot-nsg.md) non è stato risolto da oltre 15 giorni nel dominio gestito.
* Si è verificato un problema di fatturazione nella sottoscrizione di Azure o tale sottoscrizione è scaduta.

I domini gestiti vengono sospesi quando Microsoft non riesce a gestire e monitorare il dominio, applicarvi patch o eseguirne il backup in modo continuativo.

**Cosa aspettarsi?**
* Per i controller del dominio gestito viene effettuato il deprovisioning e tali controller non sono raggiungibili nella rete virtuale.
* L'accesso LDAP sicuro al dominio gestito in Internet (se abilitato) smette di funzionare.
* Si verificano errori di autenticazione al dominio gestito, di accesso alle macchine virtuali appartenenti a un dominio o di connessione tramite LDAP/LDAPS.
* I backup del dominio gestito non vengono più eseguiti.
* La sincronizzazione con Azure AD si interrompe.

Dopo che è stato risolto l'avviso, il dominio gestito passa allo stato "Sospeso". A quel punto è necessario contattare il supporto.
Il servizio di supporto può ripristinare il dominio gestito solo se è disponibile un backup eseguito entro i 30 giorni precedenti.

Il dominio gestito rimane nello stato Sospeso solo per 15 giorni. Per ripristinare il dominio gestito, Microsoft consiglia di risolvere immediatamente gli avvisi critici.


### <a name="deleted-state"></a>Stato "Eliminato"
Un dominio gestito che rimane nello stato "Sospeso" per 15 giorni è **Eliminato**.

**Cosa aspettarsi?**
* Vengono eliminati tutti i backup e le risorse del dominio gestito.
* Non è possibile ripristinare il dominio gestito ed è necessario creare un nuovo dominio gestito per usare Azure Active Directory Domain Services.
* Dopo l'eliminazione, in relazione al dominio gestito non viene più emessa alcuna fattura.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Come è possibile sapere se il dominio gestito è sospeso?
Viene visualizzato un [avviso](active-directory-ds-troubleshoot-alerts.md) nella pagina di integrità di Azure Active Directory Domain Services nel portale di Azure che dichiara il dominio sospeso. Lo stato del dominio viene visualizzato come "Sospeso".


## <a name="restore-a-suspended-domain"></a>Ripristinare un dominio sospeso
Per ripristinare un dominio nello stato "Sospeso", seguire questa procedura:

1. Andare alla [pagina Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) nel portale di Azure.
2. Selezionare il dominio gestito.
3. Nel pannello di sinistra selezionare **Health** (Integrità).
4. Selezionare l'avviso. L'ID avviso è AADDS503 o AADDS504, a seconda della causa della sospensione.
5. Selezionare il collegamento di risoluzione presente nell'avviso. Seguire la procedura per risolvere l'avviso.

Il dominio gestito può essere ripristinato solo sulla data dell'ultimo backup, visualizzata nella pagina Integrità del dominio gestito. Eventuali modifiche apportate dopo l'ultimo backup non vengono ripristinate. I backup per un dominio gestito vengono archiviati per un massimo di 30 giorni. I backup eseguiti prima dei 30 giorni vengono eliminati.


## <a name="next-steps"></a>Passaggi successivi
- [Risolvere gli avvisi per il dominio gestito](active-directory-ds-troubleshoot-alerts.md)
- [Altre informazioni su Azure Active Directory Domain Services](active-directory-ds-overview.md)
- [Contattare il team del prodotto](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Contatti
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
