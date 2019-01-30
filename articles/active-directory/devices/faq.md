---
title: Domande frequenti sulla gestione dei dispositivi di Azure Active Directory | Microsoft Docs
description: Domande frequenti sulla gestione dei dispositivi di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2010
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 916de2de6cdc19bfa1e3967661d40693d4be1e99
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852389"
---
# <a name="azure-active-directory-device-management-faq"></a>Domande frequenti sulla gestione dei dispositivi di Azure Active Directory

**D: Di recente è stato registrato un dispositivo. Perché non viene visualizzato nelle informazioni dell'utente all'interno del portale di Azure? Oppure perché il proprietario del dispositivo è contrassegnato come N/D per i dispositivi ibridi aggiunti ad Azure Active Directory (Azure AD)?**

**R:** I dispositivi ibridi di Windows 10 aggiunti ad Azure AD non vengono visualizzati tra i **Dispositivi utente**.
Usare la visualizzazione **Tutti i dispositivi** nel portale di Azure. In alternativa, è possibile usare un cmdlet [Get-MsolDevice](https://docs.microsoft.com/en-us/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) di PowerShell.

Sotto **Dispositivi utente** vengono elencati solo i dispositivi seguenti:

- Tutti i dispositivi personali non aggiunti ad Azure AD ibrido. 
- Tutti i dispositivi non Windows 10 o Windows Server 2016.
- Tutti i dispositivi non Windows. 

--- 

**D: Come è possibile conoscere lo stato di registrazione del dispositivo client?**

**R:** Passare a **Tutti i dispositivi** nel portale di Azure. Cercare il dispositivo usando l'ID dispositivo. Controllare il valore nella colonna Tipo di join. In alcuni casi, è possibile che il dispositivo sia stato reimpostato o che ne sia stata ricreata l'immagine. È quindi importante controllare anche lo stato di registrazione del dispositivo all'interno del dispositivo:

- Per i dispositivi Windows 10 e Windows Server 2016 o versioni successive, eseguire `dsregcmd.exe /status`.
- Per le versioni del sistema operativo di livello inferiore, eseguire `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

**D: Viene visualizzata la registrazione del dispositivo nelle informazioni dell'utente all'interno del portale di Azure. E lo stato viene visualizzato come registrato nel dispositivo. La configurazione è corretta per usare l'accesso condizionale?**

**R:** Lo stato di join del dispositivo, indicato dall'**ID dispositivo**, deve corrispondere a quello in Azure AD e soddisfare eventuali criteri di valutazione per l'accesso condizionale. Per altre informazioni, vedere [Richiedere dispositivi gestiti per l'accesso alle app cloud con l'accesso condizionale](../conditional-access/require-managed-devices.md).

---

**D: È stato eliminato il dispositivo nel portale di Azure oppure tramite Windows PowerShell. Ma lo stato locale nel dispositivo risulta ancora registrato.**

**R:** Questa operazione viene eseguita per prassi. Il dispositivo non ha accesso alle risorse nel cloud. 

Se si vuole registrare nuovamente il dispositivo, è necessario farlo manualmente da quest'ultimo. 

Per cancellare lo stato di join dai dispositivi Windows 10 e Windows Server 2016 aggiunti a un dominio Active Directory Domain Services locale, eseguire i passaggi seguenti:

1.  Aprire il prompt dei comandi come amministratore.

2.  Immettere `dsregcmd.exe /debug /leave`.

3.  Disconnettersi e accedere per attivare l'attività pianificata che registra nuovamente il dispositivo con Azure AD. 

Per le versioni del sistema operativo Windows di livello inferiore aggiunte a un dominio locale di Active Directory Domain Services, eseguire i passaggi seguenti:

1.  Aprire il prompt dei comandi come amministratore.
2.  Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**D: Perché vengono visualizzate voci di dispositivo duplicate nel portale di Azure?**

**R:**

-   Per i dispositivi Windows 10 e Windows Server 2016, i tentativi ripetuti di rimozione o aggiunta dello stesso dispositivo potrebbero dare luogo a elementi duplicati. 

-   Ciascun utente Windows che usa l'opzione **Aggiungi account aziendale o dell'istituto di istruzione** creerà un nuovo record di dispositivo con lo stesso nome.

-   Per le versioni di sistemi operativi Windows di livello inferiore aggiunte a un dominio Azure Directory locale, la registrazione automatica crea un nuovo record di dispositivo con lo stesso nome per ogni utente del dominio che accede al dispositivo. 

-   Un computer aggiunto ad Azure AD cancellato, reinstallato e aggiunto nuovamente con lo stesso nome viene visualizzato con un record diverso ma con lo stesso nome dispositivo.

---

**D: Perché un utente può comunque accedere alle risorse da un dispositivo disattivato nel portale di Azure?**

**R:** Per applicare una revoca è necessaria un'ora.

>[!NOTE] 
>Per quanto riguarda i dispositivi registrati, è consigliabile cancellarli per assicurare che gli utenti non possano accedere alle relative risorse. Per ulteriori informazioni, vedere [Informazioni sulla gestione dei dispositivi](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD

**D: In che modo è possibile separare un dispositivo aggiunto da Azure AD in locale sul dispositivo?**

**R:** 
- Per la gestione ibrida dei dispositivi aggiunti ad Azure AD, assicurarsi di disattivare la registrazione automatica. In questo modo l'attività pianificata non registra nuovamente il dispositivo. A questo punto, aprire il prompt dei comandi come amministratore e immettere `dsregcmd.exe /debug /leave`. O eseguire questo comando come uno script in più dispositivi per separare in blocco.

- Per i dispositivi puri aggiunti ad Azure AD, assicurarsi di avere un account amministratore locale offline o crearne uno. Non è possibile accedere con le credenziali di un utente Azure AD. A questo punto, passare a **Impostazioni** > **Account** > **Accedi all'azienda o all'istituto di istruzione**. Selezionare l'account e quindi **Disconnetti**. Seguire le istruzioni e, quando richiesto, fornire le credenziali di amministratore locale. Riavviare il dispositivo per completare il processo di separazione.

---

**D: Gli utenti possono accedere ai dispositivi aggiunti ad Azure AD che sono stati eliminati o disabilitati in Azure AD?**

**R:** Sì. Windows dispone di un nome utente memorizzato nella cache e della funzionalità di password che consente agli utenti che hanno effettuato l'accesso in precedenza di accedere al desktop rapidamente anche senza la connettività di rete. 

Quando un dispositivo viene eliminato o disabilitato in Azure AD, ciò non è noto al dispositivo Windows. Pertanto, gli utenti registrati in precedenza continuano ad accedere al desktop con il nome utente memorizzato nella cache e la password. Tuttavia, poiché il dispositivo è stato eliminato o disabilitato, gli utenti non possono accedere alle risorse protette dall'accesso condizionale basato sul dispositivo. 

Gli utenti che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

**D: Gli utenti disabilitati o eliminati possono accedere ai dispositivi aggiunti ad Azure AD?**

**R:** Sì, ma solo per un periodo di tempo limitato. Quando un utente viene eliminato o disabilitato in Azure AD, ciò non è noto immediatamente al dispositivo Windows. Pertanto, gli utenti registrati in precedenza possono accedere al desktop con il nome utente memorizzato nella cache e la password. 

In genere, il dispositivo è a conoscenza dello stato utente in meno di quattro ore. Quindi Windows blocca l'accesso degli utenti sul desktop. Quando l'utente viene eliminato o disabilitato in Azure AD, tutti i relativi token vengono revocati. Pertanto, non può accedere alle risorse. 

Gli utenti eliminati o disabilitati che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

**D: Gli utenti non possono eseguire la ricerca delle stampanti da dispositivi aggiunti ad Azure AD. In che modo è possibile abilitare la stampa da tali dispositivi?**

**R:** Per la distribuzione delle stampanti per i dispositivi aggiunti ad Azure AD, vedere [Deploy Windows Server Hybrid Cloud Print with Pre-Authentication (Distribuire stampa di cloud ibrido su server Windows con autenticazione preliminare)](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). È necessario un Server Windows locale per la distribuzione della stampa di cloud ibrido. Il servizio di stampa basato sul cloud non è attualmente disponibile. 

---

**D: Come è possibile connettersi a un dispositivo aggiunto a un'istanza remota di Azure AD?**

**R:** Vedere [(Connect to remote Azure Active Directory-joined PC) Connetti al computer remoto aggiunto ad Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

**D: Perché viene visualizzato un messaggio che avvisa dell'*impossibilità di raggiungere un determinato dispositivo*?**

**R:** Sono state configurate alcune regole di accesso condizionale per richiedere un stato specifico del dispositivo? Se il dispositivo non soddisfa i criteri, gli utenti vengono bloccati e viene visualizzato tale messaggio. Valutare i criteri di accesso condizionale. Assicurarsi che il dispositivo soddisfi i criteri in modo da evitare il messaggio.

---

**D: Perché alcuni utenti visualizzano un messaggio prompt di Azure Multi-Factor Authentication nei dispositivi aggiunti ad Azure AD?**

**R:** Un utente può aggiungere o registrare un dispositivo con Azure AD avvalendosi di Multi-Factor Authentication. Il dispositivo stesso diventa quindi un secondo fattore attendibile per tale utente. Ogni volta che lo stesso utente esegue l'accesso al dispositivo e accede a un'applicazione, Azure AD considera tale dispositivo come secondo fattore. Ciò consente di accedere facilmente alle applicazioni senza altri messaggi prompt da parte di Multi-Factor Authentication. 

Questo comportamento non è applicabile ad altri utenti che accedono al dispositivo. Pertanto, tutti gli altri utenti che accedono a tale dispositivo ricevono una richiesta di verifica di Multi-Factor Authentication. A questo punto è possibile accedere le applicazioni che richiedono l'autenticazione a più fattori.

---

**D: Perché viene visualizzato un messaggio di *nome utente o password non corretti* per un dispositivo appena aggiunto ad Azure AD?**

**R:** I motivi più comuni per questo scenario sono i seguenti:

- Le credenziali dell'utente non sono più valide.

- Il computer non riesce a comunicare con Azure Active Directory. Verificare la presenza di eventuali problemi di connettività di rete.

- Gli accessi federati richiedono un server federativo con supporto per endpoint WS-Trust abilitati e accessibili. 

- Autenticazione pass-through abilitata. Pertanto, la password temporanea deve essere modificata quando si accede.

---

**D: Perché viene visualizzata una finestra di dialogo di *errore* quando si tenta di aggiungere il PC ad Azure AD?**

**R:** La finestra di errore viene visualizzata quando si registra Azure Active Directory con Intune. Verificare che all'utente che cerca di aggiungere il PC ad Azure AD sia assegnata la licenza di Intune corretta. Per altre informazioni, vedere [Set up enrollment for Windows devices (Configurare la registrazione in blocco per i dispositivi Windows)](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**D: Perché il tentativo di aggiungere un PC ad Azure AD non è andato a buon fine ma non vengono comunque visualizzate informazioni sull'errore?**

**R:** È probabile che l'utente abbia effettuato l'accesso al dispositivo tramite l'account amministratore predefinito locale. Creare un account locale diverso prima di usare l'aggiunta ad Azure Active Directory per completare la configurazione. 

---

## <a name="hybrid-azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD ibrido

**D: Dov'è possibile trovare informazioni sulla risoluzione dei problemi per poter diagnosticare errori che si sono verificati durante l'aggiunta ad Azure AD ibrido?**

**R:** Per informazioni sulla risoluzione dei problemi, vedere gli articoli seguenti:

- [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)

- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)
 
**D: Perché viene visualizzato un record di registrazione in Azure AD duplicato per un dispositivo Windows 10 aggiunto ad Azure AD ibrido nell'elenco dei dispositivi Azure AD?**

**R:** Quando gli utenti aggiungono i propri account alle app in un dispositivo aggiunto al dominio, potrebbe essere visualizzata la richiesta **Aggiungi account a Windows?** Se si immette **Sì** quando viene richiesto, si registra il dispositivo in Azure AD. Il tipo di attendibilità è contrassegnato come registrato in Azure AD. Quando si abilita l'aggiunta ad Azure AD ibrido nell'organizzazione, il dispositivo viene aggiunto anche ad Azure AD ibrido. Vengono visualizzati due stati dei dispositivi per lo stesso dispositivo. 

Lo stato di aggiunto ad Azure AD ibrido ha la precedenza rispetto allo stato di registrato in Azure AD. Il dispositivo è quindi considerato aggiunto ad Azure AD ibrido per ogni processo di autenticazione e di valutazione di accesso condizionale. È possibile eliminare in modo sicuro il record di dispositivo registrato in Azure AD dal portale di Azure AD. Informazioni su come [evitare o eliminare il doppio stato nel computer Windows 10](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 

---

## <a name="azure-ad-register-faq"></a>Domande frequenti sulla registrazione in Azure AD

**D: È possibile registrare dispositivi Android o iOS in modalità BYOD?**

**R:** Sì, ma solo con il servizio di registrazione del dispositivo di Azure e per clienti che usano ambienti ibridi. Non è supportato con il servizio di registrazione dei dispositivi locali in Active Directory Federation Services (ADFS).

**D: Come si registra un dispositivo macOS?**

**R:** Eseguire questa procedura:

1.  [Creare i criteri di conformità del dispositivo](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definire i criteri di accesso condizionale per i dispositivi macOS](../active-directory-conditional-access-azure-portal.md) 

**Osservazioni:**

- Gli utenti inclusi nei criteri di accesso condizionale necessitano di una [versione di Office supportata per macOS](../conditional-access/technical-reference.md#client-apps-condition) per accedere alle risorse. 

- Durante il primo tentativo di accesso, agli utenti viene richiesto di registrare il dispositivo tramite il portale aziendale.

---


**D: quali sono i certificati MS-Organization-P2P-Access presenti sui dispositivi Windows 10?**

**R:** I certificati MS-Organization-P2P-Access vengono rilasciati da Azure AD sia sui dispositivi aggiunti ad Azure AD che su quelli aggiunti ad Azure AD ibrido. Tali certificati vengono usati per abilitare l'attendibilità tra i dispositivi nello stesso tenant per scenari di desktop remoti. Un certificato viene rilasciato al dispositivo e un altro viene rilasciato all'utente. Il certificato del dispositivo è presente in `Local Computer\Personal\Certificates` ed è valido per un giorno. Tale certificato viene rinnovato (generando un nuovo certificato) se il dispositivo è ancora attivo in Azure AD. Il certificato utente è presente in `Current User\Personal\Certificates` e anch'esso è valido per un giorno, ma viene rilasciato su richiesta quando un utente tenta di eseguire una sessione di accesso da remoto a un altro dispositivo aggiunto ad Azure AD. Non viene rinnovato alla scadenza. Entrambi questi certificati vengono emessi usando il certificato MS-Organization-P2P-Access presente in `Local Computer\AAD Token Issuer\Certificates`. Tale certificato è emesso da Azure AD durante la registrazione del dispositivo. 

---

**D: Perché vengono visualizzati più certificati scaduti emessi da MS-Organization-P2P-Access nei dispositivi Windows 10? Come è possibile eliminarli?**

**R:** Si è verificato un problema in Windows 10, versione 1709 e precedenti, in cui i certificati MS-Organization-P2P-Access scaduti sono rimasti presenti nell'archivio del computer a causa di problemi di crittografia. Gli utenti possono riscontrare problemi con la connettività di rete se si usano client VPN (ad esempio Cisco AnyConnect) che non possono gestire il numero elevato di certificati scaduti. Questo problema è stato risolto nella versione 1803 di Windows 10 nella quale vengono eliminati automaticamente i certificati MS-Organization-P2P-Access scaduti. È possibile risolvere questo problema aggiornando i dispositivi alla versione 1803 di Windows 10. Se non si è in grado di aggiornare, è possibile eliminare questi certificati senza effetti negativi.  
