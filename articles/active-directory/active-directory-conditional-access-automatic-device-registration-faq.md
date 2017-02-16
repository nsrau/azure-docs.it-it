---
title: Domande frequenti sulla registrazione automatica dei dispositivi | Microsoft Docs
description: Domande frequenti sulla registrazione automatica dei dispositivi con Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 918c2b096b9b6d935c8d5bc2588a978cdf8878cf
ms.openlocfilehash: 0c4f1489893f464eee1f76e94f2b35650f06e44e


---
# <a name="automatic-device-registration-faq"></a>Domande frequenti sulla registrazione automatica dei dispositivi

**D: Di recente è stato registrato un dispositivo. Perché non viene visualizzato nelle informazioni dell'utente all'interno del portale di Azure?**

**R:** I dispositivi Windows 10 che appartengono a un dominio con la registrazione automatica dei dispositivi non vengono visualizzati nelle informazioni UTENTE.
È necessario usare PowerShell per visualizzare tutti i dispositivi. 

Nelle informazioni UTENTE vengono elencati solo i dispositivi seguenti:

- Tutti i dispositivi personali non aggiunti a un dominio aziendale 
- Tutti i dispositivi non Windows 10/Windows Server 2016 
- Tutti i dispositivi non Windows 

---

**D: Perché non vengono visualizzati tutti i dispositivi registrati in Azure Active Directory nel portale di Azure?** 

**R:** Attualmente non è possibile visualizzare tutti i dispositivi registrati nel portale di Azure. È possibile usare Azure PowerShell per trovare tutti i dispositivi. Per ulteriori informazioni, vedere il cmdlet [Get-MsolDevice](https://docs.microsoft.com/en-us/powershell/msonline/v1/get-msoldevice).

--- 

**D: Come è possibile conoscere lo stato di registrazione del dispositivo client?**

**R:** Lo stato di registrazione del dispositivo dipende da:

- Tipo di dispositivo
- Modalità di registrazione 
- Eventuali altri dettagli associati. 
 

---

**D: Perché un dispositivo eliminato nel portale di Azure o tramite PowerShell viene comunque elencato come registrato?**

**A:** Si tratta di un comportamento previsto da progettazione. Il dispositivo non avrà accesso alle risorse nel cloud. Se si desidera registrare nuovamente il dispositivo, è necessario farlo manualmente da quest'ultimo. 

Per i dispositivi Windows 10 e Windows Server 2016 aggiunti a un dominio AD locale:

1.  Aprire il prompt dei comandi come amministratore.

2.  Digitare **dsregcmd.exe /leave**.

3.  Digitare **dsregcmd.exe**.

Per altre piattaforme Windows aggiunte a un dominio AD locale:

1.  Aprire il prompt dei comandi come amministratore.
2.  Digitare `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Digitare `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**D: Perché vengono visualizzate voci di dispositivo duplicate nel portale di Azure?**

**R:**

-   Per i dispositivi Windows 10 e Windows Server 2016, se vengono effettuati tentativi ripetuti di rimozione o aggiunta del medesimo dispositivo, potrebbero essere visualizzate voci duplicate. 

-   Ciascun utente Windows che usa l'opzione Aggiungi account aziendale o dell'istituto di istruzione creerà un nuovo record di dispositivo con lo stesso nome.

-   Per le altre piattaforme Windows aggiunte a domini AD locali che usano la registrazione automatica verrà creato un nuovo record di dispositivo con lo stesso nome per ciascun utente di dominio che accede al dispositivo. 

-   Un computer AADJ cancellato, reinstallato e aggiunto nuovamente con lo stesso nome verrà visualizzato con un record diverso ma con lo stesso nome dispositivo.

---

**D: Perché un utente può comunque accedere alle risorse da un dispositivo disattivato nel portale di Azure?**

**R:** Potrebbe volerci fino a un'ora per applicare una revoca.

>[!Note] 
>Se un dispositivo viene perso, è consigliabile cancellarlo per assicurare che gli utenti non possano accedervi. Per altre informazioni, vedere , vedere [Registrare i dispositivi per la gestione in Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**D: Perché viene visualizzato un messaggio che avvisa dell'impossibilità di raggiungere un determinato dispositivo?**

**R:** Se sono state configurate determinate regole di accesso condizionale per richiedere uno stato specifico del dispositivo e il dispositivo in questione non soddisfa i criteri, gli utenti vengono bloccati con questo messaggio. Esaminare le regole e assicurarsi che il dispositivo soddisfi i criteri per evitare di incorrere in questo messaggio.

---


**D: Il record del dispositivo è presente nelle informazioni UTENTE nel portale di Azure e viene indicato come registrato nel client. La configurazione è corretta per l'uso dell'accesso condizionale?**

**R:** Il record (deviceID) e lo stato del dispositivo nel portale di Azure devono corrispondere al client e soddisfare eventuali criteri di valutazione per l'accesso condizionale. Per altre informazioni, vedere [Introduzione a Registrazione dispositivo Azure Active Directory](active-directory-conditional-access-device-registration-overview.md).

---

**D: Perché viene visualizzato un messaggio di nome utente o password non corretti per un dispositivo appena aggiunto ad Azure AD?**

**R:** Di seguito vengono elencati alcuni motivi comuni per questo scenario:

1.  Le credenziali dell'utente non sono più valide.

2.  Il computer non riesce a comunicare con Azure Active Directory. Verificare la presenza di eventuali problemi di connettività di rete.

3.  Non sono stati soddisfatti i prerequisiti di aggiunta ad Azure AD. Assicurarsi di aver seguito i passaggi indicati in [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md).  

4.  Gli accessi federati richiedono un server di federazione con supporto per endpoint attivi WS-Trust. 

---

**D: Perché viene visualizzata la finestra di dialogo "Oops… an error occurred!" (Si è verificato un errore) quando si tenta di aggiungere un PC?**

**R:** La finestra viene visualizzata quando si registra Azure Active Directory con Intune. Per altri dettagli, vedere [Configurare la gestione dei dispositivi Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**D: Perché il tentativo di aggiunta di un PC non è andato a buon fine ma non vengono comunque visualizzate informazioni sull'errore?**

**R:** È probabile che l'utente abbia effettuato l'accesso al dispositivo con l'account amministratore predefinito. Creare un account locale diverso prima di usare l'aggiunta ad Azure Active Directory per completare la configurazione. 

---

**D: Dove è possibile trovare informazioni sulla risoluzione dei problemi legati alla registrazione automatica del dispositivo?**

**R:** Per informazioni sulla risoluzione dei problemi, vedere:

1. [Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio Azure AD per client di livello inferiore di Windows 10 e Windows Server 2016](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows.md)

2. [Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio Azure AD per client di livello inferiore di Windows](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy.md)
 
---




<!--HONumber=Feb17_HO1-->


