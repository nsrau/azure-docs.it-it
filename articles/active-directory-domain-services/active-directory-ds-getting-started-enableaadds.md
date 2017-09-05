---
title: 'Azure Active Directory Domain Services: abilitare Azure Active Directory Domain Services | Microsoft Docs'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure classico
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c659da59-f4b5-4edd-b702-1727a8ccb36f
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: e3f8dd4b820da8a14437aa313e4296dc17111674
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-classic-portal"></a>Abilitare Azure Active Directory Domain Services tramite il portale di Azure classico

> [!IMPORTANT]
> L'esperienza del portale di Azure classico per abilitare Azure AD Domain Services, illustrata in questo articolo, verrà presto rimossa. Per le nuove distribuzioni, [**usare invece la nuova esperienza del portale di Azure (anteprima)**](active-directory-ds-getting-started.md).
>

## <a name="task-3-enable-azure-active-directory-domain-services"></a>Attività 3: Abilitare Azure Active Directory Domain Services
In questa attività si abiliterà Azure Active Directory Domain Services (Azure AD DS) per la directory seguendo questa procedura:

1. Passare al [portale di Azure classico](https://manage.windowsazure.com).
2. Selezionare il pulsante **Active Directory** nel riquadro sinistro.
3. Selezionare il tenant (directory) di Azure Active Directory (Azure AD) per il quale si desidera abilitare Azure AD DS.

    ![Selezionare una directory di Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Nella pagina **anteprima directory** fare clic sulla scheda **Configura**.

    ![Scheda Configura della directory](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. In **servizi di dominio**, modificare l'opzione **Abilita Servizi di dominio per la directory** su **Sì**.  
    Nella pagina verranno visualizzate altre opzioni di configurazione di Azure Active Directory Domain Services.

    ![Abilita servizi di dominio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > Quando si abilita Azure Active Directory Domain Services per il tenant, Azure AD genera e archivia gli hash delle credenziali Kerberos e NTLM necessari per l'autenticazione degli utenti.
   >
   >
6. Specificare il **Nome di dominio DNS di Servizi di dominio**.

   * Il nome di dominio predefinito della directory (con suffisso **.onmicrosoft.com**) viene selezionato per impostazione predefinita.

   * Nell'elenco sono contenuti tutti i domini che sono stati configurati per la directory di Azure AD, inclusi i domini verificati e non verificati configurati nella scheda **Domini**.

   * È inoltre possibile immettere un nome di dominio personalizzato. In questo esempio, il nome di dominio personalizzato è *contoso100.com*.

     > [!WARNING]
     > Il prefisso del nome del dominio specificato (ad esempio, *contoso100* nel nome di dominio *contoso100.com*) può contenere massimo 15 caratteri. Non è possibile creare un dominio di Azure Active Directory Domain Services con un prefisso contenente più di 15 caratteri.
     >
     >
7. Assicurarsi che il nome di dominio DNS scelto per il dominio gestito non esista già nella rete virtuale. In particolare, verificare se:

   * È già presente un dominio con lo stesso nome di dominio DNS nella rete virtuale.

   * La rete virtuale selezionata ha una connessione VPN alla rete locale, dove è presente un dominio con lo stesso nome di dominio DNS.

   * Esiste un servizio cloud con lo stesso nome della rete virtuale.
8. Selezionare una rete virtuale in cui si desidera la disponibilità di Azure Active Directory Domain Services. Selezionare la rete virtuale e la subnet dedicata create nell'elenco a discesa **Connetti Servizi di dominio a questa rete virtuale**. Tenere inoltre in considerazione quanto segue:

   * Assicurarsi che la rete virtuale specificata appartenga a un'area di Azure supportata da Azure Active Directory Domain Services. Per conoscere le aree di Azure in cui è disponibile Azure Active Directory Domain Services, vedere i [servizi di Azure per area](https://azure.microsoft.com/regions/#services/).

   * Le reti virtuali appartenenti a un'area in cui Azure Active Directory Domain Services non è supportato non vengono visualizzate nell'elenco a discesa.

   * Usare una subnet dedicata nella rete virtuale per Azure Active Directory Domain Services. *Non* selezionare la subnet del gateway. Vedere le [considerazioni sulla rete](active-directory-ds-networking.md).

   * Analogamente, le reti virtuali create con Azure Resource Manager non vengono visualizzate nell'elenco a discesa. Le reti virtuali basate su Resource Manager attualmente non sono supportate da Azure Active Directory Domain Services.
9. Per abilitare Azure Active Directory Domain Services, fare clic su **Salva** nel riquadro attività nella parte inferiore della pagina.
    * Quando si attiva Azure Active Directory Domain Services per la directory, la pagina mostra lo stato *In sospeso*.

        ![Finestra Abilita Domain Services](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

        > [!NOTE]
        > Azure Active Directory Domain Services garantisce un'elevata disponibilità per il dominio gestito. Dopo avere abilitato Azure Active Directory Domain Services, vengono visualizzati uno alla volta gli indirizzi IP per cui è disponibile Domain Services nella rete virtuale. Il secondo indirizzo IP viene visualizzato poco dopo il primo, non appena il servizio abilita la disponibilità elevata per il dominio. Al termine della configurazione e attivazione della disponibilità elevata per il dominio, nella sezione **Servizi di dominio** della scheda **Configura** dovrebbero comparire due indirizzi IP.
        >
        >
    * Dopo circa 20-30 minuti, il primo indirizzo IP in cui è disponibile Domain Services nella rete virtuale viene visualizzato nel campo **Indirizzo IP** nella pagina **Configura**.

        ![La finestra di Domain Service visualizza il primo indirizzo IP di cui è stato eseguito il provisioning](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
    * Quando la disponibilità elevata è operativa per il dominio, nella pagina sono visualizzati due indirizzi IP. Il dominio gestito è disponibile nella rete virtuale selezionata a questi due indirizzi IP.

10. Prendere nota degli indirizzi IP per poter aggiornare le impostazioni DNS per la rete virtuale. Questo passaggio consente alle macchine virtuali nella rete virtuale di connettersi al dominio per operazioni quali l'aggiunta al dominio.

    ![La finestra di Domain Services mostra entrambi gli IP di cui è stato eseguito il provisioning](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> A seconda delle dimensioni del tenant di Azure AD (ad esempio, numero di utenti e gruppi), la sincronizzazione con il dominio gestito richiede tempo. Questo processo di sincronizzazione avviene in background. Per tenant di grandi dimensioni con decine di migliaia di oggetti, possono essere necessari anche un paio di giorni perché tutti gli utenti, le appartenenze ai gruppi e le credenziali siano sincronizzati.
>
>

## <a name="next-step"></a>Passaggio successivo
[Attività 4: Aggiornare le impostazioni DNS per la rete virtuale di Azure](active-directory-ds-getting-started-update-dns.md)

