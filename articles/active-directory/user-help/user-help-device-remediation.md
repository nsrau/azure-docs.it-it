---
title: Cause possibili del messaggio d'errore "Non è possibile accedervi da qui" in Azure Active Directory| Microsoft Docs
description: Risolvere le cause possibili del messaggio d'errore "Non è possibile accedervi da qui".
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: c34b096766b6b7dda26c51559bf58e40d95715e7
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651268"
---
# <a name="potential-reasons-for-the-you-cant-get-there-from-here-error-message"></a>Cause possibili del messaggio d'errore "Non è possibile accedervi da qui"
In fase di accesso ai servizi o alle app Web interne dell'organizzazione potrebbe essere visualizzato il messaggio d'errore **Non è possibile accedervi da qui**. Questo messaggio significa che l'organizzazione ha applicato criteri che impediscono al dispositivo di accedere alle risorse dell'organizzazione. Di seguito sono riportate alcune operazioni che è possibile provare, anche se probabilmente si dovrà contattare il supporto tecnico per risolvere il problema.

## <a name="make-sure-youre-using-a-supported-browser"></a>Assicurarsi di usare un browser supportato
Se viene visualizzato il messaggio d'errore **Non è possibile accedervi da qui**, indicante che si sta tentando di accedere ai siti dell'organizzazione da un browser non supportato, controllare quale browser si sta usando.

![Messaggio d'errore relativo al supporto browser](media/user-help-device-remediation/browser-version.png)

Per risolvere il problema è necessario installare ed eseguire un browser supportato, in base al sistema operativo. Se si usa Windows 10, fra i browser supportati ci sono Microsoft Edge, Internet Explorer e Google Chrome. Se si usa un sistema operativo diverso, controllare l'elenco completo dei [browser supportati](../conditional-access/technical-reference.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Assicurarsi che il sistema operativo in uso sia supportato
Assicurarsi che la versione del sistema operativo in uso sia supportata. Fra le versioni supportate ci sono le seguenti:

- **Windows Client**. Windows 7 o versione successiva.

- **Windows Server**. Windows Server 2008 R2 o versione successiva.

- **macOS**. macOS X o versione successiva

- **Android e iOS**. Versione più recente dei sistemi operativi per dispositivi mobili Android e iOS

Per risolvere il problema è necessario installare ed eseguire un sistema operativo supportato.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Assicurarsi che il dispositivo sia collegato alla rete
Se viene visualizzato il messaggio d'errore **Non è possibile accedervi da qui**, indicante che il dispositivo non è conforme ai criteri di accesso dell'organizzazione, assicurarsi che il dispositivo sia aggiunto alla rete dell'organizzazione.

![Messaggio d'errore relativo alla presenza o meno sulla rete](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Per controllare se il dispositivo è aggiunto alla rete
1. Eseguire l'accesso a Windows usando l'account aziendale o dell'istituto di istruzione. Ad esempio: alain@contoso.com.

2. Connettersi alla rete dell'organizzazione tramite una rete privata virtuale (VPN) o DirectAccess.

3. Dopo aver stabilito la connessione, premere il **tasto WINDOWS+L** per bloccare il dispositivo.

4. Sbloccare il dispositivo usando l'account aziendale o dell'istituto di istruzione e quindi tentare di nuovo di accedere all'app o al servizio problematico.

    Se viene visualizzato di nuovo il messaggio d'errore **Non è possibile accedervi da qui**, selezionare il collegamento **Altri dettagli** e quindi contattare il supporto tecnico con tutte le informazioni a portata di mano.

### <a name="to-join-your-device-to-your-network"></a>Per aggiungere il dispositivo alla rete
Se il dispositivo non è stato aggiunto alla rete dell'organizzazione, è possibile fare una delle due cose seguenti:

- **Aggiungere un dispositivo aziendale**. Aggiungere il dispositivo Windows 10 di proprietà dell'azienda alla rete dell'organizzazione in modo da poter accedere a risorse potenzialmente limitate. Per altre informazioni e istruzioni dettagliate, vedere [Aggiungere il dispositivo aziendale alla rete dell'organizzazione](user-help-join-device-on-network.md).

- **Registrare un dispositivo personale per l'uso aziendale**. Registrare il dispositivo personale, in genere un telefono o tablet, nella rete dell'organizzazione. Dopo aver registrato il dispositivo, esso potrà accedere alle risorse limitate della propria organizzazione. Per altre informazioni e istruzioni dettagliate, vedere [Registrare il dispositivo personale nella rete dell'organizzazione](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Passaggi successivi
- [Cos'è il portale App personali?](active-directory-saas-access-panel-introduction.md)

- [Accedere con il telefono, non con la password](microsoft-authenticator-app-phone-signin-faq.md)