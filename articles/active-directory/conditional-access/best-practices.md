---
title: Procedure consigliate per l'accesso condizionale in Azure Active Directory Documenti Microsoft
description: Informazioni sulle operazioni da sapere e sulle operazioni da evitare di eseguire durante la configurazione dei criteri di accesso condizionale.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295348"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Procedure consigliate per l'accesso condizionale in Azure Active DirectoryBest practices for Conditional Access in Azure Active Directory

Con [l'accesso condizionale di Azure Active Directory (Azure AD),](../active-directory-conditional-access-azure-portal.md)è possibile controllare il modo in cui gli utenti autorizzati accedono alle app cloud. Questo articolo illustra quanto segue:

- Informazioni utili 
- Che cosa è necessario evitare di fare quando si configurano i criteri di accesso condizionale. 

In questo articolo si presuppone che l'utente abbia familiarità con i concetti e la terminologia descritta in [Che cos'è l'accesso condizionale in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Che cosa è necessario per far funzionare una politica?

Quando si crea un nuovo criterio, non sono presenti utenti, gruppi, app o controlli di accesso selezionati.

![App cloud](./media/best-practices/02.png)

Affinché il criterio funzioni, è necessario configurare quanto segue:

| Cosa           | Come                                  | Motivo |
| :--            | :--                                  | :-- |
| **App cloud** |Selezionare una o più app.  | L'obiettivo di un criterio di accesso condizionale è consentire di controllare il modo in cui gli utenti autorizzati possono accedere alle app cloud.|
| **Utenti e gruppi** | Selezionare almeno un utente o un gruppo autorizzato ad accedere alle app cloud selezionate. | Un criterio di accesso condizionale a cui non sono assegnati utenti e gruppi non viene mai attivato. |
| **Controlli di accesso** | Selezionare almeno un controllo di accesso. | L'elaboratore di criteri deve sapere quali operazioni eseguire se le condizioni vengono soddisfatte. |

## <a name="what-you-should-know"></a>Informazioni utili

### <a name="how-are-conditional-access-policies-applied"></a>Come vengono applicati i criteri di accesso condizionale?

Quando si accede a un'app cloud, possono essere applicati più criteri di accesso condizionale. In questo caso, devono essere soddisfatti tutti i criteri applicati. Ad esempio, se un criterio richiede l'autenticazione a più fattori (MFA) e un altro richiede un dispositivo conforme, è necessario completare l'autenticazione a più fattori e usare un dispositivo conforme. 

Tutti i criteri vengono applicati in due fasi:

- Fase 1: 
   - Raccolta di dettagli: raccogliere i dettagli per identificare i criteri che sarebbero già stati soddisfatti.
   - Durante questa fase, gli utenti potrebbero visualizzare una richiesta di certificato se la conformità del dispositivo fa parte dei criteri di accesso condizionale. Questa richiesta può verificarsi per le app del browser quando il sistema operativo del dispositivo non è Windows 10.
   - La fase 1 della valutazione dei criteri si verifica per tutti i criteri abilitati e i criteri in [modalità solo report.](concept-conditional-access-report-only.md)
- Fase 2:
   - Applicazione: tenendo conto dei dettagli raccolti nella fase 1, richiedere all'utente di soddisfare eventuali requisiti aggiuntivi che non sono stati soddisfatti.
   - Applica i risultati alla sessione. 
   - La fase 2 della valutazione dei criteri si verifica per tutti i criteri abilitati.

### <a name="how-are-assignments-evaluated"></a>Come vengono valutate le assegnazioni?

Tutte le assegnazioni vengono collegate logicamente con l'operatore **AND**. Se sono configurate più assegnazioni, per attivare un criterio devono essere soddisfatte tutte.  

Se è necessario configurare una condizione relativa alla località applicata a tutte le connessioni stabilite dall'esterno della rete dell'organizzazione:

- Includere **Tutte le località**
- Escludere **Tutti gli indirizzi IP attendibili**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Quali operazioni è necessario eseguire se non si riesce ad accedere al portale di amministrazione di Azure AD?

Se si è bloccati dal portale di Azure AD a causa di un'impostazione non corretta in un criterio di accesso condizionale:If you are locked out of the Azure AD portal due to an incorrect setting in a Conditional Access policy:

- Controllare se siano presenti altri amministratori dell'organizzazione che non sono ancora stati bloccati. Un amministratore con accesso al portale di Azure può disabilitare il criterio che impedisce l'accesso. 
- Se nessun amministratore dell'organizzazione può aggiornare il criterio, è necessario inviare una richiesta di supporto. Il supporto tecnico Microsoft può esaminare e aggiornare i criteri di accesso condizionale che impediscono l'accesso.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Che cosa accade se sono configurati criteri nel portale di Azure classico e nel portale di Azure?  

Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Che cosa accade se sono presenti criteri nel portale di Intune Silverlight e nel portale di Azure?

Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Che cosa accade se sono configurati più criteri per lo stesso utente?  

Per ogni accesso, Azure Active Directory valuta tutti i criteri e verifica che tutti i requisiti vengano soddisfatti prima di concedere l'accesso all'utente. Il criterio di blocco dell'accesso è prioritario rispetto alle altre impostazioni di configurazione. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>L'accesso condizionale funziona con Exchange ActiveSync?

Sì, è possibile utilizzare Exchange ActiveSync in un criterio di accesso condizionale.

Alcune app cloud come SharePoint Online ed Exchange Online supportano anche protocolli di autenticazione legacy. Quando un'app client può usare un protocollo di autenticazione legacy per accedere a un'app cloud, Azure AD non può applicare criteri di accesso condizionale a questo tentativo di accesso. Per impedire a un'app client di ignorare l'applicazione di criteri, è necessario controllare se è possibile abilitare solo l'autenticazione moderna nelle app cloud interessate.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Come configurare l'accesso condizionale con le app di Office 365

Poiché le app di Office 365 sono interconnesse, è consigliabile assegnare le app di uso comune durante la creazione dei criteri.

Le applicazioni interconnesse comuni includono Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online e Office 365 Yammer.

È importante per i criteri che richiedono interazioni dell'utente, ad esempio l'autenticazione a più fattori, quando l'accesso viene controllato all'inizio di una sessione o di un'attività. In caso contrario, gli utenti non saranno in grado di completare alcune attività all'interno di un'app. Ad esempio, se è necessaria l'autenticazione a più fattori nei dispositivi non gestiti per accedere a SharePoint ma non alla posta elettronica, gli utenti che lavorano nella posta elettronica non saranno in grado di allegare file di SharePoint a un messaggio. Ulteriori informazioni sono disponibili nell'articolo [Che cosa sono le dipendenze del servizio in Azure Active Directory Conditional Access?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>Azioni da evitare

Il framework di accesso condizionale offre una grande flessibilità di configurazione. Con un'elevata flessibilità, tuttavia, è consigliabile esaminare attentamente ogni criterio di configurazione prima del rilascio per evitare risultati indesiderati. In questo contesto, è necessario prestare particolare attenzione alle assegnazioni che interessano set completi, ad esempio **tutti gli utenti/i gruppi/le applicazioni cloud**.

Nell'ambiente, è necessario evitare le seguenti configurazioni:

**Per tutti gli utenti e tutte le applicazioni cloud:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.
- **Richiedi un dispositivo conforme**: per gli utenti che non hanno ancora registrato i propri dispositivi, questo criterio blocca tutti gli accessi, incluso l'accesso al portale di Intune. Se l'utente è amministratore senza un dispositivo registrato, questo criterio blocca l'accesso al portale di Azure per la modifica dei criteri.
- **Require domain join** (Richiedi aggiunta a dominio): se ancora non si dispone di un dispositivo aggiunto al dominio, questo criterio di blocco dell'accesso è anche in grado di bloccare l'accesso per tutti gli utenti nell'organizzazione.
- Richiedi criteri di **protezione delle app:** questo criterio blocca l'accesso può anche bloccare l'accesso a tutti gli utenti dell'organizzazione se non si dispone di un criterio di Intune.Require app protection policy - This policy block access has also the potential to block access for all users in your organization if you don't have an Intune policy. Se si è un amministratore senza un'applicazione client con criteri di protezione delle app di Intune, questo criterio impedisce di tornare a portali come Intune e Azure.If you are an administrator without a client application that has an Intune app protection policy, this policy imp.if you are an administrator without a client application that has an Intune app protection policy, this policy imp.If you are an administrator without a client application that has an Intune app protection policy, this policy imp.Reson d.

**Per tutti gli utenti, tutte le applicazioni cloud e tutte le piattaforme per dispositivi:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.

## <a name="how-should-you-deploy-a-new-policy"></a>Come distribuire un nuovo criterio

Come primo passaggio, è consigliabile valutare il criterio con lo [strumento per l'analisi di simulazione](what-if-tool.md).

Quando i nuovi criteri sono pronti per l'ambiente, distribuirli in più fasi:

1. Applicare un criterio a un set ridotto di utenti e verificare che abbia il comportamento previsto. 
1. Quando si espande un criterio per includere altri utenti, continuare a escludere tutti gli amministratori dal criterio per assicurarsi che abbiano ancora accesso e possano aggiornare un criterio se è necessaria una modifica.
1. Applicare un criterio a tutti gli utenti solo se necessario. 

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

- Come configurare un criterio di accesso condizionale, vedere [Richiedere l'autenticazione a più fattori per app specifiche con l'accesso condizionale](app-based-mfa.md)di Azure Active Directory .
- Come pianificare i criteri di accesso condizionale, vedere [Come pianificare la distribuzione](plan-conditional-access.md)di accesso condizionale in Azure Active Directory .
