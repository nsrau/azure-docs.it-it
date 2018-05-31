---
title: Azure Active Directory Connect Health - Diagnosticare gli errori di sincronizzazione relativi agli attributi duplicati | Microsoft Docs
description: Questo documento descrive la procedura di diagnostica degli errori di sincronizzazione relativi agli attributi duplicati e la potenziale correzione degli scenari con oggetti orfani direttamente dal portale di Azure.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305598"
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>Diagnosi e correzione degli errori di sincronizzazione relativi agli attributi duplicati 

## <a name="overview"></a>Panoramica
Con un ulteriore passo in avanti nell'individuazione degli errori di sincronizzazione, Azure Active Directory Connect Health sta introducendo un'esperienza di correzione self-service per la risoluzione degli errori di sincronizzazione relativi agli attributi duplicati e la correzione degli oggetti orfani da Azure AD. I vantaggi principali delle funzionalità di diagnosi sono:
- Fornire una procedura di diagnosi per limitare gli scenari di errore di sincronizzazione relativi agli attributi duplicati e indicare risoluzioni specifiche.
- Applicare la correzione per scenari dedicati da Azure AD per risolvere l'errore con un singolo clic.
- Per abilitare queste funzionalità, non sono richiesti aggiornamenti o configurazioni.
Informazioni dettagliate sulla [resilienza duplicata](https://aka.ms/dupattributeresdocs) in Azure AD.

## <a name="problems"></a>I problemi
### <a name="common-scenario"></a>Scenario comune
Quando si verificano gli errori di sincronizzazione **QuarantinedAttributeValueMustBeUnique** e **AttributeValueMustBeUnique**, è frequente che si sia verificato un conflitto del nome dell'entità utente o degli indirizzi proxy in Azure AD. È possibile risolvere gli errori di sincronizzazione aggiornando l'oggetto di origine in conflitto dal lato locale. L'errore di sincronizzazione verrà risolto dopo la sincronizzazione seguente. Ad esempio, l'immagine seguente indica che tra due utenti si è verificato un conflitto relativo allo UserPrincipalName *Joe.J@contoso.com*. Gli oggetti in conflitto vengono messi in quarantena in Azure AD. 

![Diagnosi di uno scenario comune per un errore di sincronizzazione](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenario con oggetto orfano
In alcuni casi è possibile che un utente esistente perda l'ancoraggio di origine. L'oggetto di origine è stato eliminato in Active Directory locale, ma il segnale di modifica dell'eliminazione non è mai stato sincronizzato con Azure AD. Questa condizione può verificarsi ad esempio per un problema del motore di sincronizzazione o per la migrazione del dominio. Quando lo stesso oggetto viene ripristinato o ricreato, dal punto di vista logico l'utente esistente deve essere l'utente da sincronizzare dall'ancoraggio di origine. Per un utente esistente come oggetto solo cloud, è possibile anche che un utente in conflitto venga sincronizzato con Azure AD e non può essere abbinato nella sincronizzazione con l'oggetto esistente. Non esiste un modo diretto per eseguire di nuovo il mapping dell'ancoraggio di origine. Per altre informazioni, vedere le risorse della [Knowledge Base esistenti](https://support.microsoft.com/help/2647098). Ad esempio, l'oggetto esistente in Azure AD mantiene la licenza di Joe. È ora presente un oggetto appena sincronizzato con ancoraggio di origine diverso nello stato dell'attributo duplicato in Azure AD. Le modifiche relative a Joe in Active Directory locale non verranno applicate all'utente di origine (oggetto esistente) di Joe in Azure AD.  

![Diagnosi di uno scenario con oggetto orfano per un errore di sincronizzazione](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Procedura di diagnostica e risoluzione dei problemi in Connect Health 
La funzionalità di diagnostica supporta gli oggetti utente con gli attributi duplicati seguenti:

| Nome attributo | Tipi di errore di sincronizzazione|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique o AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique o AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Per poter accedere a questa funzionalità, è necessario che sia stata impostata l'autorizzazione **Amministratore globale**  o **Collaboratore** nel controllo degli accessi in base al ruolo. 
>

Seguendo i passaggi dal portale di Azure sarà possibile limitare i dettagli degli errori di sincronizzazione e fornire soluzioni più specifiche:

![Procedura di diagnosi per gli errori di sincronizzazione](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Dal portale Azure sarà possibile eseguire alcuni passaggi per identificare scenari risolvibili specifici:  
1.  Nella colonna di stato della diagnostica verrà indicato se esistono flussi di risoluzione dei problemi per limitare il caso di errore e una potenziale correzione direttamente da Azure Active Directory.
| Status | Da cosa dipende il problema? |
| ------------------ | -----------------|
| Non avviata | Questa procedura di diagnosi non è stata avviata. Dipende dal risultato della diagnosi; esiste potenzialmente un modo per correggere l'errore di sincronizzazione direttamente dal portale. |
| Correzione manuale necessaria | L'errore non rientra nei criteri della correzione disponibile dal portale. Il caso può essere: (1) I tipi di oggetto in conflitto non sono utenti (2) La procedura diagnostica è stata eseguita e nessuna risoluzione è disponibile dal portale. In questo caso, una delle soluzioni è eseguire la correzione in locale. [Altre informazioni sulla correzione in locale](https://support.microsoft.com/help/2647098) | 
| In attesa di sincronizzazione | La correzione è stata applicata. Per cancellare l'errore, attendere il successivo ciclo di sincronizzazione. |
>[!IMPORTANT]
> La colonna di stato della diagnostica verrà reimpostata dopo ogni ciclo di sincronizzazione. 
>

2.  Se si fa clic sul pulsante **Esegui diagnosi** nel pannello dei dettagli dell'errore, sarà necessario rispondere ad alcune domande e identificare i dettagli dell'errore di sincronizzazione. Rispondere alle domande consentirà di identificare il caso dello scenario con oggetto orfano. 

3.  L'eventuale presenza di un pulsante **Chiudi** al termine della diagnostica implica che non è disponibile una correzione rapida dal portale in base alle risposte fornite. Fare riferimento alla soluzione illustrata nell'ultimo passaggio. È comunque possibile eseguire la correzione in locale. Dopo avere fatto clic sul pulsante di chiusura, lo stato dell'errore di sincronizzazione corrente diventerà **Correzione manuale necessaria**. Lo stato verrà mantenuto durante il ciclo di sincronizzazione corrente.

4.  Dopo avere identificato il caso dell'oggetto orfano, sarà possibile correggere gli errori di sincronizzazione relativi agli attributi duplicati direttamente dal portale. Fare clic sul pulsante **Applica correzione** per attivare il processo. Lo stato dell'errore di sincronizzazione corrente diventerà **In attesa di sincronizzazione**.

5.  Dopo il ciclo di sincronizzazione seguente l'errore dovrebbe essere rimosso dall'elenco.

## <a name="how-to-answer-the-diagnosis-questions"></a>Come rispondere alle domande di diagnosi 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>L'utente esiste in Active Directory locale?

La domanda tenta di identificare l'oggetto di origine di un utente esistente in Active Directory locale.  
1.  Verificare se Active Directory include un oggetto con il valore UserPrincipalName specificato. In caso contrario, rispondere No.
2.  Se si seleziona Sì, verificare se l'oggetto è ancora incluso nell'ambito per la sincronizzazione.  
  - Eseguire una ricerca nello spazio connettore di Azure AD con il nome di dominio.
  - Se l'oggetto viene trovato con lo stato **Aggiunta in sospeso**, rispondere No. Azure AD Connect non è in grado di connettere l'oggetto all'oggetto di AD corretto.
  - Se l'oggetto non viene trovato, rispondere Sì.

> Nel diagramma seguente, ad esempio, la domanda mira a stabilire se *Joe Jackson* esiste ancora in Active Directory locale.
Per lo **scenario comune**, sia *Joe Johnson* che *Joe Jackson* saranno presenti in Active Directory locale. Gli oggetti in quarantena sono due utenti diversi.

![Diagnosi di uno scenario comune per un errore di sincronizzazione](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> Per lo **scenario con oggetto orfano**, solo un utente, *Joe Johnson*, sarà presente in Active Directory locale:

![Diagnosi di uno scenario con oggetto orfano per un errore di sincronizzazione](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>Entrambi gli account appartengono allo stesso utente?
La domanda mira a stabilire se l'utente in conflitto in ingresso e l'oggetto utente esistente in Azure AD appartengono allo stesso utente.  
1.  L'oggetto in conflitto è stato appena sincronizzato in Azure Active Directory. Confrontare l'oggetto in base a:  
  - Nome visualizzato
  - Nome dell'entità utente
  - ID oggetto
2.  Se non è stato possibile confrontarli, assicurarsi che Active Directory includa oggetti con i valori UserPrincipalName specificati. Rispondere NO se vengono trovati entrambi.  

> Nel caso riportato di seguito, i due oggetti appartengono allo stesso utente *Joe Johnson*.

![Diagnosi di uno scenario con oggetto orfano per un errore di sincronizzazione](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>Cosa accade dopo che è stata applicata la correzione per lo scenario con oggetto orfano?
In base delle risposte alle domande è possibile che sia presente il pulsante **Applica correzione**, se è disponibile una correzione da Azure AD. In questo caso, l'oggetto locale è in fase di sincronizzazione con un oggetto Azure AD imprevisto. I due oggetti vengono mappati usando l'"ancoraggio di origine". L'applicazione delle modifica implicherà passaggi come:
- Aggiornare l'ancoraggio di origine all'oggetto corretto in Azure AD.
- Eliminare l'oggetto in conflitto in Azure AD, se presente.

![Diagnosi di uno scenario per un errore di sincronizzazione dopo la correzione](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> La modifica di applicazione della correzione verrà applicata solo ai casi con oggetto orfano.
>

Dopo la procedura precedente, l'utente potrà accedere alla risorsa di origine, che è un collegamento all'oggetto esistente. Il valore dello **stato di diagnostica** nella visualizzazione elenco diventerà **In attesa di sincronizzazione**. L'errore di sincronizzazione verrà risolto dopo la sincronizzazione seguente. Connect Health non visualizzerà più l'errore di sincronizzazione risolto nella visualizzazione elenco. 


## <a name="faq"></a>Domande frequenti
 -  Cosa accade se l'esecuzione dell'applicazione non riesce?  
Se l'esecuzione non riesce, è possibile che in Azure AD Connect si stia verificando un errore di esportazione al momento. Aggiornare la pagina del portale e riprovare dopo la sincronizzazione seguente. Il ciclo di sincronizzazione predefinito dura 30 minuti. 

 -  Cosa accade se l'**oggetto esistente** deve essere l'oggetto da eliminare?  
Se l'oggetto esistente deve essere eliminato in questo caso, il processo non comporta la modifica dell'ancoraggio di origine. Dovrebbe essere possibile correggerlo da Active Directory locale.  

 -  Quale autorizzazione consente all'utente di applicare la correzione?  
L'Amministratore globale o il Collaboratore nelle impostazioni di controllo degli accessi in base al ruolo hanno l'autorizzazione per accedere al processo di diagnostica e risoluzione dei problemi.

 -  È necessario configurare AAD Connect o aggiornare l'agente di Azure AD Connect Health per questa funzionalità?  
No, la funzionalità di diagnosi è completamente basata su cloud.

 -  Se l'oggetto esistente viene eliminato temporaneamente, la procedura di diagnosi ripristinerà l'oggetto in modo che sia di nuovo attivo?  
No, la correzione non aggiornerà attributi dell'oggetto diversi dall'ancoraggio di origine. 
