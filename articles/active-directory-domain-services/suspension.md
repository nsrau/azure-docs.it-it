---
title: Domini sospesi in Azure AD Domain Services | Microsoft Docs
description: Informazioni sui diversi Stati di integrità per un dominio gestito di Azure AD DS e su come ripristinare un dominio sospeso.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 106cd870a8ba52917ecabe6266bbb2c376296d79
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704264"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Informazioni sugli stati di integrità e risoluzione dei domini sospesi in Azure Active Directory Domain Services

Quando Azure Active Directory Domain Services non è in grado di amministrare un dominio gestito per un lungo periodo di tempo, lo stato del dominio diventa sospeso. Se un dominio gestito rimane in uno stato sospeso, viene eliminato automaticamente. Per rendere integro il dominio gestito di Azure AD DS ed evitare la sospensione, risolvere tutti gli avvisi nel minor tempo possibile.

Questo articolo spiega perché i domini gestiti sono sospesi e come ripristinare un dominio sospeso.

## <a name="overview-of-managed-domain-states"></a>Panoramica degli Stati del dominio gestito

Attraverso il ciclo di vita di un dominio gestito Azure AD DS, esistono diversi Stati che ne indicano l'integrità. Se il dominio gestito segnala un problema, risolvere rapidamente la causa sottostante per arrestare lo stato continuando a peggiorare.

