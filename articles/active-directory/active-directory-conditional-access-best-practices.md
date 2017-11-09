---
title: Procedure consigliate per l'accesso condizionale in Azure Active Directory | Microsoft Docs
description: "Informazioni sugli aspetti da conoscere e su ciò che è consigliabile evitare quando si configurano i criteri di accesso condizionale."
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 74b97ac263dcc45f7a8dd7461cbdb23d9fd5e6fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Procedure consigliate per l'accesso condizionale in Azure Active Directory

Questo argomento fornisce informazioni sugli aspetti da conoscere e su ciò che è consigliabile evitare quando si configurano i criteri di accesso condizionale. Prima di procedere nella lettura, occorre acquisire familiarità con i concetti e la terminologia descritti in [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)

## <a name="what-you-should-know"></a>Informazioni utili

### <a name="whats-required-to-make-a-policy-work"></a>Elementi necessari per il funzionamento di un criterio

Quando si crea un nuovo criterio, non sono selezionti utenti, gruppi, app o controlli di accesso.

![App cloud](./media/active-directory-conditional-access-best-practices/02.png)


Per far funzionare il criterio, è necessario configurare quanto segue:


|Cosa           | Come                                  | Motivo|
|:--            | :--                                  | :-- |
|**App cloud** |È necessario selezionare una o più app.  | L'obiettivo di un criterio di accesso condizionale è consentire di ottimizzare il modo in cui gli utenti autorizzati possono accedere alle app.|
| **Utenti e gruppi** | È necessario selezionare almeno un utente o gruppo che è autorizzato ad accedere alle app cloud selezionate. | Un criterio di accesso condizionale a cui non sono assegnati utenti e gruppi non viene mai attivato. |
| **Controlli di accesso** | È necessario selezionare almeno un controllo di accesso. | Il processore del criterio deve sapere cosa fare se vengono soddisfatte le condizioni.|


Oltre a questi requisiti di base, in molti casi è necessario anche configurare una condizione. Mentre un criterio funzionerebbe anche senza una condizione configurata, le condizioni rappresentano il fattore determinante per ottimizzare l'accesso alle app.


![App cloud](./media/active-directory-conditional-access-best-practices/04.png)



### <a name="how-are-assignments-evaluated"></a>Come vengono valutate le assegnazioni?

Tutte le assegnazioni vengono collegate logicamente con l'operatore **AND**. Se è configurata più di un'assegnazione, per attivare un criterio, devono essere soddisfatte tutte le assegnazioni.  

Se è necessario configurare una condizione della località che si applica a tutte le connessioni stabilite dall'esterno della rete dell'organizzazione, è possibile:

- Includere **Tutte le località**
- Escludere **Tutti gli indirizzi IP attendibili**

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Che cosa accade se sono configurati criteri nel portale di Azure classico e nel portale di Azure?  
Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Che cosa accade se sono presenti criteri nel portale di Intune Silverlight e nel portale di Azure?
Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Che cosa accade se sono configurati più criteri per lo stesso utente?  
Per ogni accesso, Azure Active Directory valuta tutti i criteri e verifica che tutti i requisiti vengano soddisfatti prima di concedere l'accesso all'utente.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>L'accesso condizionale funziona con Exchange ActiveSync?

Sì, è possibile usare Exchange ActiveSync in criteri di accesso condizionale.


## <a name="what-you-should-avoid-doing"></a>Azioni da evitare

Il framework di accesso condizionale offre ottima flessibilità di configurazione. Tuttavia, questa flessibilità ideale comporta anche che è opportuno esaminare attentamente ogni criterio di configurazione prima che venga rilasciato, in modo da evitare risultati indesiderati. In questo contesto, è necessario prestare particolare attenzione alle assegnazioni che interessano set completi, ad esempio **tutti gli utenti/i gruppi/le applicazioni cloud**.

Nell'ambiente, è necessario evitare le seguenti configurazioni:


