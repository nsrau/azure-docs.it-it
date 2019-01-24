---
title: Pianificare i criteri di accesso condizionale in Azure Active Directory | Microsoft Docs
description: Questo articolo descrive come pianificare i criteri di accesso condizionale per Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
tags: azuread
ms.service: active-directory
ms.component: conditional-access
ms.topic: conceptual
ms.workload: identity
ms.date: 12/13/2018
ms.author: markvi
ms.reviewer: martincoetzer
ms.openlocfilehash: 1911dd189e21a6d29b2bf1ba3d179b41e948f469
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450508"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Procedura: Pianificare la distribuzione dell'accesso condizionale in Azure Active Directory

Pianificare la distribuzione dell'accesso condizionale è fondamentale per assicurarsi che la strategia di accesso per le app e le risorse all'interno dell'organizzazione sia quella desiderata. Durante la fase di pianificazione della distribuzione, è consigliabile dedicare la maggior parte del tempo a progettare i vari criteri necessari per concedere o bloccare l'accesso agli utenti in base alle condizioni scelte. Questo documento illustra i passaggi da eseguire per implementare criteri di accesso condizionale sicuri ed efficaci. Prima di iniziare, assicurarsi di comprendere il funzionamento dell'[accesso condizionale](overview.md) e quando usarlo.


## <a name="what-you-should-know"></a>Informazioni utili

Più che a una funzionalità autonoma, l'accesso condizionale può essere paragonato a un framework che consente di controllare l'accesso alle app e alle risorse dell'organizzazione. Di conseguenza, alcune impostazioni di accesso condizionale richiedono la configurazione di funzionalità aggiuntive. Ad esempio, è possibile configurare un criterio che risponda a un determinato [livello di rischio di accesso](../identity-protection/howto-sign-in-risk-policy.md#what-is-the-sign-in-risk-policy). Tuttavia, un criterio basato sul livello di rischio di accesso richiede l'abilitazione di [Azure Active Directory Identity Protection](../identity-protection/overview.md).

Se sono richieste funzionalità aggiuntive, può anche essere necessario ottenere le licenze correlate. Ad esempio, mentre l'accesso condizionale è una funzionalità di Azure AD Premium P1, la protezione delle identità richiede una licenza di Azure AD Premium P2.

Esistono due tipi di criteri di accesso condizionale: di base e standard. I [criteri di base](baseline-protection.md) sono criteri di accesso condizionale predefiniti. L'obiettivo di questi criteri è assicurarsi di disporre almeno del livello di base della sicurezza abilitato. Criteri di base. I criteri di base sono disponibili in tutte le edizioni di Azure AD e forniscono solo opzioni di personalizzazione limitate. Se uno scenario richiede una maggiore flessibilità, disabilitare i criteri di base e implementare i requisiti in criteri standard personalizzati.

Nei criteri di accesso condizionale standard è possibile personalizzare tutte le impostazioni in modo da soddisfare i requisiti aziendali. I criteri standard richiedono una licenza di Azure AD Premium P1.




## <a name="draft-policies"></a>Disegnare i criteri

L'accesso condizionale di Azure Active Directory consente di portare la protezione delle app cloud a un nuovo livello. In questo nuovo livello, le modalità di accesso a un'app cloud si basano su una valutazione dei criteri dinamica anziché su una configurazione di accesso statica. Nei criteri di accesso condizionale si definisce una risposta (**fare questo**) a una condizione di accesso (**quando accade questo**).

![Motivo e risposta](./media/plan-conditional-access/10.png)

Definire i criteri di accesso condizionale da implementare usando questo modello di pianificazione. L'esercizio di pianificazione:

- Aiuta a delineare le risposte e le condizioni per ogni criterio.
- Consente di creare un catalogo dei criteri di accesso condizionale ben documentato per l'organizzazione. 

È possibile usare il catalogo per valutare se l'implementazione dei criteri riflette i requisiti aziendali dell'organizzazione. 

Per creare i criteri di accesso condizionale per l'organizzazione, usare il modello di esempio seguente:

|Quando accade *questo*:|Fare *questo*:|
|-|-|
|Viene eseguito un tentativo di accesso:<br>A un'app cloud*<br>- Da utenti e gruppi*<br>Usando:<br>-Condizione 1 (ad esempio, all'esterno della rete aziendale)<br>-Condizione di 2 (ad esempio, rischio di accesso)|Bloccare l'accesso all'applicazione|
|Viene eseguito un tentativo di accesso:<br>A un'app cloud*<br>- Da utenti e gruppi*<br>Usando:<br>-Condizione 1 (ad esempio, all'esterno della rete aziendale)<br>-Condizione di 2 (ad esempio, rischio di accesso)|Concedere l'accesso con (E):<br>-Requisito 1 (ad esempio, MFA)<br>-Requisito 2 (ad esempio, conformità del dispositivo)|
|Viene eseguito un tentativo di accesso:<br>A un'app cloud*<br>- Da utenti e gruppi*<br>Usando:<br>-Condizione 1 (ad esempio, all'esterno della rete aziendale)<br>-Condizione di 2 (ad esempio, rischio di accesso)|Concedere l'accesso con (O):<br>-Requisito 1 (ad esempio, MFA)<br>-Requisito 2 (ad esempio, conformità del dispositivo)|

