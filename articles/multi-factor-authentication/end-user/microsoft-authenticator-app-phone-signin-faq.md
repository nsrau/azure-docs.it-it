---
title: 'Accesso tramite telefono e Microsoft Authenticator: account di Azure e Microsoft | Microsoft Docs'
description: "Uso del telefono per accedere all'account Microsoft anziché digitare la password. Questo articolo risponde alle domande frequenti su questa funzionalità."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 86cf6ba2f2ec86a609b4ec60cc143b07d9e542b3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Accedere con il telefono, non con la password

L'app Microsoft Authenticator consente di proteggere gli account eseguendo la verifica in due passaggi dopo aver inserito la password. Ma non tutti sanno che può sostituire completamente la password dell'account personale di Microsoft.

Questa funzionalità è disponibile su dispositivi iOS e Android e funziona con gli account personali di Microsoft.

## <a name="how-it-works"></a>Funzionamento

Molti utenti usano l'app Microsoft Authenticator per eseguire la verifica in due passaggi quando si accede all'account personale di Microsoft. Digitare la password, quindi passare all'applicazione per approvare una notifica oppure ottenere un codice di verifica. Con l'accesso tramite telefono è possibile ignorare la password ed eseguire le operazioni di verifica dell'identità sul telefono. Trattandosi di un tipo di verifica in due passaggi, per verificare l'identità è comunque necessario specificare una cosa che si conosce e una cosa di cui si è in possesso. Il telefono è la cosa si possiede, mentre il PIN del telefono o la chiave biometrica è la cosa che si conosce.

## <a name="how-to-get-started"></a>Attività iniziali

Per accedere all'account personale di Microsoft con il telefono, seguire questi passaggi:

1. Abilitare l'accesso all'account personale tramite telefono.

  - Se l'utente non ha ancora l'app Microsoft Authenticator, installarla e aggiungere l'account personale di Microsoft seguendo i passaggi sulla [pagina di Microsoft Authenticator](microsoft-authenticator-app-how-to.md). Gli account appena aggiunti vengono abilitati automaticamente, quindi è possibile iniziare.

  - Se si usa già Microsoft Authenticator per la verifica in due passaggi, selezionare l'account personale dalla home page dell'app e scegliere **Abilita l'accesso tramite telefono** dal menu a discesa.

  >[!NOTE]
  >Per proteggere l'account è necessario un PIN o un blocco biometrico sul dispositivo. Se il telefono resta sbloccato, l'app chiede all'utente di impostare un blocco prima di abilitare l'accesso tramite telefono.

3. La maggior parte delle pagine in cui in genere l'utente inserisce la password dell'account di Microsoft ha un collegamento che dice **Usa un'app**. Selezionare il collegamento per accedere tramite il telefono.

4. Microsoft invia una notifica al telefono cellulare dell'utente. Approvare la notifica per accedere al proprio account.   

## <a name="faq"></a>Domande frequenti

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>In che modo l'accesso tramite telefono è più sicuro dell'accesso tramite password?  

Oggi la maggior parte delle persone accede a siti Web o alle app usando un nome utente e una password.  Sfortunatamente, le password spesso vengono smarrite, rubate o individuate da pirati informatici. Quando si configura l'app Microsoft Authenticator per accedere, sul telefono viene generata una chiave in grado di sbloccare l'account. Microsoft protegge la chiave con il PIN o con la biometria già in uso sul telefono.  Quando si accede tramite telefono, questa chiave viene usata per dimostrare la propria identità in modo sicuro grazie a due fattori: il telefono stesso e la capacità di sbloccarlo. 

La chiave usata è simile alle chiavi usate nelle specifiche di Windows Hello e FIDO Alliance UAF. I biodati vengono usati solo per proteggere la chiave in locale e non vengono mai inviati o archiviati nel cloud. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Quando posso usare il telefono per sostituire la password e quando invece devo ancora usare la password?  

Oggi, la funzione di accesso tramite telefono funziona solo con le app e i servizi Web che si basano sugli account di Microsoft, con le app di iOS o Android che usano un account personale di Microsoft e le app su Windows 10 che usano un account personale di Microsoft. Quando si accede a uno di questi siti Web o app, nella pagina in cui in genere si inserisce la password è presente un collegamento che afferma **Usa un'app**. 

L'accesso tramite telefono non può essere usato per sbloccare un computer Windows, una XBOX o una versione desktop delle app di Microsoft, ad esempio le app di Office al momento.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>Questa funzione sostituisce la verifica in due passaggi? È consigliabile disattivarla?   

In alcuni casi sì. Microsoft vuole espandere l'ambito dell'accesso tramite telefono, ma per ora, in alcuni casi, all'interno dell'ecosistema di Microsoft questa funzione non è supportata. In questi casi, viene usata ancora la verifica in due passaggi per eseguire un accesso sicuro. Per questo motivo, no, non è consigliabile disattivare la verifica in due passaggi per l'account.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>Se la verifica in due passaggi resta attiva per il mio account, devo approvare due notifiche?

No. L'accesso all'account di Microsoft tramite telefono viene considerato una verifica in due passaggi. Invece di digitare la password e approvare una notifica, l'utente dimostra la propria identità sapendo come sbloccare il telefono e quindi approva la notifica. Microsoft non invierà una seconda notifica da approvare.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Se perdo il telefono o non ce l'ho con me, come eseguo l'accesso all'account?  

È sempre possibile fare clic su **Use a password instead** (Usa la password) nella pagina di accesso per tornare a usare la password. Tenere presente che, se si usa la verifica in due passaggi, è comunque necessario un secondo metodo per verificare l'accesso. Per questo motivo si consiglia di assicurarsi di avere informazioni di sicurezza aggiuntive e aggiornate nel proprio account. È possibile gestire le informazioni di sicurezza all'indirizzo https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Come smetto di usare questa funzionalità e torno all'inserimento della password?

Fare clic su **Use a password instead** (Usa la password) quando si accede. Il sistema ricorda la scelta più recente e la ripropone per impostazione predefinita al successivo accesso. Se si desidera tornare indietro all'accesso tramite telefono, fare clic su **Usa un'app**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>È possibile usare l'app per accedere a tutti gli account personali con Microsoft?   
Questa funzionalità è disponibile solo per gli account personali di Microsoft al momento. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>È possibile accedere al mio PC con il telefono?  
Per il PC, è consigliabile accedere con Windows Hello in Windows 10 usando l'immagine, l'impronta digitale o un PIN.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>È possibile accedere con il mio Windows Phone?  
Fino a questo momento, Microsoft non ha sviluppato questa funzionalità per Microsoft Authenticator sul Windows Phone. 

## <a name="next-steps"></a>Passaggi successivi
Se non è stata scaricata l'app Microsoft Authenticator, cercarla. L'app è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), mentre l'accesso tramite telefono è disponibile sull'app Microsoft Authenticator per [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

In caso di domande sull'app in generale, consultare le [domande frequenti su Microsoft Authenticator](microsoft-authenticator-app-faq.md)
