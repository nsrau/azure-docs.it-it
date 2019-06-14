---
title: Azure Active Directory Connect Health - Diagnosticare gli errori di sincronizzazione relativi agli attributi duplicati | Microsoft Docs
description: Questo documento descrive il processo di diagnostica degli errori di sincronizzazione di attributi duplicati e la potenziale correzione degli scenari con oggetti orfani direttamente dal portale di Azure.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbdeef7c591221756ad206bf2f3dd78ac3d26c4f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60349982"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnosticare e correggere gli errori di sincronizzazione di attributi duplicati

## <a name="overview"></a>Panoramica
Con un ulteriore miglioramento per l'evidenziazione degli errori di sincronizzazione, Azure Active Directory (Azure AD) Connect Health introduce una procedura di correzione in modalità self-service. Consente di risolvere gli errori di sincronizzazione degli attributi duplicati e di correggere gli oggetti resi orfani da Azure AD.
La funzionalità di diagnostica presenta i vantaggi seguenti:
- Offre una procedura di diagnostica che consente di limitare gli errori di sincronizzazione degli attributi duplicati. E fornisce correzioni specifiche.
- Applica una correzione per scenari dedicati da Azure AD per risolvere l'errore in un singolo passaggio.
- Per abilitare queste funzionalità, non sono richiesti aggiornamenti o configurazioni.
Per altre informazioni su Azure AD, vedere [Sincronizzazione delle identità e resilienza degli attributi duplicati](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>I problemi
### <a name="a-common-scenario"></a>Scenario comune
Quando si verificano gli errori di sincronizzazione **QuarantinedAttributeValueMustBeUnique** e **AttributeValueMustBeUnique**, è frequente che si sia verificato un conflitto di **UserPrincipalName** o **Proxy Addresses** in Azure AD. È possibile risolvere gli errori di sincronizzazione aggiornando l'oggetto di origine in conflitto dal lato locale. L'errore di sincronizzazione verrà risolto dopo la sincronizzazione successiva. Ad esempio, questa immagine indica che due utenti hanno un **UserPrincipalName** in conflitto. Sono entrambi **Joe.J\@contoso.com**. Gli oggetti in conflitto vengono messi in quarantena in Azure AD.

![Diagnosi di uno scenario comune per un errore di sincronizzazione](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenario con oggetto orfano
In alcuni casi è possibile che un utente esistente perda l'oggetto **Ancoraggio di origine**. L'eliminazione dell'oggetto di origine è avvenuta in Active Directory locale. Ma la modifica del segnale di eliminazione non è stata mai sincronizzata in Azure AD. Questa perdita avviene per motivi come problemi del motore di sincronizzazione o la migrazione di un dominio. Quando lo stesso oggetto viene ripristinato o ricreato, dal punto di vista logico l'utente esistente deve essere l'utente da sincronizzare **dall'ancoraggio di origine**. 

Quando un utente esistente è un oggetto solo cloud, è possibile anche vedere l'utente in conflitto sincronizzato in Azure AD. L'utente non può essere abbinato come sincronizzato con l'oggetto esistente. Non esiste un modo diretto per eseguire di nuovo il mapping **dell'ancoraggio** di origine. Per altre informazioni, vedere la [Knowledge Base esistente](https://support.microsoft.com/help/2647098). 

Ad esempio, l'oggetto esistente in Azure AD mantiene la licenza di Luca. È ora presente un oggetto appena sincronizzato con **ancoraggio di origine** diverso nello stato dell'attributo duplicato in Azure AD. Le modifiche relative a Luca in Active Directory locale non verranno applicate all'utente di origine (oggetto esistente) di Luca in Azure AD.  

![Diagnosi di uno scenario con oggetto orfano per un errore di sincronizzazione](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Procedura di diagnostica e risoluzione dei problemi in Connect Health 
La funzionalità di diagnostica supporta oggetti utente con gli attributi duplicati seguenti:

| Nome attributo | Tipi di errore di sincronizzazione|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique o AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique o AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Per accedere a questa funzionalità, è necessaria l'autorizzazione **Amministratore globale**  o **Collaboratore** nelle impostazioni di controllo degli accessi in base al ruolo.
>

Seguire la procedura dal portale di Azure per limitare i dettagli degli errori di sincronizzazione e fornire soluzioni più specifiche:

![Procedura per la diagnosi di errori di sincronizzazione](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Dal portale di Azure sono sufficienti pochi passaggi per identificare scenari risolvibili specifici:  
1.  Controllare la colonna **Diagnose status** (Stato diagnosi). Lo stato indica se esiste un modo per correggere un errore di sincronizzazione direttamente da Azure Active Directory. In altre parole, esiste un flusso di risoluzione dei problemi che può identificare il caso di errore e potenzialmente risolverlo.

| Stato | Da cosa dipende il problema? |
| ------------------ | -----------------|
| Non avviata | Questo processo di diagnosi non è stato avviato. A seconda del risultato della diagnosi; esiste potenzialmente un modo per correggere l'errore di sincronizzazione direttamente dal portale. |
| Correzione manuale necessaria | L'errore non rientra nei criteri delle correzioni disponibili dal portale. È possibile che i tipi di oggetto in conflitto non siano utenti oppure la procedura diagnostica è stata eseguita e nessuna risoluzione è disponibile dal portale. Nel secondo caso, una correzione dal lato locale è ancora una delle soluzioni. [Altre informazioni sulle correzioni in locale](https://support.microsoft.com/help/2647098). | 
| In attesa di sincronizzazione | È stata applicata una correzione. Il portale è in attesa del successivo ciclo di sincronizzazione per cancellare l'errore. |

  >[!IMPORTANT]
  > La colonna di stato della diagnostica verrà reimpostata dopo ogni ciclo di sincronizzazione. 
  >

1. Selezionare il pulsante **Esegui diagnosi** sotto i dettagli dell'errore. Verrà richiesto di rispondere ad alcune domande per identificare i dettagli dell'errore di sincronizzazione. Le risposte alle domande sono utili per identificare un caso di oggetto orfano.

1. La presenza di un pulsante **Chiudi** al termine della diagnostica indica che non è disponibile una correzione rapida dal portale in base alle risposte fornite. Fare riferimento alla soluzione illustrata nell'ultimo passaggio. Le correzioni in locale sono ancora le soluzioni. Selezionare il pulsante **Chiudi**. Lo stato dell'errore di sincronizzazione corrente diventa **Correzione manuale necessaria**. Lo stato rimane durante il ciclo di sincronizzazione corrente.

1. Dopo avere identificato un caso di oggetto orfano, è possibile correggere gli errori di sincronizzazione relativi agli attributi duplicati direttamente dal portale. Per attivare il processo, selezionare il pulsante **Applica correzione**. Lo stato dell'errore di sincronizzazione corrente diventa **In attesa di sincronizzazione**.

1. Dopo il ciclo di sincronizzazione successivo l'errore dovrebbe essere rimosso dall'elenco.

## <a name="how-to-answer-the-diagnosis-questions"></a>Come rispondere alle domande di diagnosi 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>L'utente esiste in Active Directory locale?

Questa domanda tenta di identificare l'oggetto di origine dell'utente esistente da Active Directory locale.  
1. Verificare se Azure Active Directory include un oggetto con il valore **UserPrincipalName** specificato. In caso contrario, rispondere **No**.
2. In caso affermativo, verificare se l'oggetto è ancora incluso nell'ambito per la sincronizzazione.  
   - Eseguire una ricerca nello spazio connettore di Azure AD con il nome di dominio.
   - Se l'oggetto viene trovato con lo stato **Aggiunta in sospeso**, rispondere **No**. Azure AD Connect non è in grado di connettere l'oggetto all'oggetto di Azure AD corretto.
   - Se l'oggetto non viene trovato, rispondere **Sì**.

In questi esempi, la domanda tenta di identificare se **Luca Milano** esiste ancora in Active Directory locale.
Per lo **scenario comune**, sia **Luca Milanesi** che **Luca Milano** sono presenti in Active Directory locale. Gli oggetti in quarantena sono due utenti diversi.

![Diagnosi di uno scenario comune per un errore di sincronizzazione](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Per lo **scenario con oggetto orfano**, solo l'utente singolo **Luca Milanesi** è presente in Active Directory locale:

![Diagnosi di uno scenario *utente esistente* con oggetto orfano per un errore di sincronizzazione](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Entrambi gli account appartengono allo stesso utente?
La domanda mira a stabilire se l'utente in conflitto in ingresso e l'oggetto utente esistente in Azure AD appartengono allo stesso utente.  
1. L'oggetto in conflitto è stato appena sincronizzato in Azure Active Directory. Confrontare gli attributi degli oggetti:  
   - Nome visualizzato
   - Nome entità utente
   - ID oggetto
2. Se Azure AD non riesce a confrontarli, assicurarsi che Active Directory includa oggetti con i valori **UserPrincipalName** specificati. Rispondere **No** se vengono trovati entrambi.

Nell'esempio seguente, i due oggetti appartengono allo stesso utente **Luca Milanesi**.

![Diagnosi di uno scenario *stesso utente* con oggetto orfano per un errore di sincronizzazione](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Cosa accade dopo che è stata applicata la correzione per lo scenario con oggetto orfano
In base alle risposte alle domande precedenti, verrà visualizzato il pulsante **Applica correzione** quando è disponibile una correzione da Azure AD. In questo caso, l'oggetto locale viene sincronizzato con un oggetto Azure AD imprevisto. I due oggetti vengono mappati usando **Ancoraggio di origine**. La modifica **Applica correzione** esegue questi passaggi o passaggi simili:
1. Aggiorna il valore **Ancoraggio di origine** all'oggetto corretto in Azure AD.
2. Elimina l'oggetto in conflitto in Azure AD, se presente.

![Diagnosticare un errore di sincronizzazione dopo la correzione](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> La modifica **Applica correzione** si applica solo ai casi con oggetto orfano.
>

Dopo i passaggi precedenti, l'utente può accedere alla risorsa originale, ovvero un collegamento a un oggetto esistente. Il valore **Diagnose status** (Stato diagnosi) nella visualizzazione elenco diventa **In attesa di sincronizzazione**. L'errore di sincronizzazione verrà risolto dopo la sincronizzazione successiva. Connect Health non mostrerà più l'errore di sincronizzazione risolto nella visualizzazione elenco.

## <a name="failures-and-error-messages"></a>Errori e messaggi di errore
**L'utente con attributo in conflitto viene eliminato temporaneamente in Azure Active Directory. Assicurarsi che l'utente venga eliminato definitivamente prima di riprovare.**  
L'utente con attributo in conflitto in Azure AD deve essere rimosso prima di applicare la correzione. Vedere [come eliminare in modo permanente l'utente in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) prima di riprovare ad applicare la correzione. L'utente verrà anche eliminato automaticamente in modo permanente dopo 30 giorni in cui si trova nello stato di eliminazione temporanea. 

**L'aggiornamento dell'ancoraggio di origine in un utente basato sul cloud nel tenant non è supportato.**  
L'utente basato su cloud in Azure AD non deve avere l'ancoraggio di origine. In questo caso l'aggiornamento dell'ancoraggio di origine non è supportato. La correzione manuale è necessaria in locale. 

## <a name="faq"></a>Domande frequenti
**D.** Cosa accade se l'esecuzione di **Applica correzione** non riesce?  
**R.** Se l'esecuzione non riesce, è possibile che in Azure AD Connect si stia verificando un errore di esportazione. Aggiornare la pagina del portale e riprovare dopo la sincronizzazione successiva. Il ciclo di sincronizzazione predefinito è 30 minuti. 


**D.** Cosa accade se l'**oggetto esistente** deve essere l'oggetto da eliminare?  
**R.** Se l'**oggetto esistente** deve essere eliminato, il processo non comporta la modifica di **Ancoraggio di origine**. In genere, è possibile risolvere questo problema da Active Directory locale. 


**D.** Quale autorizzazione è necessaria per applicare la correzione?  
**R.** **Amministratore globale** o **Collaboratore** nelle impostazioni di controllo degli accessi in base al ruolo hanno l'autorizzazione per accedere al processo di diagnostica e risoluzione dei problemi.


**D.** È necessario configurare Azure AD Connect o aggiornare l'agente di Azure AD Connect Health per questa funzionalità?  
**R.** No, il processo di diagnosi è completamente basato sul cloud.


**D.** Se l'oggetto esistente viene eliminato temporaneamente, il processo di diagnosi renderà di nuovo attivo l'oggetto?  
**R.** No, la correzione non aggiornerà gli attributi dell'oggetto, se non **Ancoraggio di origine**.