Come minimo, **quando accade questo** definisce l'entità di sicurezza (**chi**) che tenta di accedere a un'app cloud (**cosa**). Se necessario, è anche possibile includere **come** viene eseguito un tentativo di accesso. Nell'accesso condizionale, gli elementi che definiscono chi, cosa e come sono noti come condizioni. Per altre informazioni, vedere [Quali sono le condizioni dell'accesso condizionale di Azure Active Directory?](conditions.md) 

Con **fare questo**, si definisce la risposta dei criteri a una condizione di accesso. Nella risposta, si blocca o si concede l'accesso con requisiti aggiuntivi, ad esempio, l'autenticazione a più fattori (MFA). Per una panoramica completa, vedere [Quali sono i controlli di accesso nell'accesso condizionale di Azure Active Directory?](controls.md)  
 

La combinazione delle condizioni con i controlli di accesso rappresenta un tipo di criteri di accesso condizionale.

![Motivo e risposta](./media/plan-conditional-access/51.png)


Per altre informazioni, vedere [Elementi necessari per il funzionamento di un criterio](best-practices.md#whats-required-to-make-a-policy-work).

A questo punto si può stabilire uno standard di denominazione per i criteri. Lo standard di denominazione aiuta a individuare i criteri e comprenderne le finalità senza aprirli nel portale di amministrazione di Azure. È consigliabile assegnare ai criteri un nome che mostri:

- Numero di sequenza
- Cloud a cui si applica
- Risposta
- A chi si applica
- Quando si applica 
 
![Standard di denominazione](./media/plan-conditional-access/11.png)





Ad esempio, il nome seguente indica che il criterio richiede l'autenticazione a più fattori per gli utenti di marketing su reti esterne che usano l'app Dynamics CRP:

`CA01-Dynamics CRP: Require MFA For marketing When on external networks`


Oltre ai criteri attivi, è opportuno implementare anche criteri disabilitati che agiscano come [controlli di accesso resilienti secondari in scenari di emergenza/interruzione dei servizi](../authentication/concept-resilient-controls.md). Lo standard di denominazione dovrebbe includere anche questo scopo, per renderne più semplice l'abilitazione durante un'interruzione dei servizi. Ad esempio: 

`EM01-Finance app: Require MFA For Sales When on untrusted network`


## <a name="plan-policies"></a>Pianificare i criteri

Quando si pianifica una soluzione di criteri di accesso condizionale, valutare se è necessario creare criteri per ottenere i risultati seguenti. 


### <a name="block-access"></a>Blocca accesso

L'opzione per bloccare l'accesso è notevolmente efficace perché:

- È prioritaria rispetto a tutte le altre assegnazioni per un utente

- Consente di bloccare l'accesso al tenant da parte dell'intera organizzazione
 
Se si vuole bloccare l'accesso per tutti gli utenti, è necessario escludere dai criteri almeno un utente (in genere gli account di accesso di emergenza). Per altre informazioni, vedere come [selezionare utenti e gruppi](block-legacy-authentication.md#select-users-and-cloud-apps).  
    

### <a name="require-mfa"></a>Richiedere l'autenticazione MFA

Per semplificare l'esperienza di accesso degli utenti, è possibile consentire di accedere alle app cloud usando un nome utente e una password. Tuttavia, in genere esistono almeno alcuni scenari per cui è consigliabile richiedere una forma di verifica degli account più avanzata. Con i criteri di accesso condizionale, è possibile limitare il requisito relativo all'autenticazione a più fattori a determinati scenari. 

I casi d'uso comuni per richiedere l'autenticazione MFA riguardano l'accesso:

- [Da parte degli amministratori](baseline-protection.md#require-mfa-for-admins)
- [A specifiche app](app-based-mfa.md) 
- [Da percorsi di rete che non si considerano attendibili](untrusted-networks.md).


### <a name="respond-to-potentially-compromised-accounts"></a>Rispondere ad account potenzialmente compromessi

Con i criteri di accesso condizionale, è possibile implementare risposte automatiche agli accessi da identità potenzialmente compromesse. La probabilità che un account sia stato compromesso viene espressa sotto forma di livelli di rischio. Identity Protection calcola due livelli di rischio: rischio di accesso e rischio utente. Per implementare una risposta a un rischio di accesso sono disponibili due opzioni:

- [La condizione di rischio di accesso](conditions.md#sign-in-risk) nei criteri di accesso condizionale
- [I criteri di rischio di accesso](../identity-protection/howto-sign-in-risk-policy.md) in Identity Protection 

Usare il rischio di accesso come condizione è il metodo consigliato perché offre più opzioni di personalizzazione.

Il livello di rischio utente è disponibile solo sotto forma di [criteri di rischio utente](../identity-protection/howto-user-risk-policy.md) di Identity Protection. 

Per altre informazioni, vedere [Cos'è Azure Active Directory Identity Protection?](../identity-protection/overview.md) 


### <a name="require-managed-devices"></a>Richiedere dispositivi gestiti

L'ampia diffusione dei dispositivi supportati per l'accesso alle risorse cloud offre notevoli vantaggi in termini di produttività degli utenti. In alcuni casi, tuttavia, può essere necessario evitare che alcune risorse dell'ambiente risultino accessibili a dispositivi con un livello di protezione sconosciuto. Per le risorse di questo tipo è opportuno definire un requisito di accessibilità in modo che gli utenti possano accedervi solo tramite un dispositivo gestito. Per altre informazioni, vedere la procedura [Richiedere dispositivi gestiti per l'accesso alle app cloud con l'accesso condizionale](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Richiedere app client approvate

Una delle prime decisioni da prendere per gli scenari BYOD (Bring Your Own Device) è se gestire l'intero dispositivo o solo i dati al suo interno. I dipendenti usano dispositivi mobili sia per le attività personali che per quelle aziendali. È importante assicurarsi che i dipendenti siano produttivi e al contempo evitare la perdita di dati. Con l'accesso condizionale di Azure Active Directory (Azure AD) è possibile fare in modo che l'accesso alle app cloud sia limitato solo alle app client approvate in grado di proteggere i dati aziendali. Per altre informazioni, vedere [Richiedere app client approvate per l'accesso alle app cloud con l'accesso condizionale](app-based-conditional-access.md).


### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy

Azure AD supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. Come si può impedire alle app che usano l'autenticazione legacy di accedere alle risorse dei tenant? Si consiglia di bloccarle con un criterio di accesso condizionale. Se necessario, è possibile autorizzare solo determinati utenti e percorsi di rete specifici per l’uso delle app basate sull’autenticazione legacy. Per altre informazioni, vedere la procedura [Bloccare l'autenticazione legacy per Azure AD con l'accesso condizionale](block-legacy-authentication.md).


## <a name="test-your-policy"></a>Verificare i criteri

Prima di implementare i criteri nell'ambiente di produzione, è consigliabile testarli per verificare che tutto funzioni come previsto.

1. Creare utenti di test

2. Creare un piano di test

3. Configurare i criteri

4. Valutare un accesso simulato

5. Verificare i criteri

6. Pulizia



### <a name="create-test-users"></a>Creare utenti di test

Per testare un criterio, creare un set di utenti simili agli utenti nell'ambiente in uso. La creazione di utenti di test consente di verificare che i criteri funzionino come previsto di applicarli agli utenti reali e potenzialmente compromettere il loro accesso alle app e alle risorse. 


Alcune organizzazioni hanno tenant di test a questo scopo. Tuttavia, può essere difficile ricreare tutte le condizioni e le app in un tenant di test per verificare completamente i risultati di un criterio. 

### <a name="create-a-test-plan"></a>Creare un piano di test

Il piano di test è importante per disporre di un confronto tra i risultati previsti e i risultati effettivi. È sempre necessario definire il risultato atteso prima di eseguire un test. Nella tabella seguente vengono descritti alcuni test case di esempio. Modificare gli scenari e i risultati previsti in base al modo in cui sono configurati i criteri di accesso condizionale.

|Policy |Scenario |Risultato previsto | Risultato |
|---|---|---|---|
|[Richiedi autenticazione a più fattori quando non al lavoro](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Un utente autorizzato accede ad *App* da un percorso attendibile/dal posto di lavoro|All'utente non viene richiesta l'autenticazione a più fattori| |
|[Richiedi autenticazione a più fattori quando non al lavoro](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Un utente autorizzato accede ad *App* da un percorso non attendibile/non dal posto di lavoro|All'utente viene richiesta l'autenticazione a più fattori e può eseguire correttamente l'accesso| |
|[Richiedi autenticazione a più fattori (per l'amministratore)](https://docs.microsoft.com/azure/active-directory/conditional-access/baseline-protection#require-mfa-for-admins)|L'amministratore globale accede ad *App*|All'amministratore viene richiesta l'autenticazione a più fattori| |
|[Accessi a rischio](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|L'utente accede ad *App* usando un [browser Tor](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|All'amministratore viene richiesta l'autenticazione a più fattori| |
|[Gestione del dispositivo](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Un utente autorizzato cerca di accedere da un dispositivo autorizzato|Accesso concesso| |
|[Gestione del dispositivo](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Un utente autorizzato cerca di accedere da un dispositivo non autorizzato|Accesso bloccato| |
|[Modifica password per gli utenti a rischio](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Un utente autorizzato cerca di accedere con credenziali compromesse (accesso ad alto rischio)|All'utente viene richiesto di cambiare la password o l'accesso viene bloccato in base al criterio| |


### <a name="configure-the-policy"></a>Configurare i criteri

La gestione dei criteri di accesso condizionale è un'attività manuale. Nel portale di Azure è possibile gestire i criteri di accesso condizionale in un'unica posizione centrale, la pagina Accesso condizionale. Un punto di ingresso alla pagina Accesso condizionale è la sezione **Sicurezza** sezione nel riquadro di spostamento **Active Directory**. 

![Accesso condizionale](media/plan-conditional-access/03.png)


Per altre informazioni su come creare i criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md). Questo argomento di avvio rapido consente di:

- Acquisire familiarità con l'interfaccia utente.
- Ottenere una panoramica del funzionamento dell'accesso condizionale. 


### <a name="evaluate-a-simulated-sign-in"></a>Valutare un accesso simulato

Ora che sono stati configurati i criteri di accesso condizionale, è possibile sapere se funzionano come previsto. Come primo passaggio, usare lo [strumento per i criteri What If](what-if-tool.md) dell'accesso condizionale per simulare un accesso dell'utente di test. La simulazione valuta l'impatto di questo accesso sui criteri e genera un report di simulazione.

>[!NOTE]
> Anche se un'esecuzione simulata offre una prima impressione dell'impatto dei criteri di accesso condizionale, non sostituisce un'effettiva esecuzione dei test.


### <a name="test-your-policy"></a>Verificare i criteri

Eseguire i test case in base al piano di test. In questo passaggio viene eseguito un test completo di ogni criterio per gli utenti di test, per verificare che il loro comportamento sia quello previsto. Usare gli scenari creati in precedenza per eseguire ogni test.

È importante assicurarsi di testare i criteri di esclusione di un criterio. Ad esempio, è possibile escludere un utente o gruppo da un criterio che richiede l'autenticazione MFA. È pertanto necessario testare se agli utenti esclusi viene richiesta l'autenticazione MFA, perché la combinazione di altri criteri potrebbe richiedere l'autenticazione MFA per questi stessi utenti.


### <a name="cleanup"></a>Pulizia

La procedura di pulizia comprende i passaggi seguenti:

1. Disabilitare il criterio.

2. Rimuovere gli utenti e gruppi assegnati.

3. Eliminare gli utenti di test.  




## <a name="move-to-production"></a>Passare in produzione

Quando si è pronti a distribuire un nuovo criterio nel proprio ambiente, è consigliabile procedere per fasi:

- Dare comunicazione delle modifiche agli utenti finali.

- Iniziare con un set ridotto di utenti e verificare che il criterio abbia il comportamento previsto.

- Quando si espande un criterio per includere un maggior numero di utenti, continuare a escludere tutti gli amministratori. Escludere gli amministratori assicura che qualcuno possa comunque accedere a un criterio, se è necessaria una modifica.

- Applicare un criterio a tutti gli utenti solo se necessario.

Come procedura consigliata, creare almeno un account utente che sia:

- Dedicato all'amministrazione dei criteri

- Escluso da tutti i criteri

 



## <a name="rollback-steps"></a>Procedura di ripristino dello stato precedente

Nel caso in cui sia necessario eseguire il rollback dei criteri appena implementati, usare uno o più delle opzioni seguenti:

1. **Disabilitare il criterio** -La disabilitazione di un criterio garantisce che non venga applicato quando un utente tenta di accedere. È sempre possibile tornare indietro e abilitare il criterio quando si vorrà utilizzarlo.

    ![Disabilitare il criterio](media/plan-conditional-access/07.png)

2. **Escludere un utente o gruppo da un criterio** -Se un utente non è in grado di accedere all'app, è possibile scegliere di escluderlo dal criterio

    ![Escludere utenti](media/plan-conditional-access/08.png)

    >[!NOTE]
    > Questa opzione deve essere usata solo se necessario, solo in situazioni in cui l'utente è attendibile. L'utente deve essere nuovamente aggiunto al criterio o al gruppo appena possibile.

3. **Eliminare il criterio**: se il criterio non è più necessario, eliminarlo.

## <a name="next-steps"></a>Passaggi successivi

Consultare la [Documentazione sull'accesso condizionale di Azure AD](index.yml) per una panoramica delle informazioni disponibili.