**Per tutti gli utenti e tutte le applicazioni cloud:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.

- **Richiedi un dispositivo conforme**: per gli utenti che non hanno ancora registrato i propri dispositivi, questo criterio blocca tutti gli accessi, tra cui l'accesso al portale di Intune. Se l'utente è amministratore senza un dispositivo registrato, questo criterio blocca l'accesso al portale di Azure per la modifica dei criteri.

- **Require domain join** (Richiedi aggiunta a dominio): se ancora non si dispone di un dispositivo aggiunto al dominio, questo criterio di blocco dell'accesso è anche in grado di bloccare l'accesso per tutti gli utenti nell'organizzazione.


**Per tutti gli utenti, tutte le applicazioni cloud e tutte le piattaforme per dispositivi:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.



## <a name="policy-migration"></a>Migrazione dei criteri

Se nel portale di Azure classico sono configurati dei criteri, è opportuno eseguirne la migrazione nel portale di Azure per i motivi seguenti:


- Un utente per cui sono configurati criteri nel portale di Azure classico e nel portale di Azure deve soddisfare i requisiti per entrambi i criteri 

- Se non si esegue la migrazione dei criteri esistenti, non si potranno implementare i criteri che concedono l'accesso


### <a name="migration-from-the-azure-classic-portal"></a>Migrazione dal portale di Azure classico

In questo scenario: 

- Nel [portale di Azure classico](https://manage.windowsazure.com) sono configurati:

    - SharePoint Online

    ![Accesso condizionale](./media/active-directory-conditional-access-best-practices/14.png)

    - Criteri di accesso condizionale basato su dispositivo

    ![Accesso condizionale](./media/active-directory-conditional-access-best-practices/15.png)

- Si vuole eseguire la configurazione di criteri di accesso condizionale con gestione di applicazioni mobili nel portale di Azure 
 

#### <a name="configuration"></a>Configurazione 

- Esaminare i criteri di accesso condizionale basato su dispositivo

- Eseguirne la migrazione nel portale di Azure 

- Aggiungere criteri di accesso condizionale con gestione di applicazioni mobili


### <a name="migrating-from-intune"></a>Migrazione da Intune 

In questo scenario:

- In [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ) sono configurati criteri di accesso condizionale con gestione di applicazioni mobili per Exchange Online o SharePoint Online

    ![Accesso condizionale](./media/active-directory-conditional-access-best-practices/15.png)

- Si vuole eseguire la migrazione in modo da usare l'accesso condizionale con gestione di applicazioni mobili nel portale di Azure


#### <a name="configuration"></a>Configurazione 
 
- Esaminare i criteri di accesso condizionale basato su dispositivo

- Eseguirne la migrazione nel portale di Azure 

- Esaminare i criteri di accesso condizionale con gestione di applicazioni mobili configurati per Exchange Online o SharePoint Online in Intune

- Aggiungere il controllo per **richiedere applicazioni approvate** oltre al controllo basato su dispositivo 
 

### <a name="migrating-from-the-azure-classic-portal-and-intune"></a>Migrazione dal portale di Azure classico e Intune

In questo scenario:

- Sono configurati i criteri seguenti:

    - **Portale di Azure classico:** criteri di accesso condizionale basato su dispositivo 

    - **Intune:** criteri di accesso condizionale con gestione di applicazioni mobili 
    
- Si vuole eseguire la migrazione di entrambi i criteri in modo da usare l'accesso condizionale con gestione di applicazioni mobili nel portale di Azure


#### <a name="configuration"></a>Configurazione

- Esaminare i criteri di accesso condizionale basato su dispositivo

- Eseguirne la migrazione nel portale di Azure 

- Esaminare i criteri di accesso condizionale con gestione di applicazioni mobili configurati per Exchange Online o SharePoint Online in Intune

- Aggiungere il controllo per **richiedere applicazioni approvate** oltre a quello basato su dispositivo 




## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).
