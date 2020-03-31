---
title: Domini sospesi in Servizi di dominio di Azure AD Documenti Microsoft
description: Informazioni sui diversi stati di integrità per un dominio gestito di Azure AD DS e su come ripristinare un dominio sospeso.
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
ms.openlocfilehash: 8a82d2ad3e79633bb930348c6162996e961c4306
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612958"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Comprendere gli stati di integrità e risolvere i domini sospesi in Servizi di dominio Azure Active DirectoryUnderstand the health states and resolve suspended domains in Azure Active Directory Domain Services

Quando Azure Active Directory Domain Services non è in grado di amministrare un dominio gestito per un lungo periodo di tempo, lo stato del dominio diventa sospeso. Se un dominio gestito rimane in uno stato sospeso, viene eliminato automaticamente. Per mantenere integro il dominio gestito di Servizi di dominio Active Directory di Azure ed evitare la sospensione, risolvere gli avvisi nel più rapidamente possibile.

Questo articolo spiega perché i domini gestiti vengono sospesi e come ripristinare un dominio sospeso.

## <a name="overview-of-managed-domain-states"></a>Panoramica degli stati del dominio gestito

Durante il ciclo di vita di un dominio gestito di Servizi di dominio Active Directory di Azure, esistono stati diversi che ne indicano l'integrità. Se il dominio gestito segnala un problema, risolvere rapidamente la causa sottostante per impedire che lo stato continui a degradarsi.

