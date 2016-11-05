---
title: Configurazione della registrazione automatica per i dispositivi appartenenti a un dominio di Windows 8.1| Microsoft Docs
description: " Procedura di configurazione dei criteri di gruppo per i dispositivi appartenenti a un dominio di Windows 8.1 per la registrazione automatica in Azure AD. "
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: Markvi

---
# <a name="configure-automatic-device-registration-for-windows-8.1-domain-joined-devices"></a>Configurazione della registrazione automatica per i dispositivi appartenenti a un dominio di Windows 8.1
È possibile usare un oggetto Criteri di gruppo di Active Directory per configurare i dispositivi appartenenti a un dominio di Windows 8.1 per la registrazione automatica in Azure AD. Per configurare Criteri di gruppo, è necessario avere almeno un computer Windows Server 2012 R2 o Windows 8.1 aggiunto a un dominio con la funzionalità Gestione Criteri di gruppo installata. Dopo aver abilitato Criteri di gruppo per il dominio, qualsiasi utente di dominio che accede al computer verrà automaticamente registrato con un oggetto dispositivo in Azure AD. in cui sarà presente un oggetto dispositivo per ogni utente registrato del dispositivo fisico. Leggere i prerequisiti elencati in Registrazione automatica dei dispositivi con Azure Active Directory per dispositivi appartenenti a un dominio di Windows e assicurarsi che siano soddisfatti.

> [!NOTE]
> Per le istruzioni più recenti su come configurare la registrazione automatica dei dispositivi, vedere [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md).
> 
> 

## <a name="configure-the-group-policy-for-your-windows-8.1-domain-joined-devices"></a>Configurare l'oggetto Criteri di gruppo per i dispositivi appartenenti al dominio di Windows 8.1
1. Aprire Server Manager e passare a **Strumenti** > **Gestione Criteri di gruppo**.
2. Da Gestione Criteri di gruppo passare al nodo corrispondente al dominio in cui si vuole abilitare la funzionalità **Aggiunta all'area di lavoro**.
3. Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e scegliere **Nuovo**. Assegnare un nome all'oggetto Criteri di gruppo, ad esempio **Aggiunta automatica all'area di lavoro**. Fare clic su **OK**.
4. Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.
5. Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Aggiunta all'area di lavoro**.
6. Fare clic con il pulsante destro del mouse su Aggiungi automaticamente i computer client all'area di lavoro e scegliere **Modifica**.
7. Selezionare il pulsante di opzione Abilitato e fare clic su Applica. Fare clic su **OK**.
8. È ora possibile collegare l'oggetto Criteri di gruppo a una posizione di propria scelta. Per abilitare questo oggetto per tutti i dispositivi Windows 8.1 appartenenti al dominio nell'organizzazione, collegarlo al dominio.

## <a name="unregistering-your-windows-8.1-domain-joined-devices"></a>Annullare la registrazione dei dispositivi appartenenti a un dominio di Windows 8.1
Per annullare la registrazione dei dispositivi appartenenti a un dominio di Windows 8.1, effettuare le seguenti operazioni: Modificare le impostazioni dei Criteri di gruppo di Aggiunta all'area di lavoro definite nella sezione precedente. Impostare il criterio Aggiungi automaticamente i computer client all'area di lavoro su Disabilitato. In questo modo, i nuovi dispositivi non verranno aggiunti automaticamente all'area di lavoro.

Annullare la registrazione dei computer Windows 8.1 appartenenti al dominio scegliendo una delle due seguenti opzioni:

* Opzione 1: **Annullare la registrazione di un dispositivo Windows 8.1 appartenente al dominio con le impostazioni del PC**
  
  1. Nel dispositivo Windows 8.1 passare a **Impostazioni PC** > **Rete** > **Area di lavoro**
  2. Selezionare **Esci**.
     Questa procedura deve essere ripetuta per ogni utente di dominio che ha eseguito l'accesso al computer ed è stato aggiunto automaticamente all'area di lavoro.
* Opzione 2: Annullare la registrazione di un dispositivo Windows 8.1 appartenente al dominio con uno script
  
  1. Aprire un prompt dei comandi sul computer Windows 8.1 ed eseguire il seguente comando: ` %SystemRoot%\System32\AutoWorkplace.exe leave`

Questo comando deve essere eseguito nel contesto di ogni utente di dominio che ha eseguito l'accesso al computer.

## <a name="event-viewer-&-errors-for-windows-8.1-domain-joined-devices"></a>Visualizzatore eventi ed errori per i dispositivi Windows 8.1 appartenenti a un dominio
Il registro eventi di Windows in un computer Windows 8.1 visualizza i messaggi relativi alla registrazione dei dispositivi. È possibile trovare messaggi per gli eventi riusciti e non. 

Il registro eventi si trova nel Visualizzatore eventi sotto Applicazioni e servizi **Registri** > **Microsoft** > **Windows > Aggiunta all'area di lavoro**.

## <a name="additional-details"></a>Dettagli aggiuntivi
L'oggetto Criteri di gruppo abilita nel sistema un'attività pianificata che viene eseguita nel contesto dell'utente e attivata al momento dell'accesso dell'utente. Una volta completato l'accesso, l'attività registrerà automaticamente l'utente e il dispositivo in Azure AD. L'attività pianificata è disponibile nei dispositivi Windows 8.1 nella libreria dell'Utilità di pianificazione in **Microsoft** > **Windows** > **Aggiunta all'area di lavoro**. L'attività verrà eseguita e registrerà tutti gli utenti di Active Directory che accedono al computer. 

## <a name="additional-topics"></a>Argomenti aggiuntivi
* [Panoramica di Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)
* [Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows 10 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration.md)
* [Configurare la registrazione automatica per i dispositivi Windows 7 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md)

<!--HONumber=Oct16_HO2-->


