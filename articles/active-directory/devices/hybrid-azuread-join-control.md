---
title: Controllare l'aggiunta dei dispositivi ad Azure AD ibrido | Microsoft Docs
description: Informazioni su come controllare l'aggiunta dei dispositivi ad Azure AD ibrido in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93afc6f748ca9f464261c59e037a603ab6113bf8
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518168"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>Controllare l'aggiunta dei dispositivi ad Azure AD ibrido

L'aggiunta ad Azure Active Directory (Azure AD) ibrido è un processo che consente di registrare automaticamente i dispositivi aggiunti a un dominio locale in Azure AD. In alcuni casi non si vuole che tutti i dispositivi vengano registrati automaticamente. Questo avviene, ad esempio, durante l'implementazione iniziale, per verificare che tutto funzioni come previsto.

Questo articolo offre indicazioni su come controllare l'aggiunta dei dispositivi ad Azure AD ibrido. 


## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con gli argomento seguenti:

-  [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)
 
-  [Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido](hybrid-azuread-join-plan.md)

-  [Configurare l'aggiunta ad Azure Active Directory ibrido per i domini gestiti](hybrid-azuread-join-managed-domains.md) o [Configurare l'aggiunta ad Azure Active Directory ibrido per i domini federati](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Controllare i dispositivi Windows correnti

Per i dispositivi che eseguono il sistema operativo desktop Windows, è supportata la versione 1607 (Aggiornamento dell'anniversario di Windows 10) o successiva. Come procedura consigliata, eseguire l'aggiornamento alla versione più recente di Windows 10.

Tutti i dispositivi Windows correnti vengono registrati automaticamente in Azure AD all'avvio del dispositivo o all'accesso dell'utente. È possibile controllare questo comportamento con un oggetto Criteri di gruppo (GPO) o System Center Configuration Manager.

Per controllare i dispositivi Windows correnti, è necessario: 


1.  **Per tutti i dispositivi**: disabilitare la registrazione automatica dei dispositivi.
2.  **Per i dispositivi selezionati**: abilitare la registrazione automatica dei dispositivi.

Dopo aver verificato che tutto funzioni come previsto, si è pronti per abilitare nuovamente la registrazione automatica dei dispositivi per tutti i dispositivi.



### <a name="group-policy-object"></a>Oggetto Criteri di gruppo 

È possibile controllare il comportamento di registrazione dei dispositivi distribuendo l'oggetto Criteri di gruppo seguente: **Registra i computer aggiunti a un dominio come dispositivi**.

Per impostare l'oggetto Criteri di gruppo:

1.  Aprire **Server Manager** e quindi passare a **Strumenti** > **Gestione Criteri di gruppo**.

2.  Passare al nodo corrispondente al dominio in cui si vuole disabilitare o abilitare la registrazione automatica.

3.  Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e quindi scegliere **Nuovo**.

4.  Immettere un nome (ad esempio, **Aggiunta ad Azure AD ibrido**) per l'oggetto Criteri di gruppo. 

5.  Selezionare **OK**.

6.  Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.

7.  Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Registrazione dispositivo**. 

8.  Fare clic con il pulsante destro del mouse su **Registra i computer aggiunti a un dominio come dispositivi** e quindi scegliere **Modifica**.

    > [!NOTE] 
    > Questo modello di Criteri di gruppo è stato rinominato dalle versioni precedenti della console Gestione Criteri di gruppo. Se si usa una versione precedente della console, passare a **configurazione Computer** > **criteri** > **modelli amministrativi**  >  **i componenti di Windows** > **servizio registrazione dispositivo** > **Register dominio aggiunto al computer come dispositivo**. 

9.  Selezionare una delle impostazioni seguenti e quindi selezionare **Applica**:

    - **Disabilitato**: per impedire la registrazione automatica dei dispositivi.
    - **Abilitato**: per abilitare la registrazione automatica dei dispositivi.

10. Selezionare **OK**.

È necessario collegare l'oggetto Criteri di gruppo a una posizione di propria scelta. Ad esempio, per impostare questi criteri per tutti i dispositivi correnti aggiunti a un dominio all'interno dell'organizzazione, collegare l'oggetto Criteri di gruppo al dominio. Per eseguire una distribuzione controllata, impostare questi criteri sui dispositivi correnti di Windows aggiunti a un dominio che appartengono a un'unità organizzativa o a un gruppo di sicurezza.

### <a name="configuration-manager-controlled-deployment"></a>Distribuzione controllata di Gestione configurazione 

È possibile controllare il comportamento di registrazione dei dispositivi correnti configurando l'impostazione client seguente: **Registra automaticamente i nuovi dispositivi Windows 10 aggiunti al dominio con Azure Active Directory**.

Per configurare l'impostazione client:

1.  Aprire **Configuration Manager**, selezionare **amministrazione**, quindi passare a **impostazioni Client**.

2.  Aprire le proprietà per **impostazioni Client predefinite** e selezionare **servizi Cloud**.

3.  In **Impostazioni del dispositivo** selezionare una delle impostazioni seguenti per **Registra automaticamente i nuovi dispositivi Windows 10 aggiunti al dominio con Azure Active Directory**:

    - **No**: per impedire la registrazione automatica dei dispositivi.
    - **Sì**: per abilitare la registrazione automatica dei dispositivi.

4.  Selezionare **OK**.

È necessario collegare questa impostazione client a una posizione di propria scelta. Ad esempio, per configurare questa impostazione client per tutti i dispositivi correnti di Windows nell'organizzazione, collegare l'impostazione client al dominio. Per eseguire una distribuzione controllata, è possibile configurare questa impostazione client sui dispositivi correnti di Windows aggiunti a un dominio che appartengono a un'unità organizzativa o a un gruppo di sicurezza.

> [!Important]
> Anche se la configurazione precedente interessa dispositivi Windows 10 aggiunti a un dominio esistenti, è possibile che i dispositivi appena aggiunti al dominio tentino ancora di completare l'aggiunta ad Azure AD ibrido a causa di potenziali ritardi nell'applicazione delle impostazioni di Criteri di gruppo o di Gestione configurazione nei dispositivi. 
>
> Per evitare questo problema, è consigliabile creare una nuova immagine Sysprep (usata come esempio per un metodo di provisioning). Crearla da un dispositivo che non è mai stato aggiunto in precedenza ad Azure AD ibrido e a cui è già stata applicata l'impostazione di Criteri di gruppo o l'impostazione client di Configuration Manager. È anche necessario usare la nuova immagine per il provisioning di nuovi computer che si aggiungono al dominio dell'organizzazione. 

## <a name="control-windows-down-level-devices"></a>Controllare i dispositivi Windows di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, è necessario scaricare e installare il pacchetto di Windows Installer (con estensione msi) dalla pagina [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554) (Microsoft Workplace Join per computer non Windows 10) dell'Area download.

È possibile distribuire il pacchetto usando un sistema di distribuzione del software come [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro quiet. Configuration Manager Current Branch offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto dell'utente e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo con Azure AD con le credenziali dell'utente dopo l'autenticazione con Azure AD.

Per controllare la registrazione del dispositivo è necessario distribuire il pacchetto Windows Installer solo a un gruppo selezionato di dispositivi Windows di livello inferiore. Se si è verificato che tutto funzioni come previsto, si è pronti per l'implementazione del pacchetto su tutti i dispositivi di livello inferiore.


## <a name="next-steps"></a>Passaggi successivi

* [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)