![La progressione degli Stati che un dominio gestito di Azure AD DS impiega per la sospensione](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Un dominio gestito di Azure AD DS può trovarsi in uno degli Stati seguenti:

* [Running](#running-state)
* [Richiede attenzione](#needs-attention-state)
* [Sospeso](#suspended-state)
* [Eliminata](#deleted-state)

## <a name="running-state"></a>Stato di esecuzione

Un dominio gestito Azure AD DS configurato correttamente e in esecuzione senza problemi è nello stato in *esecuzione* . Si tratta dello stato desiderato per un dominio gestito.

### <a name="what-to-expect"></a>Programma

* La piattaforma Azure può monitorare regolarmente l'integrità del dominio gestito.
* I controller di dominio per il dominio gestito vengono corretti e aggiornati regolarmente.
* Le modifiche da Azure Active Directory vengono regolarmente sincronizzate con il dominio gestito.
* Per il dominio gestito vengono eseguiti backup regolari.

## <a name="needs-attention-state"></a>Stato richiesta attenzione

Un dominio gestito Azure AD DS con uno o più problemi che devono essere corretti è nello stato *needs attention* . La pagina integrità per il dominio gestito elenca gli avvisi e indica dove si è verificato un problema. Alcuni avvisi sono temporanei e vengono risolti automaticamente dalla piattaforma Azure. Per altri avvisi, è possibile risolvere il problema attenendosi alla procedura di risoluzione fornita. È presente un avviso critico. per ulteriori informazioni sulla risoluzione dei problemi, [aprire una richiesta di supporto tecnico di Azure][azure-support] .

Un esempio di avviso è costituito da un gruppo di sicurezza di rete restrittivo. In questa configurazione, la piattaforma Azure potrebbe non essere in grado di aggiornare e monitorare il dominio gestito. Viene generato un avviso e lo stato cambia in *richiesta attenzione*.

Per ulteriori informazioni, vedere [come risolvere i problemi relativi agli avvisi per un dominio gestito di Azure AD DS][resolve-alerts].

### <a name="what-to-expect"></a>Programma

Quando un dominio gestito di Azure AD DS è nello stato *richiesta attenzione* , la piattaforma Azure potrebbe non essere in grado di monitorare, applicare patch, aggiornare o eseguire il backup dei dati a intervalli regolari. In alcuni casi, ad esempio con una configurazione di rete non valida, è possibile che i controller di dominio per il dominio gestito non siano raggiungibili.

* Il dominio gestito è in uno stato non integro e il monitoraggio dell'integrità in corso può arrestarsi fino a quando l'avviso non viene risolto.
* Non è possibile applicare patch o aggiornare i controller di dominio per il dominio gestito.
* Le modifiche da Azure Active Directory potrebbero non essere sincronizzate con il dominio gestito.
* I backup per il dominio gestito non possono essere eseguiti.
* Se si risolvono gli avvisi non critici che hanno un effetto sul dominio gestito, l'integrità dovrebbe tornare allo stato *in esecuzione* .
* Gli avvisi critici vengono attivati per i problemi di configurazione in cui la piattaforma Azure non è in grado di raggiungere i controller di dominio. Se questi avvisi critici non vengono risolti entro 15 giorni, il dominio gestito entra nello stato *suspended* .

## <a name="suspended-state"></a>Stato sospeso

Un dominio gestito di Azure AD DS entra nello stato **suspended** per uno dei seguenti motivi:

* Uno o più avvisi critici non sono stati risolti in 15 giorni.
    * Gli avvisi critici possono essere provocati da una configurazione non corretta che blocca l'accesso alle risorse necessarie da parte di Azure Active Directory Domain Services. Ad esempio, l'avviso [AADDS104: errore di rete][alert-nsg] non è stato risolto da oltre 15 giorni nel dominio gestito.
* Si è verificato un problema di fatturazione con la sottoscrizione di Azure o la sottoscrizione di Azure è scaduta.

I domini gestiti vengono sospesi quando la piattaforma Azure non è in grado di gestire, monitorare, applicare patch o eseguire il backup del dominio. Un dominio gestito rimane in stato *sospeso* per 15 giorni. Per mantenere l'accesso al dominio gestito, risolvere immediatamente gli avvisi critici.

### <a name="what-to-expect"></a>Programma

Il comportamento seguente si verifica quando un dominio gestito di Azure AD DS si trova nello stato *suspended* :

* I controller di dominio per il dominio gestito sono deprovisioned e non sono raggiungibili all'interno della rete virtuale.
* LDAP sicuro l'accesso al dominio gestito su Internet, se abilitato, smette di funzionare.
* Si sono verificati errori durante l'autenticazione al dominio gestito, l'accesso a macchine virtuali appartenenti a un dominio o la connessione tramite LDAP/LDAPs.
* I backup per il dominio gestito non vengono più eseguiti.
* La sincronizzazione con Azure AD si interrompe.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Come è possibile sapere se il dominio gestito è sospeso?

Viene visualizzato un [avviso][resolve-alerts] nella pagina relativa all'integrità di Azure AD DS nel portale di Azure in cui si nota che il dominio è sospeso. Lo stato del dominio Mostra anche *sospeso*.

### <a name="restore-a-suspended-domain"></a>Ripristinare un dominio sospeso

Per ripristinare l'integrità di un dominio gestito di Azure AD DS nello stato *suspended* , attenersi alla procedura seguente:

1. Nella portale di Azure cercare e selezionare **servizi di dominio**.
1. Scegliere il dominio gestito di Azure AD DS dall'elenco, ad esempio *aadds.contoso.com*, quindi selezionare **Health (integrità**).
1. Selezionare l'avviso, ad esempio *AADDS503* o *AADDS504*, a seconda della cause della sospensione.
1. Scegliere il collegamento di risoluzione fornito nell'avviso e seguire la procedura per risolverlo.

Un dominio gestito può essere ripristinato solo fino alla data dell'ultimo backup. La data dell'ultimo backup viene visualizzata nella pagina **stato** del dominio gestito. Eventuali modifiche apportate dopo l'ultimo backup non vengono ripristinate. I backup per un dominio gestito vengono archiviati per un massimo di 30 giorni. I backup eseguiti prima dei 30 giorni vengono eliminati.

Dopo aver risolto gli avvisi quando il dominio gestito si trova nello stato *suspended* , [aprire una richiesta di supporto di Azure][azure-support] per tornare a uno stato integro. Se è presente un backup inferiore a 30 giorni, il supporto tecnico di Azure può ripristinare il dominio gestito.

## <a name="deleted-state"></a>Stato eliminato

Se uno Azure AD dominio gestito DS rimane nello stato *suspended* per 15 giorni, viene eliminato. Questo processo è irreversibile.

### <a name="what-to-expect"></a>Programma

Quando un dominio gestito di Azure AD DS entra nello stato *eliminato* , viene visualizzato il comportamento seguente:

* Vengono eliminati tutti i backup e le risorse del dominio gestito.
* Non è possibile ripristinare il dominio gestito ed è necessario creare un dominio gestito sostitutivo per riutilizzare Azure AD DS.
* Dopo l'eliminazione, in relazione al dominio gestito non viene più emessa alcuna fattura.

## <a name="next-steps"></a>Passaggi successivi

Per fare in modo che il dominio gestito di Azure AD DS sia integro e ridurre al minimo il rischio che venga sospeso, informazioni su come [risolvere gli avvisi per il dominio gestito][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