![La progressione degli stati che un dominio gestito di Azure AD DS porta alla sospensione](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Un dominio gestito di Azure AD DS può trovarsi in uno degli stati seguenti:An Azure AD DS managed domain can be in one of the following states:

* [In esecuzione](#running-state)
* [Ha bisogno di attenzione](#needs-attention-state)
* [Sospeso](#suspended-state)
* [Eliminato](#deleted-state)

## <a name="running-state"></a>Stato di esecuzione

Nello stato *In esecuzione* è presente un dominio gestito di Azure AD DS configurato correttamente e in esecuzione senza problemi. Questo è lo stato desiderato per un dominio gestito.

### <a name="what-to-expect"></a>Cosa aspettarsi

* La piattaforma Azure può monitorare regolarmente l'integrità del dominio gestito.
* I controller di dominio per il dominio gestito vengono aggiornati e aggiornati regolarmente.
* Le modifiche da Azure Active Directory vengono sincronizzate regolarmente con il dominio gestito.
* Vengono eseguiti backup regolari per il dominio gestito.

## <a name="needs-attention-state"></a>Necessità stato di attenzione

Un dominio gestito di Servizi di dominio Active Directory di Azure con uno o più problemi che devono essere risolti si trova nello stato *di attenzione Esigenze.An* Azure AD DS managed domain with one or more issues that need to be fixed is in the Needs attention state. La pagina di integrità per il dominio gestito elenca gli avvisi e indica dove si è verificato un problema. Alcuni avvisi sono temporanei e vengono risolti automaticamente dalla piattaforma Azure.Some alerts are transient and are automatically resolved by the Azure platform. Per altri avvisi, è possibile risolvere il problema seguendo la procedura di risoluzione fornita. È presente un avviso critico, aprire una richiesta di supporto di [Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

Un esempio di avviso è quando è presente un gruppo di sicurezza di rete restrittivo. In questa configurazione, la piattaforma Azure potrebbe non essere in grado di aggiornare e monitorare il dominio gestito. Viene generato un avviso e lo stato cambia in *Richiede attenzione*.

Per altre informazioni, vedere Come risolvere i problemi relativi agli avvisi per un dominio gestito di Servizi di dominio Active Directory di Azure.For more information, see [How to troubleshoot alerts for an Azure AD DS managed domain][resolve-alerts].

### <a name="what-to-expect"></a>Cosa aspettarsi

Quando un dominio gestito di Servizi di dominio Active Directory di Azure è nello stato *Needs Attention,* la piattaforma Azure potrebbe non essere in grado di monitorare, applicare patch, aggiornare o eseguire il backup dei dati a intervalli regolari. In alcuni casi, ad esempio con una configurazione di rete non valida, i controller di dominio per il dominio gestito potrebbero non essere raggiungibili.

* Il dominio gestito è in uno stato non integro e il monitoraggio dell'integrità in corso potrebbe interrompersi fino a quando l'avviso non viene risolto.
* I controller di dominio per il dominio gestito non possono essere aggiornati o aggiornati.
* Le modifiche da Azure Active Directory potrebbero non essere sincronizzate con il dominio gestito.
* I backup per il dominio gestito potrebbero non essere eseguiti.
* Se si risolvono gli avvisi non critici che influiscono sul dominio gestito, l'integrità deve tornare allo stato *In esecuzione.*
* Gli avvisi critici vengono attivati per i problemi di configurazione in cui la piattaforma Azure non può raggiungere i controller di dominio. Se questi avvisi critici non vengono risolti entro 15 giorni, il dominio gestito passa allo stato *Sospeso.If* these critical alerts aren't resolved within 15 days, the managed domain enter the Suspended state.

## <a name="suspended-state"></a>Stato sospeso

Un dominio gestito di Azure AD DS passa allo stato Sospeso per uno dei motivi seguenti:An Azure AD DS managed domain enters the **Suspended** state for one of the following reasons:

* Uno o più avvisi critici non sono stati risolti in 15 giorni.
    * Gli avvisi critici possono essere provocati da una configurazione non corretta che blocca l'accesso alle risorse necessarie da parte di Azure Active Directory Domain Services. Ad esempio, l'avviso [AADDS104: errore di rete][alert-nsg] non è stato risolto da oltre 15 giorni nel dominio gestito.
* Si è verificato un problema di fatturazione con la sottoscrizione di Azure o la sottoscrizione di Azure è scaduta.

I domini gestiti vengono sospesi quando la piattaforma Azure non è in grado di gestire, monitorare, applicare patch o eseguire il backup del dominio. Un dominio gestito rimane in uno stato *Sospeso* per 15 giorni. Per mantenere l'accesso al dominio gestito, risolvere immediatamente gli avvisi critici.

### <a name="what-to-expect"></a>Cosa aspettarsi

Il comportamento seguente si verifica quando un dominio gestito di Azure AD DS è nello stato *Sospeso:The* following behavior is experienced when an Azure AD DS managed domain is in the Suspended state:

* I controller di dominio per il dominio gestito vengono sottoposti a deprovisioning e non sono raggiungibili all'interno della rete virtuale.
* L'accesso LDAP sicuro al dominio gestito tramite Internet, se abilitato, smette di funzionare.
* Si verificano errori nell'autenticazione al dominio gestito, nell'accesso a macchine virtuali aggiunte al dominio o nella connessione tramite LDAP/LDAPS.
* I backup per il dominio gestito non vengono più eseguiti.
* La sincronizzazione con Azure AD si interrompe.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Come è possibile sapere se il dominio gestito è sospeso?

Viene visualizzato un [avviso][resolve-alerts] nella pagina Integrità di Servizi di dominio Active Directory di Azure nel portale di Azure in cui viene noto che il dominio è stato sospeso. Lo stato del dominio mostra anche *Sospeso*.

### <a name="restore-a-suspended-domain"></a>Ripristinare un dominio sospeso

Per ripristinare l'integrità di un dominio gestito di Servizi di dominio Active Directory di Azure che si è nello stato *Sospeso,* completare i passaggi seguenti:To restore the health of an Azure AD DS managed domain that's in the Suspended state, complete the following steps:

1. Nel portale di Azure cercare e selezionare **Servizi di dominio**.
1. Scegliere il dominio gestito di Servizi di dominio Active Directory di Azure dall'elenco, ad esempio *aaddscontoso.com*, quindi selezionare **Integrità**.
1. Selezionare l'avviso, ad esempio *AADDS503* o *AADDS504*, a seconda della causa della sospensione.
1. Scegliere il collegamento di risoluzione fornito nell'avviso e seguire i passaggi per risolverlo.

Un dominio gestito può essere ripristinato solo alla data dell'ultimo backup. La data dell'ultimo backup viene visualizzata nella pagina **Integrità** del dominio gestito. Eventuali modifiche apportate dopo l'ultimo backup non vengono ripristinate. I backup per un dominio gestito vengono archiviati per un massimo di 30 giorni. I backup eseguiti prima dei 30 giorni vengono eliminati.

Dopo aver risolto gli avvisi quando il dominio gestito è nello stato *Sospeso,* [aprire una richiesta][azure-support] di supporto di Azure per tornare a uno stato integro. Se è presente un backup inferiore a 30 giorni, il supporto di Azure può ripristinare il dominio gestito.

## <a name="deleted-state"></a>Stato eliminato

Se un dominio gestito di Servizi di dominio Active Directory di Azure rimane nello stato *Sospeso* per 15 giorni, viene eliminato. Questo processo è irrecuperabile.

### <a name="what-to-expect"></a>Cosa aspettarsi

Quando un dominio gestito di Servizi di dominio Active Directory di Azure entra nello stato *Eliminato,* si verifica il comportamento seguente:When an Azure AD DS managed domain enters the Deleted state, the following behavior is seen:

* Vengono eliminati tutti i backup e le risorse del dominio gestito.
* Non è possibile ripristinare il dominio gestito ed è necessario creare un dominio gestito sostitutivo per riutilizzare Servizi di dominio Active Directory di Azure.You can't restore the managed domain, and need to create a replacement managed domain to reuse Azure AD DS.
* Dopo l'eliminazione, in relazione al dominio gestito non viene più emessa alcuna fattura.

## <a name="next-steps"></a>Passaggi successivi

Per mantenere integro il dominio gestito di Servizi di dominio Active Directory di Azure e ridurre al minimo il rischio che venga sospeso, vedere come [risolvere gli avvisi per il dominio gestito.][resolve-alerts]

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
