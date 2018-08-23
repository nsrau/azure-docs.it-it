---
title: Come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: d49b5404f1a2b4ac7fa4cc170ccc010a28bf98a2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146872"
---
# <a name="how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Come controllare l'aggiunta dei dispositivi all'identità ibrida di Azure AD

L'aggiunta all'identità ibrida di Azure AD è un processo che consente di registrare automaticamente i dispositivi aggiunti a un dominio locale con Azure AD. In alcuni casi non si vuole che tutti i dispositivi vengano registrati automaticamente. Questo avviene ad esempio durante l'implementazione iniziale, per verificare che tutto funzioni come previsto.

Questo articolo offre indicazioni su come controllare l'aggiunta dei dispositivi all'identità ibrida di Azure AD. 


## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con:

-  [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)
 
-  [Come pianificare l'implementazione dell'aggiunta all'identità ibrida di Azure Active Directory](hybrid-azuread-join-plan.md)

-  Configurare l'aggiunta all'identità ibrida di Azure Active Directory per i [domini gestiti](hybrid-azuread-join-managed-domains.md) o i [domini federati](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Controllare i dispositivi Windows correnti

Per i dispositivi che eseguono il sistema operativo desktop Windows, è supportata la versione 1607 (Aggiornamento dell'anniversario di Windows 10) o successiva. Come procedura consigliata, eseguire l'aggiornamento alla versione più recente di Windows 10.

Tutti i dispositivi Windows correnti vengono registrati automaticamente con Azure AD all'avvio del dispositivo o all'accesso dell'utente. È possibile controllare questo comportamento con un oggetto Criteri di gruppo (GPO) o System Center Configuration Manager.

Per controllare i dispositivi Windows correnti, è necessario: 

1.  **Per tutti i dispositivi**: disabilitare la registrazione automatica dei dispositivi.
2.  **Per i dispositivi selezionati**: abilitare la registrazione automatica dei dispositivi.

Se si è verificato che tutto funzioni come previsto, si è pronti per abilitare nuovamente la registrazione automatica dei dispositivi per tutti i dispositivi.



## <a name="group-policy-object"></a>Oggetto Criteri di gruppo 

È possibile controllare il comportamento di registrazione dei dispositivi distribuendo l'oggetto Criteri di gruppo seguente: **Registra i computer aggiunti a un dominio come dispositivi**

**Per impostare l'oggetto Criteri di gruppo**:

1.  Aprire **Server Manager** e quindi passare a **Strumenti\> Gestione Criteri di gruppo**.

2.  Passare al nodo corrispondente al dominio in cui si vuole disabilitare/abilitare la registrazione automatica.

3.  Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e quindi scegliere **Nuovo**.

4.  Digitare un nome (ad esempio, *Aggiunta ad Azure AD ibrido*) per l'oggetto Criteri di gruppo. 

5.  Fare clic su **OK**.

6.  Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.

7.  Passare a **Configurazione computer \> Criteri \> Modelli amministrativi \> Componenti di Windows \> Registrazione del dispositivo**. 

8.  Fare clic con il pulsante destro del mouse su **Registra i computer aggiunti a un dominio come dispositivi** e quindi scegliere **Modifica**.

    > [!NOTE] 
    > Questo modello di Criteri di gruppo è stato rinominato rispetto alle versioni precedenti della console Gestione Criteri di gruppo. Se si usa una versione precedente della console, accedere a **Configurazione Computer \> Criteri \> Modelli amministrativi \> Componenti di Windows \> Aggiunta all'area di lavoro\> Automatically workplace join client computers (Aggiunta automatica di computer client all'area di lavoro)**. 

9.  Selezionare una delle impostazioni seguenti e quindi fare clic su **Applica**:

    - **Disabilitato**: per impedire la registrazione automatica del dispositivo
    - **Abilitato**: per abilitare la registrazione automatica del dispositivo

10. Fare clic su **OK**.

È necessario collegare l'oggetto Criteri di gruppo a una posizione di propria scelta. Ad esempio, per impostare questi criteri per tutti i dispositivi correnti aggiunti a un dominio all'interno dell'organizzazione, collegare l'oggetto Criteri di gruppo al dominio. Per eseguire una distribuzione controllata, impostare questi criteri sui dispositivi correnti di Windows aggiunti a un dominio che appartengono a un'unità organizzativa o a un gruppo di sicurezza.

### <a name="configuration-manager-controlled-deployment"></a>Distribuzione controllata di Gestione configurazione 

È possibile controllare il comportamento di registrazione dei dispositivi correnti configurando le impostazioni client seguenti: **Automatically register new Windows 10 domain joined devices with azure Active Directory** (Registrare automaticamente nuovi dispositivi aggiunti a un dominio di Windows 10 con Azure Active Directory)


**Per eseguire l'impostazione client**:

1.  Aprire **Gestione configurazione** e quindi andare a **Servizi cloud**.

2.  Sotto **Impostazioni del dispositivo** selezionare una delle impostazioni seguenti per **Registrare automaticamente nuovi dispositivi aggiunti a un dominio di Windows 10 con Azure Active Directory**:

    - **No**: per impedire la registrazione automatica del dispositivo
    - **Sì**: per abilitare la registrazione automatica del dispositivo


3.  Fare clic su **OK**.
    

È necessario collegare questa impostazione client a una posizione di propria scelta. Ad esempio, per configurare questa impostazione client per tutti i dispositivi correnti di Windows nell'organizzazione, collegare l'impostazione client al dominio. Per eseguire una distribuzione controllata, è possibile configurare questa impostazione client sui dispositivi correnti di Windows aggiunti a un dominio che appartengono a un'unità organizzativa o a un gruppo di sicurezza.

> [!Important]
> Mentre la configurazione precedente interessa dispositivi Windows 10 aggiunti al dominio esistenti, è possibile che i dispositivi appena aggiunti al dominio tentino ancora di completare l'aggiunta ad Azure AD ibrido a causa di potenziali ritardi nell'applicazione effettiva dei criteri di gruppo o delle impostazioni di Gestione configurazione nel dispositivo Windows 10 appena aggiunto al dominio. Per evitare questo problema, è consigliabile creare una nuova immagine sysprep (usata come esempio per un metodo di provisioning) da un dispositivo mai aggiunto ad Azure AD ibrido e al quale sia già stata applicata la precedente impostazione di criteri di gruppo o l'impostazione client di Gestione configurazione. È anche necessario usare la nuova immagine per il provisioning di nuovi computer che si aggiungono al dominio dell'organizzazione. 

## <a name="control-windows-down-level-devices"></a>Controllare i dispositivi Windows di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, è necessario scaricare e installare il pacchetto di Windows Installer (con estensione msi) dalla pagina [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554) (Microsoft Workplace Join per computer non Windows 10) dell'Area download.

È possibile distribuire il pacchetto usando un sistema di distribuzione software come System Center Configuration Manager. Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro quiet. Current Branch di [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto dell'utente e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo con Azure AD con le credenziali dell'utente dopo l'autenticazione con Azure AD.


Per controllare la registrazione del dispositivo è necessario distribuire il pacchetto Windows Installer solo a un gruppo selezionato di dispositivi Windows di livello inferiore. Se si è verificato che tutto funzioni come previsto, si è pronti per l'implementazione del pacchetto su tutti i dispositivi di livello inferiore.


## <a name="next-steps"></a>Passaggi successivi

* [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)



