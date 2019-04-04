---
title: Procedure consigliate per l'accesso condizionale in Azure Active Directory | Microsoft Docs
description: Informazioni sugli aspetti da conoscere e su ciò che è consigliabile evitare quando si configurano i criteri di accesso condizionale.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d30fe326ef677ca4543534d57dd306ed2a660300
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895563"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Procedure consigliate per l'accesso condizionale in Azure Active Directory

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) è possibile controllare il modo in cui gli utenti autorizzati accedono alle app cloud. Questo articolo illustra quanto segue:

- Informazioni utili 
- Azioni da evitare nella configurazione dei criteri di accesso condizionale 

Questo articolo presuppone che l'utente abbia familiarità con i concetti e la terminologia descritti in [Cos'è l'accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).



## <a name="whats-required-to-make-a-policy-work"></a>Elementi necessari per il funzionamento di un criterio

Quando si crea un nuovo criterio, non sono presenti utenti, gruppi, app o controlli di accesso selezionati.

![App cloud](./media/best-practices/02.png)


Affinché il criterio funzioni, è necessario configurare quanto segue:


| Cosa           | Come                                  | Motivo |
| :--            | :--                                  | :-- |
| **App cloud** |Selezionare una o più app.  | L'obiettivo di un criterio di accesso condizionale è consentire di controllare il modo in cui gli utenti potranno accedere alle app cloud.|
| **Utenti e gruppi** | Selezionare almeno un utente o un gruppo autorizzato ad accedere alle app cloud selezionate. | Un criterio di accesso condizionale a cui non sono assegnati utenti e gruppi non viene mai attivato. |
| **Controlli di accesso** | Selezionare almeno un controllo di accesso. | L'elaboratore di criteri deve sapere quali operazioni eseguire se le condizioni vengono soddisfatte. |




## <a name="what-you-should-know"></a>Informazioni utili



### <a name="how-are-conditional-access-policies-applied"></a>Come vengono configurati i criteri di accesso condizionale

Quando si accede a un'app cloud, può essere applicato più di un criterio di accesso condizionale. In questo caso, devono essere soddisfatti tutti i criteri applicati. Se ad esempio un criterio richiede MFA e il secondo richiede un dispositivo conforme, è necessario eseguire l'autenticazione MFA e usare un dispositivo conforme. 

Tutti i criteri vengono applicati in due fasi:

- Nella **prima** fase vengono valutati tutti i criteri e vengono raccolti tutti i controlli di accesso non soddisfatti. 

- Nella **seconda** fase viene chiesto di soddisfare i requisiti non rispettati. Se uno dei criteri blocca l'accesso, si viene bloccati e non viene richiesto di soddisfare gli altri controlli dei criteri. Se non si viene bloccati da nessuno dei criteri, viene richiesto di soddisfare gli altri controlli dei criteri nell'ordine seguente:

    ![Ordine](./media/best-practices/06.png)
    
    Poi vengono i provider MFA e le condizioni per l'utilizzo.



### <a name="how-are-assignments-evaluated"></a>Come vengono valutate le assegnazioni?

Tutte le assegnazioni vengono collegate logicamente con l'operatore **AND**. Se sono configurate più assegnazioni, per attivare un criterio devono essere soddisfatte tutte.  

Se è necessario configurare una condizione relativa alla località applicata a tutte le connessioni stabilite dall'esterno della rete dell'organizzazione:

- Includere **Tutte le località**
- Escludere **Tutti gli indirizzi IP attendibili**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Quali operazioni è necessario eseguire se non si riesce ad accedere al portale di amministrazione di Azure AD?

Se non si riesce ad accedere al portale di Azure AD a causa di un'impostazione errata in un criterio di accesso condizionale:

- Controllare se siano presenti altri amministratori dell'organizzazione che non sono ancora stati bloccati. Un amministratore con accesso al portale di Azure può disabilitare il criterio che impedisce l'accesso. 

- Se nessun amministratore dell'organizzazione può aggiornare il criterio, è necessario inviare una richiesta di supporto. Il supporto tecnico Microsoft può esaminare e aggiornare i criteri di accesso condizionale che impediscono l'accesso.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Che cosa accade se sono configurati criteri nel portale di Azure classico e nel portale di Azure?  

Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Che cosa accade se sono presenti criteri nel portale di Intune Silverlight e nel portale di Azure?

Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Che cosa accade se sono configurati più criteri per lo stesso utente?  

