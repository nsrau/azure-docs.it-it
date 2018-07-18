---
title: Uso del controllo dell'accesso in base all'età in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come identificare i minori che utilizzano l'applicazione.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9d24e37642a41e4d60b33f42a60d7e56cb4b35b5
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446725"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Utilizzo del controllo dell’accesso in base all’età in Azure Active Directory B2C

>[!IMPORTANT]
>Questa funzionalità è in anteprima privata.  Consultare il nostro [blog di assistenza](https://blogs.msdn.microsoft.com/azureadb2c/) per informazioni dettagliate sulla disponibilità di tale servizio, in alternativa contattare AADB2CFeedback@microsoft.com.  NON utilizzarlo in directory di produzione, l’utilizzo di queste nuove funzionalità può comportare la perdita di dati e causare modifiche impreviste di comportamento fino al momento in cui il servizio non assumerà un carattere di disponibilità generale.  
>

##<a name="age-gating"></a>Controllo dell'accesso in base all'età
Il controllo dell’accesso in base all’età consente di usare Azure Active Directory B2C per identificare i minori che utilizzano l'applicazione.  È possibile scegliere di impedire all'utente di accedere all'applicazione o consentirgli di tornare all'applicazione con attestazioni aggiuntive mirate a identificare la fascia di età dell'utente e il relativo stato di consenso dei genitori.  

>[!NOTE]
>Il consenso dei genitori viene monitorato in un attributo utente denominato `consentProvidedForMinor`.  È possibile aggiornare questa proprietà tramite l'API Graph, che utilizzerà questo campo durante l’aggiornamento di `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Impostazione della directory per il controllo dell'accesso in base all'età
Per poter utilizzare il controllo dell'accesso in base all'età nel flusso utente, è necessario configurare la directory in modo tale da supportare proprietà aggiuntive. Questa operazione può essere eseguita tramite `Properties` nel menu (che sarà disponibile solo se si è parte dell'anteprima privata).  
1. Nell'estensione di Azure Active Directory B2C, fare clic nel menu a sinistra sulle **Proprietà** per il tenant.
2. Nella sezione **Controllo dell'accesso in base all'età**, fare clic sul pulsante **Configura**.
3. Attendere il completamento dell'operazione e la directory verrà configurata per il controllo dell'accesso in base all'età.

##<a name="enabling-age-gating-in-your-user-flow"></a>Abilitazione del controllo dell'accesso in base all'età nel flusso utente
Dopo che la directory è stata configurata per l’utilizzo del controllo dell'accesso in base all'età, è possibile utilizzare questa funzionalità nei flussi utente della versione di anteprima.  Questa funzionalità richiede modifiche che la rendono incompatibile con i tipi di flussi di utente esistenti.  Abilitare il controllo dell'accesso in base all'età tramite i passaggi seguenti:
1. Creare un flusso utente di anteprima.
2. Una volta creato, nel menu passare a **Proprietà**.
3. Nella sezione **Controllo dell'accesso in base all'età**, selezionare l'elemento di attivazione/disattivazione per abilitare la funzionalità.
4. È quindi possibile scegliere come si desidera gestire gli utenti che si identificano come minori.

##<a name="what-does-enabling-age-gating-do"></a>Cosa comporta l’abilitazione del controllo dell'accesso in base all'età?
Dopo l'abilitazione del controllo dell'accesso in base all'età nel flusso utente, l'esperienza utente subisce alcune modifiche.  Al momento della registrazione, viene ora richiesta agli utenti la data di nascita e il Paese di residenza insieme agli attributi utente configurati per il flusso utente.  Al momento dell’accesso, verrà richiesto agli utenti di inserire la propria data di nascita e il Paese di residenza se non hanno già inserito tali dati.  Da questi due valori, Azure Active Directory B2C identifica se l'utente è un minore e aggiorna il campo `ageGroup`, il valore può essere `null`, `Undefined`, `Minor`, `Adult` e `NotAdult`.  I campi `ageGroup` e `consentProvidedForMinor` vengono quindi utilizzati per calcolare `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Opzioni di controllo dell'accesso in base all'età
È possibile scegliere che sia Azure Active Directory B2C a bloccare i minori senza il consenso dei genitori oppure a fornire loro l’autorizzazione, lasciando all’applicazione la decisione su come procedere.  

###<a name="allowing-minors-without-parental-consent"></a>Consenso ai minori senza il consenso dei genitori
Per i flussi utente che consentono la registrazione, l'accesso o entrambi, è possibile scegliere di consentire l’accesso all’applicazione ai minori senza consenso.  Ai minori senza il consenso dei genitori viene concesso di accedere o registrarsi come di consueto e Azure Active Directory B2C emette un token ID con l’attestazione `legalAgeGroupClassification`.  Tramite questa attestazione è possibile scegliere l'esperienza degli utenti, ad esempio attraverso un'esperienza per raccogliere il consenso dei genitori (e aggiornare il campo `consentProvidedForMinor`).

###<a name="blocking-minors-without-parental-consent"></a>Blocco dei minori senza il consenso dei genitori
Per i flussi utente che consentono la registrazione, l'accesso o entrambi, è possibile scegliere di bloccare l’accesso all’applicazione ai minori senza consenso.  Sono disponibili due opzioni per la gestione degli utenti bloccati in Azure Active Directory B2C:
* Restituisci un messaggio JSON all'applicazione, questa opzione invierà una risposta all'applicazione rispetto alla quale è stato bloccato un minore.
* Mostra una pagina di errore, l'utente visualizzerà una pagina che lo informa dell’impossibilità di accedere all'applicazione

##<a name="known-issues"></a>Problemi noti
###<a name="format-for-the-response-when-a-minor-is-blocked"></a>Formato della risposta quando viene bloccato un minore.
La risposta attualmente non è espressa correttamente, il bug verrà risolto in un aggiornamento successivo.

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>L'eliminazione di attributi specifici aggiunti durante l'installazione può impedire alla directory di utilizzare il controllo dell'accesso in base all'età.
Nella configurazione del controllo dell'accesso in base all'età, la directory viene configurata in `Properties` tramite un'opzione.  Se si elimina `legalCountry` o `dateOfBirth` mediante Graph, la directory non potrà più usare il controllo dell'accesso in base all'età e le proprietà non potranno essere ricreate.

###<a name="list-of-countries-is-incomplete"></a>L’elenco dei Paesi è incompleto
Attualmente l'elenco di Paesi per legalCountry è incompleto, i Paesi restanti verranno aggiunti in un aggiornamento successivo.