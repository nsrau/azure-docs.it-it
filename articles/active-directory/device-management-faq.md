---
title: Domande frequenti sulla gestione dei dispositivi di Azure Active Directory | Microsoft Docs
description: Domande frequenti sulla gestione dei dispositivi di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 864f790db48d3d4542ed56a4c7272a198df5bd56
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901137"
---
# <a name="azure-active-directory-device-management-faq"></a>Domande frequenti sulla gestione dei dispositivi di Azure Active Directory



**D: Come si registra un dispositivo macOS?**

**R:** Per registrare un dispositivo macOS:

1.  [Creare i criteri di conformità del dispositivo](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definire i criteri di accesso condizionale per i dispositivi macOS](active-directory-conditional-access-azure-portal.md) 

**Osservazioni:**

- Gli utenti che vengono inclusi nei criteri di accesso condizionale necessitano di una [versione di Office supportata per macOS](active-directory-conditional-access-technical-reference.md#client-apps-condition) per accedere alle risorse. 

- Durante il primo tentativo di accesso, agli utenti viene richiesto di registrare il dispositivo tramite il portale aziendale.

---

**D: Di recente è stato registrato un dispositivo. Perché non viene visualizzato nelle informazioni dell'utente all'interno del portale di Azure?**

**R:** I dispositivi Windows 10 aggiunti ad Azure AD ibrido non vengono visualizzati tra i dispositivi utente.
È necessario usare la visualizzazione Tutti i dispositivi nel portale di Azure. In alternativa, è possibile usare il cmdlet [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) di PowerShell.

Nei dispositivi utente vengono elencati solo i dispositivi seguenti:

- Tutti i dispositivi personali non aggiunti ad Azure AD ibrido 
- Tutti i dispositivi non Windows 10/Windows Server 2016
- Tutti i dispositivi non Windows 

--- 

**D: Come è possibile conoscere lo stato di registrazione del dispositivo client?**

**R:** È possibile usare il portale di Azure, selezionare Tutti i dispositivi e cercare il dispositivo tramite l'ID dispositivo. Controllare il valore nella colonna Tipo di join.

Se si vuole controllare lo stato locale della registrazione del dispositivo da un dispositivo registrato:

- Per i dispositivi Windows 10 e Windows Server 2016 o versioni successive, eseguire dsregcmd.exe /status.
- Per le versioni di sistemi operativi di livello inferiore, eseguire "%programFiles%\Microsoft Workplace Join\autoworkplace.exe"

---

**D: Ho eliminato un dispositivo nel portale di Azure o tramite Windows PowerShell, ma lo stato locale indica che è ancora registrato.**

**A:** Si tratta di un comportamento previsto da progettazione. Il dispositivo non avrà accesso alle risorse nel cloud. 

Se si vuole registrare nuovamente il dispositivo, è necessario farlo manualmente da quest'ultimo. 

Per cancellare lo stato di join dai dispositivi Windows 10 e Windows Server 2016 aggiunti a un dominio AD locale:

1.  Aprire il prompt dei comandi come amministratore.

2.  Digitare `dsregcmd.exe /debug /leave`.

3.  Disconnettersi e accedere per attivare l'attività pianificata che registra nuovamente il dispositivo con Azure AD. 

Per le versioni di sistemi operativi Windows di livello inferiore aggiunti a un dominio AD locale:

1.  Aprire il prompt dei comandi come amministratore.
2.  Digitare `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Digitare `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---
**D: In che modo è possibile separare un dispositivo aggiunto da Azure AD in locale sul dispositivo?**

**R:** 
- Per i dispositivi aggiunti all'identità ibrida di Azure AD, disattivare le registrazione automatica in modo che l'attività pianificata non registri nuovamente il dispositivo. A questo punto, aprire il prompt dei comandi come amministratore e digitare `dsregcmd.exe /debug /leave`. In alternativa, è possibile eseguire il comando come script tra più dispositivi per eseguire la separazione in blocco.

- Per i dispositivi puri aggiunti ad Azure AD, assicurarsi di avere un account amministratore locale offline o crearne uno poiché non sarà possibile accedere con le credenziali di un utente Azure AD. A questo punto, passare a **Impostazioni** > **Account** > **Accedi all'azienda o all'istituto di istruzione**. Selezionare l'account e fare clic su **Disconnetti**. Seguire le istruzioni e, quando richiesto, fornire le credenziali di amministratore locale. Riavviare il dispositivo per completare il processo di separazione.

---

**D: Gli utenti non possono eseguire la ricerca delle stampanti da dispositivi aggiunti ad Azure AD. In che modo è possibile abilitare la stampa a partire dai dispositivi aggiunti ad Azure AD?**

**R:** Per la distribuzione delle stampanti nei dispositivi aggiunti ad Azure AD, vedere [Stampa di cloud ibrido](https://docs.microsoft.com/en-us/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). È necessario un Server Windows locale per la distribuzione della stampa di cloud ibrido. Il servizio di stampa basato sul cloud non è attualmente disponibile. 

---

**D: Perché vengono visualizzate voci di dispositivo duplicate nel portale di Azure?**

**R:**

-   Per i dispositivi Windows 10 e Windows Server 2016, se vengono effettuati tentativi ripetuti di rimozione o aggiunta dello stesso dispositivo, potrebbero essere visualizzate voci duplicate. 

-   Ciascun utente Windows che usa l'opzione Aggiungi account aziendale o dell'istituto di istruzione creerà un nuovo record di dispositivo con lo stesso nome.

-   Per le versioni di sistemi operativi Windows di livello inferiore aggiunte a un dominio AD locale tramite registrazione automatica verrà creato un nuovo record di dispositivo con lo stesso nome per ciascun utente del dominio che accede al dispositivo. 

-   Un computer aggiunto ad Azure AD cancellato, reinstallato e aggiunto nuovamente con lo stesso nome verrà visualizzato con un record diverso ma con lo stesso nome dispositivo.

---

**D: Perché un utente può comunque accedere alle risorse da un dispositivo disattivato nel portale di Azure?**

**R:** Potrebbe volerci fino a un'ora per applicare una revoca.

>[!Note] 
>Per quanto riguarda i dispositivi registrati, è consigliabile cancellarli per assicurare che gli utenti non possano accedere alle relative risorse. Per altre informazioni, vedere , vedere [Registrare i dispositivi per la gestione in Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**D: Perché viene visualizzato un messaggio che avvisa dell'impossibilità di raggiungere un determinato dispositivo?**

**R:** Se sono state configurate determinate regole di accesso condizionale per richiedere uno stato specifico del dispositivo e il dispositivo in questione non soddisfa i criteri, gli utenti vengono bloccati con questo messaggio. Esaminare le regole dei criteri di accesso condizionale e assicurarsi che il dispositivo soddisfi i criteri per evitare che venga visualizzato questo messaggio.

---

**D: Perché alcuni dei miei utenti non ottengono richieste di autenticazione a più fattori sui dispositivi aggiunti ad Azure AD?**

**R:** Se l'utente viene aggiunto o registra un dispositivo con Azure AD usando la Multi-Factor Authentication, il dispositivo stesso diventerà un secondo fattore attendibile per tale utente. Successivamente, ogni volta che lo stesso utente esegue l'accesso al dispositivo e a un'applicazione, Azure AD considera il dispositivo come secondo fattore e consente all'utente di accedere facilmente alle applicazioni senza ulteriori richieste di autenticazione a più fattori. Questa procedura non è applicabile a nessun altro utente che accede a tale dispositivo, in modo tale che a tutti gli altri utenti verrà comunque richiesta un'autenticazione a più fattori prima di accedere alle applicazioni che richiedono l'autenticazione.

---

**D: Il record del dispositivo è presente nelle informazioni UTENTE nel portale di Azure e viene indicato come registrato nel dispositivo. La configurazione è corretta per l'uso dell'accesso condizionale?**

**R:** Lo stato di join del dispositivo, indicato dall'ID dispositivo, deve corrispondere a quello in Azure AD e soddisfare eventuali criteri di valutazione per l'accesso condizionale. Per altre informazioni, vedere [Introduzione a Registrazione dispositivo Azure Active Directory](active-directory-device-registration.md).

---

**D: Perché viene visualizzato un messaggio di nome utente o password non corretti per un dispositivo appena aggiunto ad Azure AD?**

**R:** Di seguito vengono elencati alcuni motivi comuni per questo scenario:

- Le credenziali dell'utente non sono più valide.

- Il computer non riesce a comunicare con Azure Active Directory. Verificare la presenza di eventuali problemi di connettività di rete.

- Non sono stati soddisfatti i prerequisiti di aggiunta ad Azure AD. Assicurarsi di aver seguito i passaggi indicati in [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md).  

- Gli accessi federati richiedono un server di federazione con supporto per endpoint attivi WS-Trust. 

- È stata abilitata l'Autenticazione pass-through e l'utente ha una password temporanea che deve essere modificata quando si esegue l'accesso.

---

**D: Perché viene visualizzata una finestra di dialogo di errore quando si tenta di aggiungere il PC ad Azure AD?**

**R:** La finestra viene visualizzata quando si registra Azure Active Directory con Intune. Verificare che all'utente che sta cercando di aggiungere il PC ad Azure AD sia assegnata la licenza di Intune corretta. Per altri dettagli, vedere [Configurare la gestione dei dispositivi Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**D: Perché il tentativo di aggiunta di un PC non è andato a buon fine ma non vengono comunque visualizzate informazioni sull'errore?**

**R:** È probabile che l'utente abbia effettuato l'accesso al dispositivo con l'account amministratore predefinito locale. Creare un account locale diverso prima di usare l'aggiunta ad Azure Active Directory per completare la configurazione. 

---

**D: Dove reperire le istruzioni per la configurazione della registrazione automatica del dispositivo?**

**R:** Per altre informazioni, vedere [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md).

---

**D: Dove è possibile trovare informazioni sulla risoluzione dei problemi legati alla registrazione automatica del dispositivo?**

**R:** Per informazioni sulla risoluzione dei problemi, vedere:

- [Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio di Azure AD - Windows 10 e Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio di Azure AD per client di livello inferiore di Windows](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 

---