Per ogni accesso, Azure Active Directory valuta tutti i criteri e verifica che tutti i requisiti vengano soddisfatti prima di concedere l'accesso all'utente. Il criterio di blocco dell'accesso è prioritario rispetto alle altre impostazioni di configurazione. 


### <a name="does-conditional-access-work-with-exchange-activesync"></a>L'accesso condizionale funziona con Exchange ActiveSync?

Sì, è possibile usare Exchange ActiveSync in criteri di accesso condizionale.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Come si deve configurare l'accesso condizionale con le app di Office 365?

Poiché le app di Office 365 sono interconnesse, è consigliabile assegnare comunemente usati insieme delle App durante la creazione di criteri.

Le applicazioni interconnesse comuni includono Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online e Office 365 Yammer.

È importante per i criteri che richiedono interazioni dell'utente, come l'autenticazione a più fattori, quando l'accesso viene controllato all'inizio di una sessione o attività. In caso contrario, gli utenti non potranno completare alcune attività all'interno di un'app. Ad esempio, se è necessaria l'autenticazione a più fattori nei dispositivi non gestiti per accedere a SharePoint ma non al messaggio di posta elettronica, gli utenti che utilizzano la posta elettronica sarà in grado di collegare i file di SharePoint a un messaggio. Altre informazioni sono reperibili nell'articolo [quali sono le dipendenze dei servizi nell'accesso condizionale di Azure Active Directory?](service-dependencies.md).





## <a name="what-you-should-avoid-doing"></a>Azioni da evitare

Il framework di accesso condizionale offre ottima flessibilità di configurazione. Con un'elevata flessibilità, tuttavia, è consigliabile esaminare attentamente ogni criterio di configurazione prima del rilascio per evitare risultati indesiderati. In questo contesto, è necessario prestare particolare attenzione alle assegnazioni che interessano set completi, ad esempio **tutti gli utenti/i gruppi/le applicazioni cloud**.

Nell'ambiente, è necessario evitare le seguenti configurazioni:


**Per tutti gli utenti, tutte le app cloud:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.

- **Richiedi un dispositivo conforme**: per gli utenti che non hanno ancora registrato i propri dispositivi, questo criterio blocca tutti gli accessi, incluso l'accesso al portale di Intune. Se l'utente è amministratore senza un dispositivo registrato, questo criterio blocca l'accesso al portale di Azure per la modifica dei criteri.

- **Require domain join** (Richiedi aggiunta a dominio): se ancora non si dispone di un dispositivo aggiunto al dominio, questo criterio di blocco dell'accesso è anche in grado di bloccare l'accesso per tutti gli utenti nell'organizzazione.


**Per tutti gli utenti, tutte le app cloud, tutte le piattaforme di dispositivo:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.


## <a name="how-should-you-deploy-a-new-policy"></a>Come distribuire un nuovo criterio

Come primo passaggio, è consigliabile valutare il criterio con lo [strumento per l'analisi di simulazione](what-if-tool.md).

Quando i nuovi criteri sono pronti per l'ambiente, distribuirli in più fasi:

1. Applicare un criterio a un set ridotto di utenti e verificare che abbia il comportamento previsto. 

2.  Quando si espande un criterio per includere altri utenti, continuare a escludere tutti gli amministratori dal criterio per assicurarsi che abbiano ancora accesso e possano aggiornare un criterio se è necessaria una modifica.

3. Applicare un criterio a tutti gli utenti solo se necessario. 

Come procedura consigliata, creare un account utente che sia:

- Dedicato all'amministrazione dei criteri 
- Escluso da tutti i criteri


## <a name="policy-migration"></a>Migrazione dei criteri

Valutare la possibilità di eseguire la migrazione dei criteri che non sono stati creati nel portale di Azure per i motivi seguenti:

- È ora possibile gestire scenari che in precedenza non era possibile gestire.

- Il consolidamento consente di ridurre il numero di criteri da gestire.   

- È possibile gestire tutti i criteri di accesso condizionale in un'unica posizione centrale.

- Il portale di Azure classico è stato ritirato.   


Per altre informazioni, vedere [Migrare i criteri classici nel portale di Azure](policy-migration.md).


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su:

- Come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Come pianificare i criteri di accesso condizionale, vedere [Come pianificare la distribuzione dell'accesso condizionale in Azure Active Directory](plan-conditional-access.md).
