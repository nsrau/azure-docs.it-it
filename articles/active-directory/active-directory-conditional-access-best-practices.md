---
title: Procedure consigliate per l'accesso condizionale in Azure Active Directory | Microsoft Docs
description: "Informazioni sugli aspetti da conoscere e su ciò che è consigliabile evitare quando si configurano i criteri di accesso condizionale."
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 16f9179b6cbaee00a2afbe2efe090cb3eb8b204a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Procedure consigliate per l'accesso condizionale in Azure Active Directory

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) è possibile controllare il modo in cui gli utenti autorizzati accedono alle app cloud. Questo articolo illustra quanto segue:

- Informazioni utili 
- Azioni da evitare nella configurazione dei criteri di accesso condizionale 

Questo articolo presuppone che l'utente abbia familiarità con i concetti e la terminologia descritti in [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).



## <a name="whats-required-to-make-a-policy-work"></a>Elementi necessari per il funzionamento di un criterio

Quando si crea un nuovo criterio, non sono presenti utenti, gruppi, app o controlli di accesso selezionati.

![App cloud](./media/active-directory-conditional-access-best-practices/02.png)


Affinché il criterio funzioni, è necessario configurare quanto segue:


|Cosa           | Come                                  | Motivo|
|:--            | :--                                  | :-- |
|**App cloud** |È necessario selezionare una o più app.  | L'obiettivo di un criterio di accesso condizionale è consentire di controllare il modo in cui gli utenti potranno accedere alle app cloud.|
| **Utenti e gruppi** | È necessario selezionare almeno un utente o un gruppo autorizzato ad accedere alle app cloud selezionate. | Un criterio di accesso condizionale a cui non sono assegnati utenti e gruppi non viene mai attivato. |
| **Controlli di accesso** | È necessario selezionare almeno un controllo di accesso. | L'elaboratore di criteri deve sapere quali operazioni eseguire se le condizioni vengono soddisfatte.|




## <a name="what-you-should-know"></a>Informazioni utili

### <a name="how-are-assignments-evaluated"></a>Come vengono valutate le assegnazioni?

Tutte le assegnazioni vengono collegate logicamente con l'operatore **AND**. Se sono configurate più assegnazioni, per attivare un criterio devono essere soddisfatte tutte.  

Se è necessario configurare una condizione relativa alla località applicata a tutte le connessioni stabilite dall'esterno della rete dell'organizzazione:

- Includere **Tutte le località**
- Escludere **Tutti gli indirizzi IP attendibili**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Quali operazioni è necessario eseguire se non si riesce ad accedere al portale di amministrazione di Azure AD?

Se non si riesce ad accedere al portale di Azure AD a causa di un'impostazione errata in un criterio di accesso condizionale:

- Verificare se siano presenti altri amministratori dell'organizzazione che non sono ancora stati bloccati. Un amministratore con accesso al portale di Azure può disabilitare il criterio che impedisce l'accesso. 

- Se nessun amministratore dell'organizzazione può aggiornare il criterio, è necessario inviare una richiesta di supporto. Il supporto tecnico Microsoft può esaminare e aggiornare i criteri di accesso condizionale che impediscono l'accesso.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Che cosa accade se sono configurati criteri nel portale di Azure classico e nel portale di Azure?  

Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Che cosa accade se sono presenti criteri nel portale di Intune Silverlight e nel portale di Azure?

Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Che cosa accade se sono configurati più criteri per lo stesso utente?  

Per ogni accesso, Azure Active Directory valuta tutti i criteri e verifica che tutti i requisiti vengano soddisfatti prima di concedere l'accesso all'utente.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>L'accesso condizionale funziona con Exchange ActiveSync?

Sì, è possibile usare Exchange ActiveSync in criteri di accesso condizionale.






## <a name="what-you-should-avoid-doing"></a>Azioni da evitare

Il framework di accesso condizionale offre ottima flessibilità di configurazione. Con un'elevata flessibilità, tuttavia, è consigliabile esaminare attentamente ogni criterio di configurazione prima del rilascio per evitare risultati indesiderati. In questo contesto, è necessario prestare particolare attenzione alle assegnazioni che interessano set completi, ad esempio **tutti gli utenti/i gruppi/le applicazioni cloud**.

Nell'ambiente, è necessario evitare le seguenti configurazioni:


**Per tutti gli utenti e tutte le applicazioni cloud:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.

- **Richiedi un dispositivo conforme**: per gli utenti che non hanno ancora registrato i propri dispositivi, questo criterio blocca tutti gli accessi, tra cui l'accesso al portale di Intune. Se l'utente è amministratore senza un dispositivo registrato, questo criterio blocca l'accesso al portale di Azure per la modifica dei criteri.

- **Require domain join** (Richiedi aggiunta a dominio): se ancora non si dispone di un dispositivo aggiunto al dominio, questo criterio di blocco dell'accesso è anche in grado di bloccare l'accesso per tutti gli utenti nell'organizzazione.


**Per tutti gli utenti, tutte le applicazioni cloud e tutte le piattaforme per dispositivi:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.


## <a name="how-should-you-deploy-a-new-policy"></a>Come distribuire un nuovo criterio

Come primo passaggio, è consigliabile valutare il criterio con lo [strumento per l'analisi di simulazione](active-directory-conditional-access-whatif.md).

Quando si è pronti a distribuire un nuovo criterio nel proprio ambiente, è consigliabile procedere per fasi:

1. Applicare un criterio a un set ridotto di utenti e verificare che abbia il comportamento previsto. 

2.  Quando si espande un criterio per includere un maggior numero di utenti, continuare a escludere tutti gli amministratori dal criterio. In questo modo, gli amministratori potranno comunque accedere e aggiornare il criterio, se è necessaria una modifica.

3. Applicare un criterio a tutti gli utenti solo se realmente necessario. 

Come procedura consigliata, creare un account utente che sia:

- Dedicato all'amministrazione dei criteri 
- Escluso da tutti i criteri


## <a name="policy-migration"></a>Migrazione dei criteri

Valutare la possibilità di eseguire la migrazione dei criteri che non sono stati creati nel portale di Azure per i motivi seguenti:

- È ora possibile gestire scenari che in precedenza non era possibile gestire.

- Il consolidamento consente di ridurre il numero di criteri da gestire.   

- È possibile gestire tutti i criteri di accesso condizionale in un'unica posizione centralizzata.

- Il portale di Azure classico è stato ritirato.   


Per altre informazioni, vedere [Migrare i criteri classici nel portale di Azure](active-directory-conditional-access-migration.md).


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).
