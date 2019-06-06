---
title: Abilitare il controllo dell'accesso in base all'età in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come identificare i minori che utilizzano l'applicazione.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33b379a03c92b81885f7adfc70f7025a85ce9057
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511673"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Abilitare il controllo dell'accesso in base all'età in Azure Active Directory B2C

>[!IMPORTANT]
>Questa funzionalità è disponibile in anteprima pubblica. Non usarla per le applicazioni di produzione. 
>

Il controllo dell'accesso in base all'età in Azure Active Directory (Azure AD) B2C consente di identificare i minori che vogliono usare l'applicazione. È possibile scegliere di impedire ai minori di accedere all'applicazione. In alternativa, gli utenti possono tornare all'applicazione e identificare la propria fascia di età e il relativo stato di consenso dei genitori. Con Azure AD B2C è possibile bloccare i minori privi del consenso dei genitori. Azure AD B2C può inoltre essere configurato in modo da consentire all'applicazione di decidere come gestire l'accesso dei minori.

Dopo aver abilitato nel controllo dell'età del [flusso utente](active-directory-b2c-reference-policies.md), agli utenti viene richiesto quando si è nati e il paese/area geografica si trovano in. Se un utente esegue l'accesso senza avere precedentemente immesso queste informazioni, dovrà farlo al successivo accesso. Le regole vengono applicate ogni volta che un utente esegue l'accesso.

Azure AD B2C usa le informazioni immesse dall'utente per stabilire se si tratta di un minore. Il campo **ageGroup** viene aggiornato di conseguenza nel rispettivo account. Il valore può essere `null`, `Undefined`, `Minor`, `Adult` e `NotAdult`.  I campi **ageGroup** e **consentProvidedForMinor** vengono quindi usati per calcolare il valore di **legalAgeGroupClassification**.

Il controllo dell'accesso in base all'età prevede due valori: l'età in cui un utente non è più considerato un minore e l'età in cui un minore deve avere il consenso dei genitori. La tabella seguente elenca le regole per l'età che vengono usate per identificare un minore e un minore che necessita del consenso.

| Paese/Area geografica | Nome di paese/area geografica | Minore età per cui richiedere il consenso | Minore età |
| -------------- | ------------------- | ----------------- | --------- |
| Predefinito | Nessuna | Nessuna | 18 |
| AE | Emirati Arabi Uniti | Nessuna | 21 |
| AT | Austria | 14 | 18 |
| BE | Belgio | 14 | 18 |
| BG | Bulgaria | 16 | 18 |
| BH | Bahrain | Nessuna | 21 |
| CM | Camerun | Nessuna | 21 |
| CY | Cipro | 16 | 18 |
| CZ | Repubblica ceca | 16 | 18 |
| DE | Germania | 16 | 18 |
| DK | Danimarca | 16 | 18 |
| EE | Estonia | 16 | 18 |
| EG | Egitto | Nessuna | 21 |
| ES | Spagna | 13 | 18 |
| VF | Francia | 16 | 18 |
| GB | Regno Unito | 13 | 18 |
| GR | Grecia | 16 | 18 |
| HR | Croazia | 16 | 18 |
| HU | Ungheria | 16 | 18 |
| IE | Irlanda | 13 | 18 |
| IT | Italia | 16 | 18 |
| KR | Repubblica di Corea | 14 | 18 |
| LT | Lituania | 16 | 18 |
| LU | Lussemburgo | 16 | 18 |
| LV | Lettonia | 16 | 18 |
| MT | Malta | 16 | 18 |
| NA | Namibia | Nessuna | 21 |
| NL | Paesi Bassi | 16 | 18 |
| PL | Polonia | 13 | 18 |
| PT | Portogallo | 16 | 18 |
| RO | Romania | 16 | 18 |
| SE | Svezia | 13 | 18 |
| SG | Singapore | Nessuna | 21 |
| SI | Slovenia | 16 | 18 |
| SK | Slovacchia | 16 | 18 |
| TD | Chad | Nessuna | 21 |
| TH | Thailandia | Nessuna | 20 |
| TW | Taiwan | Nessuna | 20 | 
| Stati Uniti | Stati Uniti | 13 | 18 |

## <a name="age-gating-options"></a>Opzioni di controllo dell'accesso in base all'età
 
### <a name="allowing-minors-without-parental-consent"></a>Consenso ai minori senza il consenso dei genitori

Per i flussi utente che consentono la registrazione, l'accesso o entrambi, è possibile scegliere di consentire l'accesso all'applicazione ai minori senza consenso. Ai minori senza il consenso dei genitori viene concesso di accedere o registrarsi come di consueto e Azure AD B2C emette un token ID con l'attestazione **legalAgeGroupClassification**. Questa attestazione definisce l'esperienza degli utenti, ad esempio la raccolta del consenso dei genitori e l'aggiornamento del campo **consentProvidedForMinor**.

### <a name="blocking-minors-without-parental-consent"></a>Blocco dei minori senza il consenso dei genitori

Per i flussi utente che consentono la registrazione, l'accesso o entrambi, è possibile scegliere di bloccare l'accesso all'applicazione ai minori senza consenso. Per la gestione degli utenti bloccati in Azure AD B2C sono disponibili le opzioni seguenti:

- Restituisci un messaggio JSON all'applicazione: questa opzione invia una risposta all'applicazione rispetto alla quale è stato bloccato un minore.
- Mostra una pagina di errore: l'utente visualizza una pagina che lo informa dell'impossibilità di accedere all'applicazione.

## <a name="set-up-your-tenant-for-age-gating"></a>Configurare il tenant per il controllo dell'accesso in base all'età

Per usare il controllo dell'accesso in base all'età in un flusso utente, è necessario configurare il tenant in modo tale da supportare proprietà aggiuntive.

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto. Selezionare la directory contenente il tenant. 
2. Selezionare **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
3. Selezionare **Proprietà** per il tenant nel menu a sinistra.
2. Nella sezione **Controllo dell'accesso in base all'età** fare clic su **Configura**.
3. Attendere il completamento dell'operazione e il tenant verrà configurato per il controllo dell'accesso in base all'età.

## <a name="enable-age-gating-in-your-user-flow"></a>Abilitare il controllo dell'accesso in base all'età nel flusso utente

Dopo che il tenant è stato configurato per l'uso del controllo dell'accesso in base all'età, è possibile usare questa funzionalità nei [flussi utente](user-flow-versions.md) in cui è abilitata. Abilitare il controllo dell'accesso in base all'età tramite i passaggi seguenti:

1. Creare un flusso utente con il controllo dell'accesso in base all'età abilitato.
2. Dopo aver creato il flusso utente, selezionare **Proprietà** nel menu.
3. Nella sezione **Controllo dell'accesso in base all'età** selezionare **Abilitato**.
4. È quindi possibile decidere in che modo gestire gli utenti che si identificano come minori. Per la **registrazione o l'accesso** selezionare `Allow minors to access your application` o `Block minors from accessing your application`. Se è selezionato il blocco dei minori, selezionare `Send a JSON back to the application` o `Show an error message`. 




