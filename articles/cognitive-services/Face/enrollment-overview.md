---
title: Panoramica della registrazione all'API Viso
titleSuffix: Azure Cognitive Services
description: Informazioni sul processo di registrazione degli utenti al servizio di riconoscimento volto Viso.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: ac5106aa661cb2baea31ee15d57e9c6fac8c7192
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350331"
---
# <a name="face-api-enrollment"></a>Registrazione all'API Viso

Per usare l'API Viso di Servizi cognitivi per la verifica o l'identificazione facciale, è necessario registrare i visi in una classe **LargePersonGroup**. Questo approfondimento illustra le procedure consigliate per raccogliere il consenso significativo degli utenti, oltre alla logica di esempio per creare registrazioni di alta qualità che ottimizzino l'accuratezza del riconoscimento.  

## <a name="meaningful-consent"></a>Consenso significativo 

Uno degli scopi principali di un'applicazione di registrazione per il riconoscimento facciale è offrire agli utenti l'opportunità di dare il consenso per l'uso delle immagini del loro viso per scopi specifici, ad esempio l'accesso al luogo di lavoro. Poiché con le tecnologie di riconoscimento facciale la percezione comune è che vengano raccolti dati personali sensibili, è particolarmente importante chiedere il consenso in modo trasparente e rispettoso. Il consenso è significativo per gli utenti quando consente loro di prendere la decisione che ritengono più appropriata.   

In base una ricerca condotta da Microsoft tra gli utenti, ai principi di intelligenza artificiale responsabile di Microsoft e a una [ricerca esterna](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf), è stato rilevato che il consenso diventa significativo quando offre quanto segue agli utenti che si registrano per la tecnologia:

* Consapevolezza: gli utenti non dovranno avere alcun dubbio quando ricevono la richiesta di fornire il loro modello facciale o le foto della registrazione. 
* Comprensione: gli utenti dovranno essere in grado di descrivere accuratamente in parole proprie cosa viene chiesto di fare, da chi, a quale fine e con quali garanzie. 
* Libertà di scelta: gli utenti non dovranno sentirsi costretti o manipolati quando scelgono se dare o meno il consenso per registrarsi al riconoscimento facciale. 
* Controllo: gli utenti dovranno avere la possibilità di revocare il loro consenso ed eliminare i loro dati in qualsiasi momento. 

Questa sezione offre indicazioni per lo sviluppo di un'applicazione di registrazione per il riconoscimento facciale. Le indicazioni sono state sviluppate un base a una ricerca condotta da Microsoft tra gli utenti nel contesto della registrazione di individui al riconoscimento facciale per l'accesso agli edifici. Pertanto, queste raccomandazioni potrebbero non essere applicabili a tutte le soluzioni di riconoscimento facciale. L'uso responsabile dell'API Viso dipende fortemente dal contesto specifico in cui viene integrata, quindi l'applicazione di queste raccomandazioni secondo priorità dovrà essere adattata allo scenario in uso. 

> [!NOTE]
> È responsabilità dell'utente allineare l'applicazione di registrazione ai requisiti legali applicabili nella giurisdizione in cui opera e adeguare di conseguenza tutte le procedure di raccolta e trattamento dei dati.

## <a name="application-development"></a>Sviluppo di applicazioni 

Prima di progettare un flusso di registrazione, valutare in che modo l'applicazione da sviluppare potrà rispettare le promesse fatte agli utenti in merito alla protezione dei loro dati. Le raccomandazioni seguenti possono risultare utili per creare un'esperienza di registrazione che includa approcci responsabili alla protezione dei dati personali e alla gestione della privacy degli utenti, oltre che per assicurarsi che l'applicazione sia accessibile a tutti.  

|Category | Consigli |
|---|---|
|Hardware | Considerare la qualità della fotocamera del dispositivo di registrazione. |
|Funzionalità raccomandate per la registrazione | Includere un passaggio di accesso con autenticazione a più fattori.</br></br>Collegare le informazioni dell'utente, come un alias o un numero di identificazione, all'ID del loro modello facciale dell'API Viso, ovvero l'ID persona. Questo abbinamento è necessario per recuperare e gestire la registrazione di un utente. Nota: l'ID persona deve essere trattato come segreto nell'applicazione.</br></br>Configurare un processo automatizzato per eliminare tutti i dati della registrazione, tra cui i modelli facciali e le foto delle persone che non sono più utenti della tecnologia di riconoscimento facciale, ad esempio gli ex dipendenti.</br></br>Evitare la registrazione automatica, perché non offre all'utente le garanzie di consapevolezza, comprensione, libertà di scelta o controllo raccomandate per ottenere il consenso. </br></br>Chiedere agli utenti l'autorizzazione per salvare le immagini usate per la registrazione. Questa opzione è utile nel caso di aggiornamento del modello, perché saranno necessarie nuove foto per ripetere la registrazione nel nuovo modello all'incirca ogni 10 mesi. Se le immagini originali non vengono salvate, gli utenti dovranno ripetere il processo di registrazione dall'inizio.</br></br>Offrire agli utenti la possibilità di rifiutare esplicitamente l'archiviazione di foto nel sistema. Per rendere la scelta più comprensibile, è possibile aggiungere una seconda schermata di richiesta di consenso per il salvataggio delle foto della registrazione. </br></br>Se le foto vengono salvate, creare un processo automatizzato per ripetere la registrazione quando è disponibile un aggiornamento del modello. Gli utenti che hanno acconsentito a salvare le foto della registrazione non dovranno registrarsi di nuovo. </br></br>Creare una funzionalità dell'app che consenta agli amministratori designati di ignorare determinati filtri di qualità se un utente ha problemi a registrarsi. |
|Sicurezza | Per Servizi cognitivi vengono applicate [procedure consigliate](../cognitive-services-virtual-networks.md?tabs=portal) per la crittografia dei dati inattivi degli utenti e per quelli in transito. Di seguito sono riportate altre procedure che possono aiutare a rispettare le promesse di sicurezza fatte agli utenti durante l'esperienza di registrazione. </br></br>Adottare misure di sicurezza per garantire che nessuno abbia accesso all'ID persona in qualsiasi momento durante la registrazione. Nota: l'ID persona deve essere trattato come segreto nel sistema di registrazione. </br></br>Usare il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) con Servizi cognitivi. </br></br>Usare l'autenticazione basata su token e/o le firme di accesso condiviso piuttosto che chiavi e segreti per l'accesso a risorse come database. Usando token di richiesta o di firma di accesso condiviso, è possibile concedere un accesso limitato ai dati senza compromettere le chiavi dell'account, oltre a specificare una data di scadenza del token. </br></br>Non archiviare mai segreti, chiavi o password nell'app. |
|Privacy dell'utente |Offrire un'ampia gamma di opzioni di registrazione per soddisfare i requisiti di livelli di privacy diversi. Non obbligare le persone a usare i loro dispositivi personali per eseguire la registrazione in un sistema di riconoscimento facciale. </br></br>Consentire agli utenti di ripetere la registrazione, revocare il consenso ed eliminare i dati dall'applicazione di registrazione, in qualsiasi momento e per qualsiasi motivo. |
|Accessibilità |Seguire gli standard di accessibilità, ad esempio [ADA](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) o [W3C](https://www.w3.org/TR/WCAG21/), per assicurare che l'applicazione sia utilizzabile da persone con difficoltà visive o di movimento. |

## <a name="next-steps"></a>Passaggi successivi  

Seguire la guida [Creare un'app di registrazione](build-enrollment-app.md) per iniziare con un'app di esempio. Quindi personalizzarla o scriverne una nuova in base ai requisiti del prodotto.