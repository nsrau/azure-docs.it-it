---
ms.openlocfilehash: ffc9b09c72ef1bf5180a0d626908d09b6fdd41ca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122795"
---
### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Preparare un'installazione push in un computer Windows

1. Verificare che tra il computer Windows e il server di elaborazione sia attiva la connettività di rete.
1. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve avere diritti di amministratore, locale o di dominio. Usarlo solo per l'installazione push e per gli aggiornamenti dell'agente.

   > [!NOTE]
   > Se non si usa un account di dominio, disabilitare il controllo Accesso utente remoto nel computer locale. Per disabilitare il controllo di accesso utente remoto, nella chiave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System del Registro di sistema, aggiungere un nuovo valore DWORD: **LocalAccountTokenFilterPolicy**. Impostare il valore su **1**. Per eseguire questa operazione da un prompt dei comandi, usare il comando seguente:  
   > `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
1. Nell'istanza di Windows Firewall del computer da proteggere selezionare **Consenti app o funzionalità attraverso Windows Firewall**. Abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows (WMI)**. Per i computer appartenenti a un dominio, è possibile configurare le impostazioni del firewall con un oggetto Criteri di gruppo.

   ![Impostazioni del firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

1. Aggiungere l'account creato in CSPSConfigtool. A tale scopo, seguire questa procedura:

    a. Accedere al server di configurazione.

    b. Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%\home\svsystems\bin.

    c. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

    d. Aggiungere l'account che è stato creato.

    e. Immettere le credenziali usate quando si abilita la replica per un computer.
<!--Update_Description: wording update-->
<!--ms.date: 09/17/2018-->