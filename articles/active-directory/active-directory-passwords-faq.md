---
title: 'Domande frequenti: reimpostazione della password self-service di Azure AD | Microsoft Docs'
description: Domande frequenti su sulla reimpostazione della password self-service di Azure AD
services: active-directory
keywords: gestione delle password in Active Directory, gestione delle password, reimpostazione della password self-service di Azure AD
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a457ba3109625ab43ae3a88b95a3e7e1e9641921
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="password-management-frequently-asked-questions"></a>Domande frequenti sulla gestione delle password

Di seguito sono riportate alcune domande frequenti su tutti gli aspetti riguardanti la reimpostazione delle password.

In caso di domande generiche su Azure AD e la reimpostazione della password self-service per cui non è possibile trovare una risposta qui, è possibile richiedere assistenza alla community sui [forum di Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). La community è composta da ingegneri, responsabili dei prodotti, MVP e informatici.

Questo articolo di domande frequenti è suddiviso nelle sezioni seguenti:

* [**Domande sulla registrazione per la reimpostazione della password**](#password-reset-registration)
* [**Domande sulla reimpostazione della password**](#password-reset)
* [**Domande sulla modifica della password**](#password-change)
* [**Domande relative ai report di gestione delle password**](#password-management-reports)
* [**Domande sul writeback delle password**](#password-writeback)

## <a name="password-reset-registration"></a>Registrazione per la reimpostazione della password

* **D: Gli utenti possono registrare i propri dati per la reimpostazione della password?**

  > **R:** Sì, a condizione che la reimpostazione della password sia abilitata, gli utenti con licenza possono accedere al portale di registrazione per la reimpostazione della password all'indirizzo http://aka.ms/ssprsetup per registrare le informazioni di autenticazione. Gli utenti possono anche registrarsi tramite il pannello di accesso all'indirizzo http://myapps.microsoft.com, facendo clic sulla scheda del profilo e scegliendo l'opzione Esegui la registrazione per la reimpostazione della password.
  >
  >
* **D: è possibile definire i dati di reimpostazione della password per conto degli utenti?**

  > **R:** Sì, è possibile farlo con Azure AD Connect, PowerShell, con il [portale di Azure](https://portal.azure.com) o tramite l'interfaccia di amministrazione di Office. Per altre informazioni, vedere l'articolo [Dati usati dal servizio di reimpostazione della password self-service di Azure AD](active-directory-passwords-data.md).
  >
  >
* **D: È possibile sincronizzare i dati per le domande di sicurezza dall'ambiente locale?**

  > **R:** Attualmente non è possibile.
  >
  >
* **D: Gli utenti possono registrare i dati in modo che non siano visibili ad altri utenti?**

  > **R:** Sì. I dati che gli utenti registrano tramite il portale di registrazione per la reimpostazione della password vengono salvati in campi di autenticazione privati visibili solo agli amministratori globali e agli utenti.
    >
    > [!NOTE]
    > Se un **account amministratore di Azure** registra il numero di telefono di autenticazione, questo viene inserito anche nel campo Cellulare ed è visibile.
    >
  >
  >
* **D: Gli utenti devono essere registrati prima di poter usare la funzionalità di reimpostazione della password?**

  > **R:** No. Se le informazioni di autenticazione definite per conto degli utenti sono sufficienti, gli utenti non dovranno registrarsi. La reimpostazione della password funziona purché i dati archiviati nei campi appropriati nella directory siano formattati correttamente.
  >
  >
* **D: È possibile sincronizzare o impostare i campi Telefono per l'autenticazione, Indirizzo di posta elettronica per l'autenticazione o Telefono di autenticazione alternativo per conto degli utenti?**

  > **R:** Attualmente non è possibile.
  >
  >
* **D: In che modo il portale di registrazione riconosce le opzioni da visualizzare agli utenti?**

  > **R:** Il portale di registrazione per la reimpostazione delle password mostra solo le opzioni che sono state abilitate per gli utenti. Queste opzioni si trovano nella sezione Criteri di reimpostazione password utente della scheda Configura relativa alla directory. Di conseguenza, se ad esempio non si abilitano le domande di sicurezza, gli utenti non possono registrarsi per questa opzione.
  >
  >
* **D: Quando un utente viene considerato registrato?**

  > **R:** Un utente viene considerato registrato per la reimpostazione della password self-service quando è stato registrato almeno il **numero di metodi necessari per la reimpostazione** impostato nel [portale di Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Reimpostazione delle password

* **D: Quanto tempo occorre attendere prima di ricevere un messaggio di posta elettronica, un SMS o una telefonata relativa alla reimpostazione della password?**

  > **R:** I messaggi di posta elettronica, gli SMS e le telefonate dovrebbero arrivare entro un minuto, normalmente tra 5 e 20 secondi.
    >Se non si riceve la notifica entro questo periodo di tempo:
        > * Controllare la cartella della posta indesiderata.
        > * Controllare che il numero o il messaggio di posta elettronica contattato sia quello che previsto.
        > * Controllare che i dati di autenticazione nella directory siano formattati correttamente.
                >     * Esempio: "+1 4255551234" o "user@contoso.com"
  >
  >
* **D: Quali lingue sono supportate per la reimpostazione della password?**

  > **R:** L'interfaccia utente, gli SMS e le chiamate vocali per la reimpostazione della password sono localizzati nelle stesse lingue supportate in Office 365.
  >
  >
* **D: A quali parti dell'esperienza di reimpostazione della password vengono applicate le impostazioni di personalizzazione dell'organizzazione specificate nella scheda Configura della directory?**

  > **R:** Il portale per la reimpostazione della password mostra il logo dell'organizzazione e consente di configurare il collegamento Contattare l'amministratore che punti a un indirizzo di posta elettronica o a un URL personalizzato. Nel corpo di tutti i messaggi di posta elettronica inviati dalla funzionalità di reimpostazione della password sono inclusi il logo, i colori e il nome dell'organizzazione, oltre al nome Da personalizzato.
  >
  >
* **D: In che modo è possibile spiegare agli utenti come fare per reimpostare le proprie password?**

  > **R:** provare alcuni dei suggerimenti dell'[articolo sulla distribuzione di SSPR](active-directory-passwords-best-practices.md#email-based-rollout)
  >
  >
* **D: Questa pagina può essere usata da un dispositivo mobile?**

  > **R:** Sì, la pagina funziona anche sui dispositivi mobili.
  >
  >
* **D: È supportato lo sblocco di account Active Directory locali quando gli utenti reimpostano le password?**

  > **R:** Sì, quando un utente reimposta la password e il writeback delle password viene distribuito mediante Azure AD Connect, tale account utente viene sbloccato automaticamente quando si reimposta la password.
  >
  >
* **D: Come è possibile integrare la reimpostazione della password direttamente nell'esperienza di accesso desktop degli utenti?**

  > **R:** I clienti di Azure AD Premium possono installare gratuitamente Microsoft Identity Manager e distribuire la soluzione di reimpostazione della password locale per soddisfare questo requisito.
  >
  >
* **D: È possibile impostare domande di sicurezza diverse per impostazioni locali diverse?**

  > **R:** Attualmente non è possibile.
  >
  >
* **D: Quante domande si possono configurare per l'opzione di autenticazione Domande di sicurezza?**

  > **R:** Nel [portale di Azure](https://portal.azure.com) è possibile configurare un massimo di 20 domande di sicurezza personalizzate.
  >
  >
* **D: Qual è la lunghezza delle domande di sicurezza?**

  > **R:** Le domande di sicurezza possono avere una lunghezza compresa tra 3 e 200 caratteri.
  >
  >
* **D: Qual è la lunghezza delle risposte alle domande di sicurezza?**

  > **R:** Le risposte possono avere una lunghezza compresa tra 3 e 40 caratteri.
  >
  >
* **D: Le risposte duplicate alle domande di sicurezza vengono rifiutate?**

  > **R:** Sì, vengono rifiutate.
  >
  >
* **D: Un utente può registrarsi più di una volta per la stessa domanda di sicurezza?**

  > **R:** No. Dopo che l'utente ha registrato una determinata domanda, non potrà registrarsi per quella domanda una seconda volta.
  >
  >
* **D: È possibile impostare un limite minimo di domande di sicurezza per la registrazione e la reimpostazione?**

  > **R:** Sì, è possibile impostare un limite per la registrazione e un altro per la reimpostazione. Possono essere richieste da 3 a 5 domande di sicurezza per la registrazione e da 3 a 5 domande di sicurezza per la reimpostazione.
  >
  >
* **D: Il criterio è stato configurato per richiedere agli utenti di usare domande di sicurezza per la reimpostazione, ma gli amministratori di Azure sembrano configurati in modo diverso.**

  > **R:** Si tratta del comportamento previsto. Microsoft applica un criterio di reimpostazione della password complessa a due gate predefinito per i ruoli di amministratore di Azure. In questo modo gli amministratori sono esentati dall'uso di domande di sicurezza. Per altre informazioni su questo criterio, vedere l'articolo [Restrizioni e criteri password in Azure Active Directory](active-directory-passwords-policy.md#administrator-password-policy-differences)
  >
  >
* **D: Se un utente ha registrato più del numero massimo di domande per reimpostare la password, come vengono selezionate le domande di sicurezza durante la reimpostazione?**

  > **R:** Dal numero totale di domande per cui l'utente si è registrato vengono selezionate casualmente N domande di sicurezza, dove N è il **numero di domande richieste per la reimpostazione**. Ad esempio, se un utente ha registrato 5 domande di sicurezza, ma per la reimpostazione ne sono richieste solo 3, ne verranno selezionate casualmente e presentate 3 di quelle 5 al momento della reimpostazione. Se un utente sbaglia le risposte alle domande, il processo di selezione verrà eseguito di nuovo per evitare la ripetizione delle domande.
  >
  >
* **D: Viene impedito agli utenti di provare a reimpostare la password più volte in un breve periodo di tempo?**

  > **R:** Sì. Per evitarne l'uso improprio, il processo di reimpostazione della password comprende funzionalità di sicurezza. Gli utenti possono effettuare solo 5 tentativi di reimpostazione della password nell'arco di un'ora, prima di essere bloccati per 24 ore. Gli utenti possono convalidare un numero di telefono solo 5 volte nell'arco di un'ora, prima di essere bloccati per 24 ore. Gli utenti possono provare solo 5 volte un singolo metodo di autenticazione nell'arco di un'ora, prima di essere bloccati per 24 ore.
  >
  >
* **D: Per quanto tempo è valido il passcode monouso inviato per posta elettronica o SMS?**

  > **R:** La durata della sessione per la reimpostazione della password è di 15 minuti. Dall'inizio dell'operazione di reimpostazione della password l'utente ha 15 minuti per completare l'operazione. Allo scadere di questo periodo di tempo, il passcode monouso inviato per SMS o posta elettronica non sarà più valido.
  >
  >

## <a name="password-change"></a>Modifica della password

* **D: Dove devono andare gli utenti per modificare la password?**

  > **R:** Gli utenti possono modificare le password in qualsiasi punto visualizzino l'immagine o l'icona del profilo, ad esempio nell'angolo in alto a destra della sezione [Office 365](https://portal.office.com) o [Access Panel](https://myapps.microsoft.com) (Pannello di accesso). Gli utenti possono modificare le password dalla [pagina profilo del Pannello di accesso](https://account.activedirectory.windowsazure.com/r#/profile). Se la password è scaduta, agli utenti potrebbe venir richiesto di modificare la password automaticamente nella schermata di accesso di Azure AD. Infine, se desiderano modificare la password, gli utenti possono accedere direttamente al [portale di modifica della password di Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx).
  >
  >
* **D: Gli utenti possono ricevere una notifica nel portale di Office alla scadenza della propria password locale?**

  > **R:** Attualmente è possibile se si usa ADFS seguendo le istruzioni riportate di seguito: [Sending Password Policy Claims with ADFS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) (Invio delle attestazioni dei criteri delle password con ADFS). Se si usa la sincronizzazione dell'hash della password, al momento non è possibile. Ciò avviene perché i criteri della password locale non vengono sincronizzati, pertanto non è possibile inviare notifiche sulla scadenza nel cloud. In entrambi i casi, è anche possibile [inviare notifiche tramite PowerShell agli utenti le cui password stanno per scadere](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >

## <a name="password-management-reports"></a>Report di gestione delle password

* **D: Quanto tempo trascorre prima che i dati vengono visualizzati nei report di gestione delle password?**

  > **R:** I dati dovrebbero essere visualizzati in tali report entro 5-10 minuti. In alcuni casi, potrebbero richiedere fino a un'ora.
  >
  >
* **D: Come si possono filtrare i report di gestione delle password?**

  > **R:** Per filtrare i report di gestione delle password, è possibile fare clic sulla piccola lente di ingrandimento all'estrema destra delle etichette di colonna, vicino alla parte superiore del report. Per applicare una funzione di filtro più completa, scaricare il report in Excel e creare una tabella pivot.
  >
  >
* **D: qual è il numero massimo di eventi che vengono archiviati nei report di gestione della password?**

  > **R:** Nei report di gestione della password vengono archiviati fino a 75.000 eventi di reimpostazione della password o di registrazione della reimpostazione della password, con un backup fino a 30 giorni.  Stiamo lavorando per espandere questo numero al fine di includere un maggior numero di eventi.
  >
  >
* **D: Fino a quando risalgono i report di gestione delle password?**

  > **R:** Questi report mostrano le operazioni che si sono verificate negli ultimi 30 giorni. Per il momento, se occorre archiviare i dati, è possibile scaricare i report periodicamente e salvarli in un percorso separato.
  >
  >
* **D: Esiste un limite al numero massimo di righe visualizzabili nei report di gestione delle password?**

  > **R:** Sì, è possibile visualizzare un massimo di 75.000 righe nei report di gestione delle password, sia quando vengono riprodotti nell'interfaccia utente sia quando vengono scaricati.
  >
  >
* **D: Esiste un'API per accedere alla reimpostazione della password o alla raccolta dei dati di registrazione?**

  > **R:** Sì, per informazioni su come accedere al flusso dei dati del report sulla reimpostazione della password, vedere la documentazione seguente.  [Informazioni su come accedere agli eventi di report della reimpostazione della password a livello di programmazione](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Writeback delle password

* **D: Come funziona il writeback delle password in background?**

  > **R:** Per una spiegazione di ciò che accade quando si abilita il writeback delle password, nonché del modo in cui i dati transitano dal sistema all'ambiente locale, vedere [Funzionamento del writeback delle password](active-directory-passwords-writeback.md).
  >
  >
* **D: Entro quanto tempo si attiva il funzionamento del writeback delle password?  Esiste un ritardo di sincronizzazione come per la sincronizzazione di hash della password?**

  > **R:** Il writeback delle password è immediato. Si tratta di una pipeline sincrona che funziona fondamentalmente in modo diverso rispetto alla sincronizzazione di hash della password. Il writeback delle password consente agli utenti di avere un feedback in tempo reale sulla riuscita dell'operazione di reimpostazione o modifica della propria password. L'intervallo medio per un writeback della password riuscito è inferiore a 500 ms.
  >
  >
* **D: Se l'account locale è disabilitato, cosa succede all'accesso o all'account cloud?**

  > **R:** Se l'ID locale è disabilitato, anche l'ID o l'accesso cloud verranno disabilitati al successivo intervallo di sincronizzazione tramite AAD Connect; per impostazione predefinita questo avviene ogni 30 minuti.
  >
  >
* **D: Se l'account locale è vincolato da un criterio di password di Active Directory locale, la reimpostazione della password self-service rispetta questo criterio quando si modifica la password?**

  > **R:** Sì, la reimpostazione della password self-service si basa e si attiene ai criteri della password di Active Directory locale, che includono il tipico criterio della password di dominio AD, nonché tutti i criteri della password con granularità fine definiti destinati a un determinato utente.
  >
  >
* **D: Per quali tipi di account funziona il writeback delle password?**

  > **R:** Funziona per gli utenti federati e con sincronizzazione di hash della password.
  >
  >
* **D: Il writeback delle password applica i criteri di password del dominio?**

  > **R:** Sì, applica validità, cronologia, complessità, filtri e qualsiasi altra restrizione eventualmente applicata alle password nel dominio locale.
  >
  >
* **D: Il writeback delle password è sicuro?  Come si può essere certi di non essere oggetto di un attacco?**

  > **R:** Sì, il writeback delle password è sicuro. Per altre informazioni sui 4 livelli di sicurezza implementati dal servizio di writeback delle password, vedere la sezione [Password writeback security model](active-directory-passwords-writeback.md#password-writeback-security-model) (Modello di sicurezza del writeback della password) in Funzionamento del writeback delle password.
  >
  >

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - Iniziare a usare la gestione self-service delle password di Azure AD 
* [**Licenze**](active-directory-passwords-licensing.md): configurare le licenze di Azure AD
* [**Dati** ](active-directory-passwords-data.md): informazioni sui dati necessari e su come vengono usati per la gestione delle password
* [**Implementazione**](active-directory-passwords-best-practices.md): pianificare e distribuire agli utenti la reimpostazione password self-service usando le istruzioni disponibili in questo articolo
* [**Personalizzazione**](active-directory-passwords-customize.md): personalizzare l'aspetto dell'esperienza della reimpostazione password self-service per l'azienda.
* [**Reporting** ](active-directory-passwords-reporting.md): verificare se, quando e dove gli utenti accedono alla reimpostazione password self-service
* [**Criteri**](active-directory-passwords-policy.md): comprendere e impostare i criteri password di Azure AD
* [**Writeback delle password**](active-directory-passwords-writeback.md): funzionamento del writeback delle password con la directory locale
* [**Approfondimento tecnico**](active-directory-passwords-how-it-works.md): approfondimento sul funzionamento
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere i problemi comuni con la reimpostazione password self-service
